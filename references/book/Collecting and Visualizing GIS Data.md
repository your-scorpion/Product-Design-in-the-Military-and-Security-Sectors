# Collecting and Visualizing GIS Data
### Lessons from restoring mangrove forests in the tropics

*Maksim Tcvetkov · Product Design and Security Lead*

## Mangrove forests

Greening a city is a core ingredient of any livable urban environment. Greening a coastline in the tropics is that and something more — a visible contribution to the planet's ecology. But you can't simply scatter seed and walk away; getting anything to take hold demands considerably more than that. This article works through the key practices of the GIS world, using the restoration of tropical mangrove forests as its running example.

## Habitat conditions

There are many species of mangrove, and they differ widely in how forgiving they are about where they'll live. The discipline of bringing them back even has its own term of art — EMR, ecological mangrove restoration. In the UAE, mangroves grow on flat ground, one degree of slope at most; any steeper and the water simply washes them away. They need to be bathed regularly and sit exposed above the surface no more than about 40% of the time, spending the rest submerged.

When a propagule first settles into the sand, it doesn't stay put. It keeps drifting, sometimes for a good while, until it finds a place worth growing in. The right place is a bed of ground-up coral and shell — very small stones, ideally with a little clay mixed in. The soil should be dark and spongy, the water marine or brackish. That "soil" is really a combination of sand, silt, clay, and loam forming a porous, organically rich mass that water moves through freely. Its quality comes down to a neutral pH and a salinity somewhere between 32 and 78 ppt — a refractometer is the tool that settles the question. The whole substance draws its nutrients from evaporation, rain, and the tides. Mangroves won't root in stone, and they won't take to ordinary sand either; what they want are the tidal flats, down in the low ground. So once a propagule finds its spot, it halts for three to five days and begins to germinate. If there's black, sulfide-rich mud around it, that steers the direction of root growth, because roots refuse to push into sulfur and will instead climb, growing above ground level.

Mangroves in the UAE give up their seed once a year, somewhere between April and June. Even living almost on the equator, they still fruit seasonally. And a tree that fruits unusually hard is usually a tree in trouble — pouring everything into reproduction is how it tries to survive. For now, the only species that has held on in the UAE is *Avicennia marina*, the gray mangrove, precisely because it tolerates high salt and high humidity. Even so, hardy as it is, the water around it shouldn't exceed 41 ppt, and its temperature should stay within 14 to 37 degrees. Past 50 ppt, the leaves can no longer keep up with excreting the salt.

*Avicennia marina* solved that problem by secreting salt through glands in its leaves. It can reach eight meters, though the average mature tree in these conditions tops out around 40 centimeters. Its roots, meanwhile, may spread several meters, guarding the propagules from floating off and holding the tree above the waterline even through strong tides. The propagules themselves can wait beside the parent for up to a year, dormant, until conditions turn favorable. The more densely trees are packed together, the fewer seeds each one produces. You have to be sure a cutting sits high enough relative to the tide line, or it drowns; the movement of the water is what lets a propagule find fresh ground at a safe remove from its parent.

All of this gets assessed during the preliminary survey, carried out before any planting is planned. This is the stage for gathering the data the site demands — topographic, geological, and ecological alike.

| Tidal regime | Inundations per month | Mangrove species |
| --- | --- | --- |
| Frequent tides | 56–62 | *Sonneratia alba*, *Avicennia alba* |
| Moderately frequent tides | 45–59 | *Rhizophora* spp., *Bruguiera* spp. |
| Normal tides | 20–45 | Many species |
| Spring tides | 2–20 | Many species |
| Rare tides | 0–2 | Many species |

Harvesting seed for planting shouldn't come at the expense of the forest's own natural spread, and the recommendation is to take no more than 20% of the total. Whatever you collect today should be sown tomorrow; in between, the seed has to be kept in seawater. Any longer than that and you'll need a properly conditioned room — seawater, and no light.

As for the birds that like to make the mangroves home: flamingos, reef and gray herons, the crab-plover. And crabs will move in without fail. They earn their place — by digging their burrows they let the trees take in more oxygen, and they shred fallen leaves into the bargain. They'll eat the odd propagule too, but that's a small price. When a tree is underwater it can't take in oxygen on its own, and this is exactly where the crabs come in.

## Choosing a site

There are a few distinct ways to bring a forest back, and it helps to be clear about which one you're actually doing.

- **Restoration** returns tree density to whatever state it held before. The real work here is understanding why the trees were lost in the first place, and removing that cause. Press any two ecologists for a definition of "restoration" and you'll get two different answers, because everyone works from their own methodology — which is exactly why the *why* matters more than the label.
- **Rehabilitation** is a kind of revival, breathing new life into an ecosystem — possibly with a change in the mix of species.
- **Afforestation** is building a forest from nothing, on a mudflat where mangroves never historically grew.
- **Enhancement** is adding new trees to an existing ecosystem to improve the ecological conditions already there.

Artificial seeding is worth doing only where the ecosystem can't recover on its own, and where the ground can genuinely support long-term growth and development. Wherever possible, use seed from trees that grew on this site or one close to it.

The decision itself turns on a handful of factors, and it pays to weigh them honestly. Connectivity to neighboring ecosystems comes first — and where that link exists, the health of the adjacent stands becomes part of the calculation, since ailing trees next door are a warning rather than an invitation. The presence of seagrass or salt marsh nearby is a quiet bonus, because it compounds the site's capacity to hold carbon. Ecological significance has to be gauged. So does access: how easily the site can be reached for monitoring, for seeding, for bringing people in. And sitting over all of it is sea-level rise, because a site that works today may not survive the water of tomorrow.

Assessment can be done from satellite or drone imagery, but visiting in person still matters a great deal. Where the satellite shows low tree density, raising that density is what raises carbon storage — which is the whole point of the exercise.

## Post-planting monitoring

There's a framework called Before-After-Control-Impact (BACI) that lets you track and — crucially — separate the results of human planting from the trees' own natural spread. Checks run at least every six months through the first two and a half years, then again at five years and at ten. Where seedlings have died for reasons that have nothing to do with the environment, they need replacing; that kind of death usually traces back to a faulty planting method, an already-sick propagule, or a sudden storm.

A healthy tree is one whose leaves are green and free of encrusting shells, with no more than 10% yellowed. Sparse foliage is a worrying sign, chewed foliage is worse, and foliage gone altogether means the tree can be counted dead. All of this can be followed with MRV tools — Measurement, Reporting, Verification — and the findings acted on with further measures, whether that means shielding the trees from tides that run too strong or from grazing animals.

Among the diseases, the main threats come from fungi (*Eutypella*, *Aspergillus*, *Rhizopus*) and from insects (midges, ambrosia leaf beetles).

## Visualizing the data

The world of data visualization offers a great many "layers," each one throwing a particular aspect of the mangroves into relief. Every layer named below is tied to spatial data.

First, though, a little terminology. *Spatial resolution* is a sensor's ability to make out fine detail and small objects in an image; the higher it is, the smaller the things you can detect and tell apart. A satellite image is recorded across the visible spectrum, roughly 0.3 to 0.9 µm. *Spectral resolution*, by contrast, is the sensor's ability to distinguish different wavelengths of light — how finely it can slice the electromagnetic spectrum.

Many layers lean on the infrared. That range runs from 0.7 µm to 100 µm — a hundred times wider than the slice we can see. From 3.0 µm to 100 µm sits the thermal band, which includes the heat radiating off the ground itself. But the part that interests us is the stretch between 1 mm and 1 m, because that's where remote sensing does its work. Whatever energy reaches the ground can do one of three things: pass through it (as light does traveling through glass), reflect off it, or be absorbed by it. We want the reflection — and reflection comes in two kinds. Specular reflection works off smooth surfaces; diffuse reflection sends a photon bouncing between the angles of some small relief. Wavelength is what tips the balance: sand can be a smooth surface to a long wave and a decidedly rough one to a short.

**Satellite imagery** can supply valuable data, but it usually lacks the precision the implementation stage demands, and its resolution tends to be lower than what other survey methods deliver.

**NDVI** reports the health of vegetation from satellite imagery. It reads as healthy above a roughly hardcoded value of NDVI > 0.73, adjusted for each particular site; anywhere from 0 to 0.25, and you're effectively looking at bare earth. A leaf is green because it actively absorbs red and blue photons, while its chlorophyll soaks up the entire green band — and in autumn, with little chlorophyll left, the leaves turn their yellows and reds. NDVI from satellite imagery is the single most important thing the industry has. And NDVI × NIR gives you NIRv.

![NDVI vegetation-health layer over the survey footprint](https://your-scorpion.ru/wp-content/uploads/2026/02/Untitled-1.jpg)

**NDMI, NBR, and LST** will show you the climate.

**SAR** is used to detect soil degradation. In ecology, "degradation" usually denotes human impact that worsens an ecosystem's structure, functioning, or productivity — deforestation, fragmentation, fire, infrastructure, overgrazing. Natural processes such as plants dying off in a drought, ageing, or a mass die-off driven by a changing climate are disturbances, or expressions of natural dynamics, rather than degradation in the strict ecological sense — even if, these days, they're very often set in motion by us all the same.

**GCC** — the green channel over red plus green plus blue. Green reflectance.

**SAVI** is a vegetation index that takes soil brightness into account.

![SAVI soil-adjusted vegetation index](https://your-scorpion.ru/wp-content/uploads/2026/02/udh.jpg)

**EVI** does a good job of showing biomass. It needs the blue, red, and infrared channels, and an image in RGBN format.

**MSAVI** is SAVI's counterpart but works pixel by pixel, likewise using the red and near-infrared (NIR) channels. Infrared is what thermal remote sensing uses to measure temperature and emissivity.

**NDRE** again draws on red and near-infrared (NIR), and it's good at picking out the edges of plants.

**FVC** — fractional vegetation cover, the density of plants. It hints at biodiversity, and at soil degradation.

**LiDAR** (Light Detection and Ranging) uses laser pulses to build detailed 3D maps of a surface, which makes it possible to calculate the volume of irregular shapes precisely — sandy, rolling terrain and other relief marked by hills and valleys. It yields a DTM, and for gauging slopes it's far better suited than RGB or MSP.

There are portable, backpack LiDAR units that capture camera imagery and LiDAR data at the same time. They're often put to work mapping remote or hard-to-reach terrain, where traditional ground or aerial LiDAR platforms may be impractical. LiDAR delivers high survey accuracy, capturing detailed 3D data quickly and precisely. Common sense still applies, though: in zones of regular tides you won't be able to make comparisons — and bathymetric LiDAR is what rescues that situation. There's also the matter of mature mangroves "ploughing" the earth around themselves, leaving ground that no longer matches the criteria set out at the start of this article.

**DEM** represents the elevation values of the Earth's surface and of the features on it — buildings, vegetation, other structures — showing their height relative to a base point such as sea level.

![Digital elevation model of the surface](https://your-scorpion.ru/wp-content/uploads/2026/02/ds.jpg)

Technically, most of this data can be obtained through remote sensing: any means of learning about the Earth's surface without touching it directly. It takes seven key components. A source of light, or of some other electromagnetic energy, travels through the atmosphere toward the surface; the energy reaches the surface and interacts with it, propagating and reflecting; that response is registered by a remote sensor; and the sensor passes the data on to storage, where it is analyzed and turned into insight.

The most visually striking technique of all is building detailed 3D models and maps out of photographs. The whole aim of photogrammetry is to produce accurate, detailed 3D models, maps, and measurements of objects or terrain from overlapping photographs. The technique analyzes the geometry and spatial relationships within the photos to extract precise measurements and reconstruct the photographed scene in three dimensions. And data from an ETS (Electronic Total Station) is enormously useful here.

## Metrics

- Seedling count per subplot
- Percentage of seedlings survived
- Height and canopy width of seedlings
- Health of seedlings ('healthy', 'sick', 'dead', 'grazed')

## Collecting the data

Before you can visualize data, you first have to collect it. The most classic approach is to go out into the field on foot and gather it by hand. Manual survey belongs to the traditional methods — the hand tools and techniques that are, as a rule, less precise than the automated, digital methods modern geodetic surveying relies on.

But since we live in a world of helicopters, drones, and satellites, let's look at the more modern ways of collecting it. The first is the orthophoto — an aerial photograph that has been geometrically corrected to a single, uniform scale. That correction removes the effects of camera tilt and terrain relief, so you can measure true distances straight off the image; the uniformity is achieved through a process called orthorectification.

Almost certainly, you'll find yourself collecting data with planes and drones. They're used constantly to shoot high-resolution imagery over defined areas, but unlike ground platforms, they aren't generally the tool for continuous or long-term monitoring. In agriculture, the accepted drones are:

- **DJI Mavic 3M** — small, and suited to sites under 100 hectares.
- **Wingtra WingtraOne Gen II** — a genuinely capable bird, and convenient in a lot of ways.
- **Quantum Systems Trinity Pro.**
- **Foxtech.**
- Or alternative methods, such as the Phase One PAS PANA.

In a triangulation survey, the area is divided into well-formed triangles — triangles whose angles are neither too small nor too large, ideally between 30° and 120° — which makes for higher accuracy and more stable measurements.

Leveling is a traditional geodetic method used to measure differences in height and to establish precise reference points on construction projects. Geodesy involves the exact measurement of distances, angles, and elevations to fix the relative positions of points on the Earth's surface; a geodetic survey, accordingly, measures large areas of that surface to a high standard, accounting for the curvature of the Earth and using sophisticated equipment and techniques to reach an accuracy other survey types can't match.

Ground-penetrating radar (GPR) is a geophysical method that images the subsurface using radar pulses. It's commonly used to locate buried structures, watercourses among them, by sending electromagnetic pulses into the ground and measuring the reflections off subsurface objects; differences in material properties — the presence of water, for instance — can produce distinct reflections that help identify an underground watercourse.

And then there's the other heavy artillery: the ground platforms. Also known as ground-based weather and monitoring stations, they really are used for the continuous monitoring of atmospheric phenomena — temperature, humidity, air pressure, and so on — as well as for the long-term monitoring of terrestrial features such as changes in land use, vegetation growth, and environmental conditions. These platforms provide local data collection, often in real time, which is what makes them so valuable across a range of monitoring applications.

**GNSS** (Global Navigation Satellite System) — GNSS receivers are widely used in modern surveying for precise positioning, even if historically they aren't counted as traditional equipment. One of their key advantages is relative independence from environmental conditions: a GNSS receiver can pull accurate position data across a variety of weather and terrain, which makes it a versatile field instrument. Mounting the receivers on tripods is necessary for collecting data from reference stations, though that isn't the decisive factor in running a CORS network well.

There are nuances here, and plenty of them. One is ionospheric error, which arises from the ionosphere's effect on GNSS signal propagation, introducing delays and distortions; it can contribute to positioning error, though it isn't usually the largest source. All such nuances can be measured and accounted for. DOP, for example, stands for "dilution of precision" — a term used in satellite navigation and GPS to quantify how satellite geometry affects positional accuracy.

Raw GNSS data is stored in RINEX (Receiver Independent Exchange Format) files and imported into Trimble Business Center for post-processing. RINEX files hold the raw satellite-observation data the receivers collect, and they're compatible with a range of post-processing software.

**Radar** (Radio Detection and Ranging) is used for weather forecasting. By transmitting radio waves and measuring the returned signals, radar can detect precipitation, cloud, storms, and other atmospheric phenomena — information that's decisive for monitoring conditions and forecasting extreme weather.

A **level** is an optical instrument for measuring differences in height or elevation. It gives precise measurements while generally costing less than a total station.

A **compass** is a very simple, inexpensive surveying instrument for measuring directions — not as refined or as costly as a total station.

**Theodolites** are optical instruments for measuring horizontal and vertical angles. They're precise and indispensable in geodetic work, and usually cheaper than total stations, which add extra functions like distance measurement.

**Total stations** are modern instruments that combine electronic distance measurement (EDM) with the functions of a theodolite. High accuracy, high precision, and extended features such as data recording and storage make them the more expensive option next to simpler instruments — a compass, a level, a theodolite.

**The single-beam echo sounder.** In shallow coastal waters and rivers, single-beam echo sounders are the usual way to measure depth. The method emits a single acoustic pulse downward from a vessel or platform and times how long it takes to bounce off the bottom and return to the sensor; depth follows from the round trip.

Another way to look beneath the water is the **bathymetric survey**, which concerns itself with underwater features rather than surface topography. It measures and maps the depth, contours, and features of oceans, seas, lakes, and rivers — essential for understanding underwater topography, for marine navigation, and for coastal engineering. The output takes the form of contour maps: lines joining points of equal elevation (equal depth, in bathymetry's case) that give a visual read on underwater relief — troughs, ridges, and valleys.

Now for the real heavy artillery. **CORS** — continuously operating reference stations. This is a network of permanently installed reference stations that feed GNSS corrections to receivers in real time. Those corrections make precise positioning and navigation possible by compensating for errors introduced by things like atmospheric conditions and satellite orbit drift. The data is recorded continuously, usually at intervals of under 30 seconds.

Mobile platforms like smartphones and tablets generally aren't used for the continuous monitoring of atmospheric phenomena or the long-term monitoring of terrestrial features; they're more for personal or local data collection and don't see much use at large monitoring scales. Space platforms — satellites — handle the continuous monitoring of the atmosphere and the long-term monitoring of terrestrial features. And ground platforms are purpose-built for continuous, localized monitoring.

## Colors

Cartography uses three main types of color scheme for laying data over a base layer.

**1) Sequential schemes.** These are for ordered or numeric data that progresses from low to high. The palette varies in lightness — light to dark, or the reverse — which makes trends easy to follow.

![Sequential color scheme](https://your-scorpion.ru/wp-content/uploads/2066/02/dda.png)

**2) Diverging schemes.** Ideal for drawing out values above and below a central midpoint (a mean, say, or zero). The middle color marks the reference value, and the contrasting tones at either end mark the high and low extremes. Combine a few of these and you get excellent colors for a treemap.

![Diverging color scheme](https://your-scorpion.ru/wp-content/uploads/2066/02/1313d-1.png)

**3) Qualitative schemes.** Best for categorical data, where each category gets its own hue at equal lightness and saturation — perfect for telling regions, land-use types, or administrative boundaries apart. Say you have five parameters shifting over time: reach for a control chart and contrasting colors. Or you just need a legend for a marimekko? This is exactly the scheme that does it.

![Qualitative color scheme](https://your-scorpion.ru/wp-content/uploads/2066/02/da.png)
