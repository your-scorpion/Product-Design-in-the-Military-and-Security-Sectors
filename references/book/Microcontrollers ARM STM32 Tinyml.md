# Machine Learning on ARM Microcontrollers (STM32)

Designing a small device with embedded ML means solving two tasks at once: training a model and building reliable hardware around it.

The first practical step is not code, but product definition: name the device, choose controls, position buttons and display, define status indication, pick an encoder or joystick if needed, and design power with minimum noise. If the device must detect magnetic fields indoors, you also need a sensor element. Every selected component should be available at volume, reasonably priced, easy to integrate into PCB design, and well documented.

If your product needs regular supply, vendor choice matters. Most microcontrollers are manufactured in the US, Europe, China, and Japan. In Russian engineering teams, STM, Texas Instruments, Freescale, and NXP are common choices. In practice, local options are often hard to obtain for learning and prototyping. Under sanctions, many teams moved to NXP. In all cases, the same criteria decide: vendor support, development tools, community maturity, and component availability. STM32 checks these boxes reliably.

In many projects you will likely touch STM32F3 at some point. It is not perfect, but it is widely used, has excellent documentation, and comes with convenient code generation tooling. STM also has a broad portfolio, from tiny control tasks (LED drivers) to complex control systems (automotive class).

Recommended STM32 families:

1. High-performance MCU lines: STM32F2, STM32F4, STM32F7, STM32H7.
2. Mainstream lines: STM32G0, STM32G4, STM32F0, STM32F1, STM32F3.
3. Low-power lines: STM32L0, STM32L1, STM32L4, STM32L4+, STM32L5, STM32U5.
4. Newer lines with integrated radio co-processors: STM32WL and STM32WB.

When selecting a microcontroller, focus on core, peripherals, and pin count. Pins are always scarce in real projects, so budget extra. During prototyping it is often safer to use a more capable part, then downshift to a cheaper SKU before mass production. For ML-enabled devices, the lowest-cost part is usually not the right first target. An 80 MHz microcontroller can already run meaningful inference pipelines. A common strategy is releasing two SKUs: with ML and without ML.

A practical migration note: ST application note AN3364 is useful for replacing one STM32 variant with a lower-cost alternative once architecture is stable.

My default tooling stack:

- CubeMX for code generation.
- CubeIDE for loader, debugger, and compiler integration.
- CubeProgrammer for mass flashing compiled binaries.
- CubeMonitor for runtime variable inspection on deployed devices.

---

## Energy Efficiency

If computation happens on-device, power planning must happen early. It helps to separate:

- Instantaneous power (how much wire and regulator capacity you need).
- Average energy consumption over time (battery life and thermal budget).

A kettle may require 2 kW, but only for short intervals. Microcontrollers are similar: long sleep periods with short high-power bursts. You cannot truly eliminate peak instantaneous demand. You can only tame it with hardware design: decoupling capacitors and stable power architecture.

The energy-efficiency principle is simple: do more work for less energy.

Benefits:

- Less heat output.
- Better reliability (less risk of hot spots, solder issues, board discoloration).
- Longer battery life.
- Smaller form factors.
- Higher safety (lower delivered power, lower overheating risk).

Power behavior also affects certification. A typical device has thousands of transistors switching at shared clocks, creating pulse-like current draw and potential EMI problems. Power correction and stable switched supplies are therefore routine, even in simple products like LED bulbs. In serious devices, certified power modules are safer than improvised supplies.

### Low-power operation modes

STM32 gives three core power-saving states:

- `sleep`: fast wake-up via interrupt/event (for example, wake-word trigger).
- `stop`: deep reduction in consumption with high-speed clocks disabled.
- `standby`: deepest sleep, almost everything off, only limited wake sources.

A practical reminder: CPU is often not the main power drain. On Arduino-like systems, display brightness can dominate. Dim the display and sleep earlier, and you often get larger gains than micro-optimizing code.

### Power domains

Typical power domains:

- `VSS` / `VDD`: digital ground and supply.
- `VDDA`: analog supply (PLL, op-amps, comparators).
- `VBAT`: battery-backed domain (RTC and related retention blocks).

In some designs `VDD`/`VDDA` can be controlled separately. If domains are sequenced, analog power is often expected before digital logic.

### Supervisors, PVD, and brownout behavior

Power quality should be monitored by supervisors. Without proper supervision, there can be tiny windows where protection assumptions fail during unstable ramp-up/ramp-down. A supervisor can hold the MCU in reset until power domains are valid, then restart automatically on power collapse.

Another common mechanism is `PVD` (programmable voltage detector), which lets firmware react to voltage drops without immediate reset. Classic appliance behavior comes from this pattern: detect falling supply, trigger interrupt, use hold-up capacitance, save state, resume later.

General hardware rule: you almost never regret adding sufficient decoupling and hold-up capacitance.

### Memory and endurance

On memory trade-offs:

- RAM is fast but not non-volatile.
- EEPROM/Flash is non-volatile but slower and write-cycle limited.

STM32 low-power families may include EEPROM-like options, but most STM32 designs rely on embedded Flash for simplicity and cost. Write endurance is finite (from thousands to tens of thousands of cycles in practical use), so firmware update and logging strategies must account for wear.

Addressing reminder for 32-bit systems:

- 32 bits = 4 bytes per address word.

If you need faster non-volatile memory, F-RAM is an option, but at higher BOM cost.

---

## TinyML

Once hardware is ready, you deploy a trained model. For microcontrollers, the model must be compact enough to run at milliwatt-level budgets.

In cloud-first ML, heavy training/inference runs in data centers and edge devices mostly consume results. TinyML is the opposite decision: run inference locally even without internet connectivity.

Why local inference matters:

- Pacemakers and medical monitors cannot depend on permanent connectivity.
- Fire alarms and safety systems need deterministic local behavior.
- Latency and privacy constraints often rule out round trips.

You do not upload Python scripts directly to the MCU. You train on a workstation, export a model, then deploy a compiled runtime artifact.

### The real workload is data, not training

In most ML projects, up to 80% of effort is data engineering: collection, cleaning, labeling, and validation. Model training for small MCU tasks can take minutes; deployment and integration often take longer and lead directly into MLOps concerns.

A neural network loop can be viewed as:

1. Prediction
2. Error measurement
3. Optimization

A minimal gradient-descent-style intuition without libraries:

```python
import math

w = 2
b = -1

x = [-1, 0, 1, 2, 3, 4]
y = [-3, -1, 1, 3, 5, 7]
my_y = [(w * xi) + b for xi in x]

print("Real Y:", y)
print("Pred Y:", my_y)

total_square_error = sum((t - p) ** 2 for t, p in zip(y, my_y))
rmse = math.sqrt(total_square_error)

print("Loss:", rmse)
```

Neural networks are weighted mathematical graphs. The "neuron" is not a biological analogy in implementation terms; it is just a computation unit with weighted inputs.

The same toy idea in TensorFlow:

```python
import tensorflow as tf
import numpy as np
from tensorflow import keras

model = tf.keras.Sequential([
    keras.layers.Dense(units=1, input_shape=[1])
])

model.compile(
    optimizer=tf.keras.optimizers.SGD(learning_rate=0.01),
    loss='mean_squared_error',
    metrics=['mae', 'mse']
)

input_data = np.array([-1.0, 0.0, 1.0, 2.0, 3.0, 4.0], dtype=float)
target_output = np.array([-3.0, -1.0, 1.0, 3.0, 5.0, 7.0], dtype=float)

history = model.fit(input_data, target_output, epochs=16, validation_split=0.2, verbose=1)
weights, bias = model.layers[0].get_weights()

print("Trained Weight:", weights[0][0])
print("Trained Bias:", bias[0])
```

---

## Choosing an ML Method

For embedded systems, method selection is always constrained by memory, compute, and power.

Common options:

- Regression/classification for classic supervised tasks.
- Reinforcement learning for control tasks (for example, autonomous driving subsystems).
- Ensemble methods (including boosting) when they outperform simpler baselines in tabular settings.
- Deep learning for image/audio tasks where handcrafted features are insufficient.

TensorFlow remains popular primarily because of ecosystem and Python adoption, not because it is always best in speed or quality. Keras offers very fast model prototyping. PyTorch is the main competing ecosystem. For embedded deployment, TensorFlow Lite and TensorFlow Lite for Microcontrollers are practical endpoints.

### Feature extraction for vision

For face or object recognition, the model must learn compact features. Convolutions apply small filters over pixel neighborhoods, preserving structure while reducing dimensionality.

A raw 640x480 frame can exceed what many MCUs can process comfortably in real time, especially with color channels and intermediate tensors. Therefore, edge systems rely on:

- Downscaling
- Sliding windows or region proposals
- Quantization
- Smaller layer counts
- Hardware-aware architectures

`Conv2D` is a core building block for splitting image patterns into reusable local features.

Illustrative images from the original article:

![Feature extraction example](https://your-scorpion.ru/wp-content/uploads/2023/08/Group-47417.png)

![Convolutional processing intuition](https://your-scorpion.ru/wp-content/uploads/2023/08/Group-47420.png)

![Model flow diagram](https://your-scorpion.ru/wp-content/uploads/2023/08/G424.png)

### Practical MNIST example (resized to 36x36)

```python
from keras import models, layers
from keras.datasets import mnist
from keras.utils import to_categorical
import numpy as np

(train_images, train_labels), (test_images, test_labels) = mnist.load_data()

new_image_size = (36, 36)
train_images_resized = np.array([np.resize(image, new_image_size) for image in train_images])
test_images_resized = np.array([np.resize(image, new_image_size) for image in test_images])

network = models.Sequential()
network.add(layers.Dense(512, activation='relu', input_shape=(new_image_size[0] * new_image_size[1],)))
network.add(layers.Dense(10, activation='softmax'))

network.compile(optimizer='rmsprop', loss='categorical_crossentropy', metrics=['accuracy'])

train_images_resized = train_images_resized.reshape((60000, new_image_size[0] * new_image_size[1])).astype('float32') / 255
test_images_resized = test_images_resized.reshape((10000, new_image_size[0] * new_image_size[1])).astype('float32') / 255

train_labels_one_hot = to_categorical(train_labels)
test_labels_one_hot = to_categorical(test_labels)

network.fit(train_images_resized, train_labels_one_hot, epochs=5, batch_size=128)
```

### Wake-word and audio TinyML

Wake-word detection is a classic low-power embedded ML task. Voice assistants start interaction only after a trigger phrase.

For TinyML audio, you usually build your own dataset by recording examples in target acoustic conditions.

Why raw audio is hard on MCUs:

- 16 kHz microphone means 16,000 samples per second.
- Even short clips quickly consume memory and bandwidth.

Standard pipeline:

1. Convert audio waveform to spectral representation (FFT/MFCC).
2. Enhance useful patterns with filtering.
3. Feed spectrogram-like features to compact CNN-style models.

Spectrogram example from the source article:

![Spectrograms for the word "Test"](https://your-scorpion.ru/wp-content/uploads/2023/08/adding_new_icons-1160x987.png)

No-code or low-code tools for quick TinyML iterations:

- [Edge Impulse](https://edgeimpulse.com/)
- [Neuton.ai](https://neuton.ai/)

These tools can estimate on-device memory footprint and inference time after training.

---

## Industrial Anomaly Detection on Microcontrollers

A stronger use case than toy wake-word demos is industrial monitoring:

- Vibration changes in CNC machinery via accelerometers.
- Airflow monitoring in industrial systems.
- Wearable health anomaly detection using accelerometer + gyroscope + ECG.

The article chooses industrial predictive maintenance and references the ToyADMOS dataset.

### K-means baseline for anomaly boundaries

Start with clustering normal behavior states:

```python
import matplotlib.pyplot as plt
import seaborn as sns
import numpy as np
from sklearn.datasets import make_blobs
from sklearn.cluster import KMeans

sns.set()

def generate_and_plot_clusters(num_centers, num_samples_per_center, cluster_std):
    X, y_true = make_blobs(
        n_samples=num_centers * num_samples_per_center,
        centers=num_centers,
        cluster_std=cluster_std,
        random_state=0
    )

    plt.figure(figsize=(8, 6))
    plt.scatter(X[:, 0], X[:, 1], s=70, c=y_true, cmap='viridis')
    plt.title(f'Generated Clusters (centers={num_centers})')
    plt.show()
    return X

num_centers = 3
num_samples_per_center = 140
cluster_std = 0.36

X_train = generate_and_plot_clusters(num_centers, num_samples_per_center, cluster_std)

kmeans = KMeans(n_clusters=num_centers)
kmeans.fit(X_train)
y_kmeans = kmeans.predict(X_train)
```

Then estimate per-cluster distance thresholds (for example, 97th percentile):

```python
percentile_threshold = 97
train_distances = kmeans.transform(X_train)

center_distances = {key: [] for key in range(num_centers)}
for i in range(len(y_kmeans)):
    min_distance = train_distances[i][y_kmeans[i]]
    center_distances[y_kmeans[i]].append(min_distance)

center_percentile_distance = {
    key: np.percentile(center_distances[key], percentile_threshold)
    for key in center_distances.keys()
}

print(center_percentile_distance)
```

Mark points outside each cluster radius as anomalous:

```python
fig, ax = plt.subplots()
colors = []
for i in range(len(X_train)):
    min_distance = train_distances[i][y_kmeans[i]]
    if min_distance > center_percentile_distance[y_kmeans[i]]:
        colors.append(4)
    else:
        colors.append(y_kmeans[i])

ax.scatter(X_train[:, 0], X_train[:, 1], c=colors, s=40, cmap='viridis')
centers = kmeans.cluster_centers_
for i in range(len(centers)):
    circle = plt.Circle(
        (centers[i][0], centers[i][1]),
        center_percentile_distance[i],
        color='black',
        alpha=0.1
    )
    ax.add_artist(circle)
```

This is useful but does not scale cleanly to very high-volume vibration streams.

A practical shift is to learn normal behavior only, then detect deviations from that baseline. This naturally leads to autoencoders: train on normal data, reconstruct inputs, and flag high reconstruction error as anomalous behavior. The same principle underlies denoising and compression variants.

---

## Model Architecture Notes

A compact CNN baseline:

```python
from keras import layers, models

model = models.Sequential()
model.add(layers.Conv2D(32, (3, 3), activation='relu', input_shape=(28, 28, 1)))
model.add(layers.MaxPooling2D((2, 2)))
model.add(layers.Conv2D(64, (3, 3), activation='relu'))
model.add(layers.MaxPooling2D((2, 2)))
model.add(layers.Conv2D(64, (3, 3), activation='relu'))

model.add(layers.Flatten())
model.add(layers.Dense(64, activation='relu'))
model.add(layers.Dense(10, activation='softmax'))
```

`Conv2D` and `MaxPooling2D` output 3D tensors: height x width x channels. Deeper networks increase compression and abstraction, while channel count is controlled by the first argument in `Conv2D`.

Training pipeline on MNIST:

```python
from keras.datasets import mnist
from keras.utils import to_categorical

(train_images, train_labels), (test_images, test_labels) = mnist.load_data()

train_images = train_images.reshape((60000, 28, 28, 1)).astype('float32') / 255
test_images = test_images.reshape((10000, 28, 28, 1)).astype('float32') / 255

train_labels = to_categorical(train_labels)
test_labels = to_categorical(test_labels)

model.compile(optimizer='rmsprop', loss='categorical_crossentropy', metrics=['accuracy'])
model.fit(train_images, train_labels, epochs=5, batch_size=64)
```

For edge deployment, reducing parameter count is critical. Quantization can convert float weights to integer formats, shrinking model size and improving runtime efficiency. If that is still insufficient, move to TensorFlow Lite for Microcontrollers.

---

## Final Practical Takeaways

1. Start from hardware constraints, not model ambition.
2. Power architecture and EMC strategy are first-class design decisions.
3. Build data pipelines early; data engineering dominates schedule.
4. For TinyML, aim for robust, compact models with predictable latency.
5. Use quantization and architecture simplification before switching hardware.
6. For anomaly detection, baseline behavior modeling is often more realistic than trying to enumerate all anomalies.
7. Treat tool ecosystem choice (TensorFlow, Keras, PyTorch) as a deployment decision, not ideology.

TinyML works best when electrical engineering, firmware, and ML are designed as one system.
