# Machine Learning on ARM Microcontrollers (STM32)
### Building a small device that runs a model on milliwatts

*Maksim Tcvetkov · Product Design and Security Lead*

So, we've decided to build some small device with ML built in. We need to train a model and to create the physical device itself. The first step is to give the device a name. Then settle on the controls — lay out the buttons, the display, the status indication, the encoder-joystick, and a power system that minimizes interference. If the device has to sense a magnetic field in a room, you'll need a sensing element. Everything listed has to be available on the market in volume, not be expensive, not complicate the board design, and be beautifully documented.

If you need regular supply, you have to know the manufacturers. Microcontrollers are mostly made in America, Europe, China, and Japan, and there are a great many of them. In Russia, STM, Texas Instruments, Freescale, and NXP are popular. Let me spell out the situation with Russian microcontrollers up front: of the domestic ones, the good ones aren't available for educational purposes — at least, I never managed to get hold of a MIK32 AMUR as a private individual. And the Baikal isn't a microcontroller, it's a microprocessor. Under sanctions many have moved to NXP, because choosing a microcontroller demands developer support, availability of development tools, a community, and availability of components. STM satisfies those requirements too.

You'll almost certainly end up working with the STM32F3, even though it isn't perfect. But turn up at any expo and STM hand out their evaluation boards for free, and they have detailed documentation and convenient code generators. STM32 also release a microcontroller for every micro-task — dozens of models, from one for driving an LED to one for running a car. There's not much to complain about: the ARM architecture and its representative the STM32 are perfectly good, all of it 32-bit. I recommend sticking to the MCU models. The STM32 family is currently split into several series:

1. High-performance microcontrollers — STM32F2, STM32F4, STM32F7, and STM32H7, listed in ascending order of performance and clock speed.
2. Mainstream microcontrollers — STM32G0 and STM32G4, along with STM32F0, STM32F1, and STM32F3, of which the STM32G4 and STM32F3 series are optimized for mixed-signal processing. The STM32F078CB, for instance, can run without an external crystal.
3. Low-power microcontrollers — the STM32L0, STM32L1, STM32L4, STM32L4+, STM32L5, and STM32U5 series. A very good choice when the device runs on a battery. If a CR2032 has to be changed once every two years instead of every couple of months, that's a good device; on an ER14505 the device can run for years. Bearing in mind that lithium batteries are a bit like rechargeables, POWER FLASH especially — but they're sold in specialist shops and are usually notably more expensive than the ordinary saline or alkaline cells from any supermarket.
4. The fairly new series with an integrated radio co-processor — STM32WL and STM32WB.

When choosing a microcontroller, pay particular attention to the core, the peripherals, and the number of pins. Pins are always in short supply, so it's better to take some in reserve; the surplus will almost always come in handy for wiring up little LEDs to the client's whims. And after the project is approved, when preparing for industrial production, you pick a cheap microcontroller without the frills. In the case of machine learning we're hardly going to choose the cheapest components — on a microcontroller with 80 MHz on board you can perfectly well carry out complex computations. The presence of a neural net raises the device's cost by itself, which means it isn't the most budget segment. You can ship two versions of the device: with ML and without. After prototyping, you can easily swap the expensive microcontroller for a more affordable one; AN3364 helps you understand which microcontrollers can be replaced with cheaper alternatives.

To write the code I use [CubeMX](https://www.st.com/en/development-tools/stm32cubeide.html) as a code generator, CubeIDE for the bootloader, debugger, and compiler, and the program is ready. CubeProgrammer flashes the compiled file onto the controller en masse via the bootloader, and CubeMonitor, for debugging, lets you watch the values of variables on finished devices.

## Power efficiency

Since we'll be computing something on the device itself, we need to worry about power consumption in advance. First, what it means to manage power consumption. Every refrigerator has a "power" rating and, separately, a consumption figure in kilowatt-hours per year — the average power over a span of time. Power answers the question of what wire we need to feed the device. Average power can be explained like this: a kettle needs two kilowatts (which is a great deal), but it switches on a few times a day for a couple of minutes. The same applies to microcontrollers. A device can run in sleep mode with a small average power, but performing some one-off task will demand a large power over a short interval. And, most importantly, we can't lower the instantaneous power. All we can do is put capacitors on the input and output, or apply other circuit-design solutions.

The idea of power efficiency is quite simple: do a lot, spend little. The approach has many upsides — reduced heat output, for one. Nobody likes holding a red-hot device. But even inside the device, if half a watt is given off in a small volume it can lead to sad consequences. A resistor can, if not burn out, then char. If you've seen little dark spots on a board and detached solder, excess heat output may be to blame. Beyond that, high power efficiency helps the device's battery life and reduces its dimensions. If a device eats little electricity, that gives us better safety, since less power needs to be supplied and there's less chance of overheating.

Power efficiency and device certification also go hand in hand. In a typical device thousands of transistors are at work, all drawing current at once. The switch-on signal is distributed at the same frequency, and all the transistors draw a fairly significant current when switching. A controller at 8 MHz, for example, runs at 10 milliamps, and that sharp current draw puts a high load on things — which creates problems with radio interference. Power-factor correctors help solve the problem and raise efficiency, and they're used far more often than it might seem. Even an LED bulb or the SxL90A lidar uses a switch-mode power supply, which means the current is drawn in pulses too. So we protect the device from interference and try not to let the device emit interference, which is needed for certification. It's precisely for these reasons that I rarely use homemade power supplies and instead buy a ready-made certified switch-mode supply.

There are three main modes that let us save power:

- **Sleep mode** — the device wakes from it quickly, via an interrupt or an event. This could be the wake word for a voice speaker, or opening the garage doors with a button on a remote.
- **Stop mode** — all the high-speed clocks are switched on, and consumption drops by an order of magnitude.
- **Standby** — the deepest sleep, in which absolutely everything is off. Only certain interrupts can be received. Needed for long shutdowns.

It's important to understand that the processor is often not the most important component in the power-consumption problem. On the same Arduino, power draw will be affected more by the screen brightness. Lower the brightness and you've as good as optimized power consumption; send it to sleep sooner and you've optimized it a bit more.

Different types of power can be connected to the controller, and each separate power rail is called a domain. If a digital power source is used, that's VSS as ground and minus, VDD as positive supply. These are precisely the ones that create interference, so they have to work in tandem with an oscillator and a core voltage regulator.

Another kind of power is VDDA, the analog supply — PLL, op-amps, and comparators. In some cases VDD and VDDA can be switched off, but on an interrupt VDDA must be supplied earlier than VDD. And the third domain is the battery supply, VBAT, which runs the real-time clock (RTC).

Sometimes the ability to change the voltage is also considered. This is used only for overclocking, not for reducing power consumption — that is, we sharply raise the supply voltage for a short, fast computation. On the STM32F7 I would wake the device as fast as possible at maximum power, do the computation, and send it back to sleep. Monitoring power quality is done via inspectors / supervisors. The analog supply is applied first. Without a power supervisor the device is vulnerable to having its data read for a few microseconds, bypassing the processor's protection. The supervisor forbids the controller from working until all the power domains have entered normal operation. If the power drops, a reboot happens automatically — again, a form of protection. Another way to guard against bad power is the PVD, which monitors power quality without reboots. Take a washing machine as an example: on a power cut it continues the wash from the stage where it was interrupted, meaning the machine tracks when it started being switched off. An interrupt arrives, plus a large capacitor keeps the controller running, so when the power vanishes the microcontroller manages to save a little data. And the general rule: you can never have too many capacitors.

The last important thing on power is non-volatile memory. On the Arduino it's built in and used first and foremost for storing data. It's genuinely non-volatile — without power it can hold data for months or years. An ordinary flash drive is non-volatile too, and yet it sometimes has to be connected to power, or it loses information in large fragments (sectors). The STM32 L series contains EEPROM. But for the most part, in the name of simplifying the clocking system, the STM32 doesn't contain such slow memory — and every block is an extra design cost, so the STM32 uses built-in flash, which is also a kind of EEPROM. It's relatively fast.

RAM is fast but not power-efficient, and EEPROM is slow memory but power-efficient. Memory is the filling of cells in a 32-bit address space; 32 bits = 32 / 8 = 4 bytes. Most of the peripherals are defined by STM, meaning you won't have access to all the memory. Keep in mind, too, that 2 KB at 8 bits and 8 KB at 32 bits is not a fourfold difference for the `int` type. If you need faster memory, that's F-RAM — but obviously it'll be pricier. So the STM32 usually comes with built-in flash memory, and the company recommends using exactly that. With flash there's another problem, though: few rewrite iterations. Usually tens of thousands, but it might be a thousand — which is to say, after debugging you throw the controller straight out.

## TinyML

Once the device is designed, it's time to flash the trained model onto it. The model has to be simple enough to run on milliwatts. When we talk about classic ML, what surfaces in the mind is huge data centers that process information and train neural networks — kilometer-long buildings with enormous electricity consumption. It's those volumes that give us the blessings of neural nets. The data the nets produce is served over the internet to IoT devices, and the devices themselves do nothing with it. If that doesn't suit us, and we want an IoT device that works without the internet, then you have two paths: embedded systems and machine learning.

It's worth asking why you'd flash a model onto a microcontroller at all, rather than leaving it to run in the cloud. Picture a small pacemaker that monitors and analyzes the heart in real time — a device like that can't rely on the constant presence of the internet. Nor can a fire alarm. We don't flash Python code onto the controller directly; we build the model separately. Training happens on your working computer, and only then does the model go to the controller.

The first mandatory step in ML work is data engineering. Almost always, up to 80% of the time goes into collecting and preparing the dataset. Training the model for microcontrollers takes minutes; deploying it, up to an hour. And then MLOps begins.

Broadly, neural nets consist of three blocks: a guess, a measurement of the guess's accuracy, and optimization. The core idea of ML is solving specific problems. People feed in data and give the right answers — providing 10,000 photos of cats, say, and telling the model there are cats in the photos — and out come rules that can be applied to new data, to find cats in other photos. Or to beat a human at chess, run around as a bot in games, classify objects, recognize facial expressions, suggest interesting news.

The main algorithms used are decision trees, where each neuron gives a "yes" or "no." The questions, though, can be inadequate from a human point of view — things like: is the salary more than 0.54 kopecks?

Machine learning is first and foremost mathematics, not add-ons over the Python language. The essence of ML is transforming some input into a result. This is achieved by studying a large amount of data; training a model means searching for a set of weight values across all the network's layers. Let's look at gradient descent without libraries. We find `y`, print the real `y`, square it and take the root to find the loss.

```python
import math

w = 2
b = -1

x = [-1, 0, 1, 2, 3, 4]
y = [-3, -1, 1, 3, 5, 7]
myY = [(w * thisX) + b for thisX in x]

print("Real Y is:", y)
print("My Y is: ", myY)

total_square_error = sum((true_y - pred_y) ** 2 for true_y, pred_y in zip(y, myY))
root_mean_square_error = math.sqrt(total_square_error)

print("My loss is:", root_mean_square_error)
```

Neural networks consist of neurons connected to one another. The neurons in a human head and the neurons in machine learning have nothing in common. The connections between "computer" neurons have a certain weight, and the neuron itself doesn't work out what's important and what isn't — it simply performs its function, answering the question "is the sound loud or not," say. It's the weights that affect the significance of that result. For us it's all matrices and matrix products; we don't write neurons by hand. Now let's turn our original code into a neural network.

Working? Hurray. Let's turn the code above into something useful: we'll use [TensorFlow](https://www.tensorflow.org/) to create a neural net in a single line. We pass some information into the neuron, get an answer, and compare it with a previously validated correct answer — that's how we tell whether the net gives right answers or still needs training. Our network is sequential, meaning it runs left to right. That isn't always the case; the implementation can differ across algorithms.

The `units` parameter says how many neurons will be in the layer — one is enough for us. When compiling the model, we define the optimizer and the loss function. `SGD` is gradient descent, the direction the guess moves in. The model is trained on the data with `fit`.

```python
import tensorflow as tf
import numpy as np
from tensorflow import keras

model = tf.keras.Sequential([
    keras.layers.Dense(units=1, input_shape=[1])
])

model.compile(optimizer=tf.keras.optimizers.SGD(learning_rate=0.01),
              loss='mean_squared_error',
              metrics=['mae', 'mse'])

input_data = np.array([-1.0, 0.0, 1.0, 2.0, 3.0, 4.0], dtype=float)
target_output = np.array([-3.0, -1.0, 1.0, 3.0, 5.0, 7.0], dtype=float)

num_epochs = 16
history = model.fit(input_data, target_output, epochs=num_epochs, validation_split=0.2, verbose=1)

model.summary()

weights, bias = model.layers[0].get_weights()
print("Trained Weight: {}".format(weights[0][0]))
print("Trained Bias: {}".format(bias[0]))
```

## Choosing an ML method

For predicting numbers we, like the rest of the world, will use regression for classification. These are any tasks with a dependence on time; regression is even built into Excel. Reinforcement learning, for example — we train a car's autopilot. The car crashed on a bend, so the model has to draw conclusions and stop making that mistake. But it's better to apply ensemble methods for deep learning of neural networks. Boosting is an ensemble method, and in effectiveness it's on par with neural nets. Ensembles rest on a simple principle: take several not-especially-effective learning methods and tell them to fix one another's blunders, and the quality rises sharply — one algorithm focuses its attention on the errors of the previous one.

We choose TensorFlow because it's the most popular, and it's popular because of Python. That doesn't mean it's the best, fastest, or most accurate — just the most popular. There are imperative and declarative programming paradigms, and TensorFlow uses the declarative approach. It's an open-source machine-learning framework for deep learning, plus built-in support for classic algorithms. TensorFlow was devised by a Google team. More interesting for us, TensorFlow Lite can run on mobile and in JS. Its main drawback is the utterly incomprehensible error descriptions, owing to the special API classes.

There are alternatives, of course. [Keras](https://keras.io/) is very popular thanks to its high level of abstraction and requires no knowledge of complex mathematics — ideal for creating a model architecture, though for training I'd still turn to TensorFlow. [PyTorch](https://pytorch.org/) is TensorFlow's direct competitor, a battle of giants, Facebook vs Google.

Say the device has to recognize a face. Face recognition requires feature extraction — pulling out certain objects that let the network learn faster. Each neuron is connected to another, forming a fully connected net. Look at the example in the picture below. We take a pixel with a value of 34, then a "filter" is taken and multiplied by the pixel's value. In effect we remove the image, leaving only the features — fewer pixels, and the features become sharply defined.

![Applying a filter to extract features](https://your-scorpion.ru/wp-content/uploads/2023/08/Untitled-1.jpg)

But there's a catch. If we get a picture from a camera on the microcontroller, a 640×480px image will take up 640 * 480 * 1 * 4 = 1,228,800 bytes — the first two values being the width and height, 1 being the RGB channel, and 4 bytes/pixel. A file that large won't fit on a microcontroller. The way around it: suppose there's a dog in the frame, and let several scanning windows hunt for the dog in each video frame. Or clustering, to find anomalies — it looks for similar pixels and boxes them off.

![Scanning windows searching for the subject](https://your-scorpion.ru/wp-content/uploads/2023/08/Group-47420.png)

![Clustering to isolate anomalies](https://your-scorpion.ru/wp-content/uploads/2023/08/Group-47417.png)

Finding a cat works the way you'd eat an elephant — a piece at a time. First we train the model to recognize the various parts of the face, then we stitch them together. This can be done even on a weak microcontroller.

![Recognizing the subject part by part](https://your-scorpion.ru/wp-content/uploads/2023/08/Group-47441-1.png)

conv2d helps us here, breaking the picture into components. An Arduino Nano + SD Card Shield will fit a model like this quite comfortably, but it's still better to avoid a large number of layers. That can be achieved by changing the architecture, and by quantization. Also, in the first steps the model learns fast, but with each new step the loss decreases less — meaning we won't lose much in quality, since 80% of the model's effectiveness is reached in a small number of training iterations. That's how you get the services inside WeChat, face-based login confirmation, label recognition, entry to the metro.

[TensorFlow.js](https://www.tensorflow.org/js) lets you run the model on the front end, that is, on the device. Practically all desktop browsers are supported. Tensors are a wrapper over arrays that have a shape, like 2×2 or 3×3. Any model consists of layers — sequential ones, in our case.

The discipline of computer vision lets a computer recognize and classify objects from photo or video information, as we discussed above. Detection is a separate, harder task, and the hardest of all is tracking an object in video — moving the box after a moving object. But you've surely seen this many times on fairly simple devices, like door peepholes or automatic office gates. Deep learning is popular on microcontrollers; even the ESP32 board can handle it. Deep learning is going through a picture frame by frame — "deep" because there are many layers. Each layer is a stage, so in effect deep learning is a multi-stage way of cleaning the picture of the superfluous.

![Deep learning as layered stages](https://your-scorpion.ru/wp-content/uploads/2023/08/G424.png)

Let's solve a practical problem: we get a monochrome picture, 36 × 36 pixels, with digits from 0 to 9. This already exists in the MNIST dataset. And we'll use Keras. Off we go, loading the data:

```python
from keras import models
from keras import layers
from keras.datasets import mnist
from keras.utils import to_categorical

(train_images, train_labels), (test_images, test_labels) = mnist.load_data()
```

First we'll feed the neural network the training data, `train_images` and `train_labels`, which lets us train the network to map images to labels.

```python
new_image_size = (36, 36)
train_images_resized = np.array([np.resize(image, new_image_size) for image in train_images])
test_images_resized = np.array([np.resize(image, new_image_size) for image in test_images])

network = models.Sequential()
network.add(layers.Dense(512, activation='relu', input_shape=(new_image_size[0] * new_image_size[1],)))
network.add(layers.Dense(10, activation='softmax'))
```

Then the net has to be compiled:

```python
network.compile(optimizer='rmsprop',
                loss='categorical_crossentropy',
                metrics=['accuracy'])
```

Before training we'll preprocess the data, converting it into the shape the network expects to receive. After scaling, all the values should land in the interval [0, 1].

```python
train_images_resized = train_images_resized.reshape((60000, new_image_size[0] * new_image_size[1]))
train_images_resized = train_images_resized.astype('float32') / 255

test_images_resized = test_images_resized.reshape((10000, new_image_size[0] * new_image_size[1]))
test_images_resized = test_images_resized.astype('float32') / 255

train_labels_one_hot = to_categorical(train_labels)
test_labels_one_hot = to_categorical(test_labels)
```

The neural network will convert everything into the range from 0 to 1. To train the model, a simple line is enough: `network.fit(train_images_resized, train_labels_one_hot, epochs=5, batch_size=128)`.

![MNIST training run](https://your-scorpion.ru/wp-content/uploads/2023/08/Поездки.png)

Recognizing speech is a pure ML task in its own right — wake-word detection especially. With every voice speaker, like Marusya or Alice, and with any voice assistant, the interaction begins with a wake word. The moment you say "Hey Alice" or "Okay Google" you've spoken the wake word, which is converted to text and sent off for processing. Such key words are quite simple and eat little energy; the board can manage.

For TinyML you build the dataset with your own hands — or rather, you speak the words in your own voice. You can try using the huge datasets from [ImageNet](https://www.image-net.org/), but will they suit small devices? Let's count. Take a microphone at 16 kHz — that's 16,000 data points per second. So a one-second word would take up the microcontroller's entire memory. The solution is simple: from the sound you need to get a picture and amplify the visual traits, most likely on convolutional layers. To get the resulting signals, the Fourier transform helps us. At the output we get a spectrogram, a visual representation of the signal — from which you can tell, even visually, that identical words were spoken. But that's far from everything; you'll need filters to amplify the spectrogram's signal.

![Spectrograms for the word "Test"](https://your-scorpion.ru/wp-content/uploads/2023/08/adding_new_icons.png)

A very simple way to create your own ML model for microcontrollers is the service [edgeimpulse](https://edgeimpulse.com/); an alternative is [neuton.ai](https://neuton.ai). We record or upload our audio data and choose Audio (MFCC) for analysis, which yields a spectrogram. After training the model, the site even tells you the size required on the board and the execution time.

![edgeimpulse model results](https://your-scorpion.ru/wp-content/uploads/2023/08/Stamped-EID.gif)

But playing with voice on no-code services is small stuff. We'll head instead toward finding industrial anomalies — monitoring vibration changes on a lathe-milling machine with an ADcmXL1021-1 accelerometer, say, or a DFRobot F1031V for monitoring airflow. The most vivid is tracking a person's health: sensors in a smartwatch gather information, and with an accelerometer, gyroscope, and ECG they determine that an anomaly has occurred. The position of the body changed sharply, the pulse became suddenly higher or lower than average? An automatic call to the ambulance. But we'll still choose industry, since we already have good datasets on hand — namely [ToyADMOS](https://paperswithcode.com/dataset/toyadmos). Or you can go to a production site in person, record a few sounds, and generate synthetic data from a small set of anomalous data. We'll also solve the task of predictive maintenance, since we'll be finding anomalies before they occur, which is valuable too.

We'll use the k-means clustering algorithm, get the centroids, and group the data around them.

```python
%matplotlib inline
import matplotlib.pyplot as plt
import seaborn as sns; sns.set()
import numpy as np
```

```python
import matplotlib.pyplot as plt
from sklearn.datasets import make_blobs
from sklearn.cluster import KMeans

def generate_and_plot_clusters(num_centers, num_samples_per_center, cluster_std):
    X, y_true = make_blobs(n_samples=num_centers * num_samples_per_center, centers=num_centers,
                           cluster_std=cluster_std, random_state=0)

    plt.figure(figsize=(8, 6))
    plt.scatter(X[:, 0], X[:, 1], s=70, c=y_true, cmap='viridis')
    plt.title(f'Generated Clusters (Number of Centers: {num_centers})')
    plt.xlabel('Feature 1')
    plt.ylabel('Feature 2')
    plt.colorbar(label='Cluster Label')
    plt.show()

    return X

num_centers = 3
num_samples_per_center = 140
cluster_std = 0.36

X_train = generate_and_plot_clusters(num_centers, num_samples_per_center, cluster_std)

kmeans = KMeans(n_clusters=num_centers)
kmeans.fit(X_train)  # Create centroids
y_kmeans = kmeans.predict(X_train)

plt.figure(figsize=(8, 6))
plt.scatter(X_train[:, 0], X_train[:, 1], c=y_kmeans, s=50, cmap='viridis')
plt.scatter(kmeans.cluster_centers_[:, 0], kmeans.cluster_centers_[:, 1], s=200, c='red')
plt.title('KMeans Clustering Results')
plt.xlabel('Feature 1')
plt.ylabel('Feature 2')
plt.colorbar(label='Cluster Label')
plt.show()
```

![Generated clusters](https://your-scorpion.ru/wp-content/uploads/2023/08/designer-2023-08-28-at-1.23.11-PM.png)

We've got several clusters. Now we want to understand whether there are anomalies. In this example the clusters are the various stages of the industrial device's normal operation.

```python
from sklearn.cluster import KMeans
kmeans = KMeans(n_clusters=num_centers)
kmeans.fit(X_train)
y_kmeans = kmeans.predict(X_train)
```

Let's visualize:

```python
plt.scatter(X_train[:, 0], X_train[:, 1], c=y_kmeans, s=40, cmap='viridis')

centers = kmeans.cluster_centers_
plt.scatter(centers[:, 0], centers[:, 1], c='black', s=100, alpha=0.5);
```

![Clusters with centroids](https://your-scorpion.ru/wp-content/uploads/2023/08/photo_2023-07-10_11-21-34.jpg)

And now let's try to work out what the anomalies are:

```python
X_anomaly, y_anomaly_true = make_blobs(n_samples=300, centers=4,
                                       cluster_std=0.60, random_state=1)
plt.scatter(X_train[:, 0], X_train[:, 1], s=50);
plt.scatter(X_anomaly[:, 0], X_anomaly[:, 1], s=50);
```

![Test data thrown in against the clusters](https://your-scorpion.ru/wp-content/uploads/2023/08/designer-2023-08-28-at-12.27.16-PM-1.png)

We've thrown in test data. As a first step we have to calculate the distance from each data point to the nearest cluster center. As a second step we compute the distance from the center. These distances are what show us what's an anomaly and what's the device's normal operation.

```python
percentile_treshold = 97

train_distances = kmeans.transform(X_train)

center_distances = {key: [] for key in range(num_centers)}
for i in range(len(y_kmeans)):
    min_distance = train_distances[i][y_kmeans[i]]
    center_distances[y_kmeans[i]].append(min_distance)

center_99percentile_distance = {key: np.percentile(center_distances[key], \
                                                   percentile_treshold) \
                                for key in center_distances.keys()}

print(center_99percentile_distance)
```

Let's look at the outliers/anomalies. A visual oval boundary has been drawn from the centroids. Every point beyond the boundary is anomalous data.

```python
fig, ax = plt.subplots()

colors = []
for i in range(len(X_train)):
    min_distance = train_distances[i][y_kmeans[i]]
    if (min_distance > center_99percentile_distance[y_kmeans[i]]):
        colors.append(4)
    else:
        colors.append(y_kmeans[i])

ax.scatter(X_train[:, 0], X_train[:, 1], c=colors, s=40, cmap='viridis')

for i in range(len(centers)):
    circle = plt.Circle((centers[i][0], centers[i][1]), center_99percentile_distance[i], color='black', alpha=0.1);
    ax.add_artist(circle)
```

![Anomaly boundaries around the centroids](https://your-scorpion.ru/wp-content/uploads/2023/08/Untitled-1.png)

Let's overlay the ready-made anomalous test data on top:

![Anomalous test data overlaid](https://your-scorpion.ru/wp-content/uploads/2023/08/iPhone-14-Pro-Max-3-1.png)

It all sounds wonderful, but there's a huge downside: take a machine's vibrations and it's an enormous amount of data. The algorithm shown won't handle a volume like that. Let's go another way: we do, after all, know how the machine should work. That means we can get data on the device's reference operation. We don't know what the anomalies are or how they sound, but we do have an understanding of how it should work. We monitor the reference data, and the moment the data starts to differ — the machine needs checking.

The algorithm described above is also how noise reduction and data compression are implemented. And it's the basic implementation of autoencoders. In essence, an autoencoder is trained to reconstruct data after compression. With an autoencoder you can also find anomalies — training the model on normal data, then computing the reconstruction error to compare whether the new input resembles the training data.

## Model architecture

Now let's look at a convolutional network.

```python
from keras import layers
from keras import models
model = models.Sequential()
model.add(layers.Conv2D(32, (3, 3), activation='relu', input_shape=(28, 28, 1)))
model.add(layers.MaxPooling2D((2, 2)))
model.add(layers.Conv2D(64, (3, 3), activation='relu'))
model.add(layers.MaxPooling2D((2, 2)))
model.add(layers.Conv2D(64, (3, 3), activation='relu'))
```

Both Conv2D and MaxPooling2D output a 3D tensor with the shape height × width × channels. The deeper the network, the greater the compression. The number of channels is controlled by the first argument passed into the Conv2D layers (32 or 64).

```python
model.add(layers.Flatten())
model.add(layers.Dense(64, activation='relu'))
model.add(layers.Dense(10, activation='softmax'))
```

```python
from keras.datasets import mnist
from keras.utils import to_categorical

(train_images, train_labels), (test_images, test_labels) = mnist.load_data()

train_images = train_images.reshape((60000, 28, 28, 1))
train_images = train_images.astype('float32') / 255

test_images = test_images.reshape((10000, 28, 28, 1))
test_images = test_images.astype('float32') / 255

train_labels = to_categorical(train_labels)
test_labels = to_categorical(test_labels)

model.compile(optimizer='rmsprop', loss='categorical_crossentropy', metrics=['accuracy'])
model.fit(train_images, train_labels, epochs=5, batch_size=64)
```

And remember: the fewer neurons, the smaller our model, and quantization on top lets us get whole numbers out of floating-point ones. But if even that isn't enough, then our path leads to TensorFlow Lite for Microcontrollers.
