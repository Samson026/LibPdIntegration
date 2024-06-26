# LibPd Unity Integration

- [About](#about)
- [Unique Features](#unique-features)
- [Quickstart](#quickstart)
- [Spatialisation](#spatialisation)
- [Caveats](#caveats)
- [Related Projects](#related-projects)
- [Pure Data Resources](#pure-data-resources)
- [Credits](#credits)

## About

This project is a fork of [LibPdIntergration](https://github.com/LibPdIntegration/LibPdIntegration) to support Android platforms as well as additional wrapper changes to support later versions of [libpd](https://github.com/libpd/libpd). Currently this project only supports Android platforms and therefore does not support Unity's play mode.

## Unique Features

LibPdIntegration offers a couple of features which set it apart from existing implementations of libpd for Unity:

- It works with recent versions of Unity (at the time of writing, it's been tested on **2018.4 LTS**, **2019.4 LTS**, **2020.3 LTS** and **2021.1**, though it should work on older versions too).
- It supports multiple instances. This was impossible with previous implementations, as libpd itself did not support running multiple patches side by side. The libpd developers have recently removed that limitation however, meaning LibPdIntegration can allow developers to run multiple Pd patches in their Unity projects. This also means it's now feasible to build a 3D scene in Unity with multiple Pd patches all spatialised using Unity's audio code.

## Quickstart

This repository contains everything you need to incorporate Pd patches into your Unity project. First download it from the [releases](https://github.com/LibPdIntegration/LibPdIntegration/releases) page, then copy the contents of the [Assets](Assets/) folder into your project's Assets folder. LibPdIntegration provides native libpd binaries for supported platforms in the [Plugins](Assets/Plugins/) subfolder, and a single C# script, [LibPdInstance.cs](Assets/Scripts/LibPdInstance.cs) in the [Scripts](Assets/Scripts/) subfolder.

PD patches should be placed in the [StreamingAssets/PdAssets](Assets/StreamingAssets/PdAssets/) folder (you can create your own subfolders within).

To associate a Pd patch with a Unity GameObject, you need to add a single **Lib Pd Instance** Component to the GameObject. Doing this will also add an **Audio Source** Component; this is necessary because Unity does not process audio for GameObjects without an Audio Source.

**Lib Pd Instance** is our wrapper for libpd. To associate a Pd patch with it, drag the patch from your [StreamingAssets/PdAssets](Assets/StreamingAssets/PdAssets/) folder to the **Patch** selector in the Inspector.

![LibPdInstance Inspector Patch Selector](docs/images/libpdinstance-patch.png)

Note that the order of Components matters. **Audio Source** must come before **Lib Pd Instance**.

The **Pipe Print To Console** toggle provided by **Lib Pd Instance** lets you pipe any **print** messages sent by your Pd patch to Unity's console for debugging purposes. Note that due to a limitation with libpd, this toggle is global. i.e. if you activate it for one **Lib Pd Instance**, it will be active for all **Lib Pd Instances**.

See the sister project [LibPdIntegrationExamples](https://github.com/LibPdIntegration/LibPdIntegrationExamples) and the [wiki](https://github.com/LibPdIntegration/LibPdIntegration/wiki) for more information, including how to communicate between Unity and libpd. And there's also [Yann Seznec's](http://www.yannseznec.com/) excellent [introductory videos on youtube](https://www.youtube.com/playlist?list=PLXGA7pVjV1jdfe2FaEs2EzuZ-16HLH1_0).

**Note:** Ensure pd-core.aar inside of [Assets/StreamingAssets/Android](Assets/StreamingAssets/Android) has been toggled to build for Android platforms.

![Correct Inspector settings for the 64-bit libpd Linux binary](docs/images/osx-linux-binary-exclusion.png)

## Spatialisation

Spatialisation of Pure Data patches in Unity is a little convoluted. The following describes a method that does not require any external frameworks, but do check out the [Spatialisation](https://github.com/LibPdIntegration/LibPdIntegration/wiki/spatialisation) page on the wiki if you happen to be using an external audio framework like [Steam Audio](https://valvesoftware.github.io/steam-audio/). The process is a bit more straightforward in that case.

For this method we need to use a special sound file ([included in this repository](https://github.com/LibPdIntegration/LibPdIntegration/blob/master/extras/SpatialiserFix.wav)) in our **Audio Source**, and use an **`adc~`** object in our PD patch to apply the **Audio Source** spatialisation to the output of our PD patch.

The necessary steps are:

**In Unity:**

1. Set the **Audio Source**'s **AudioClip** to our *SpatialiserFix.wav* sound file.

   ![Audio Source AudioClip set to SpatialiserFix.wav](docs/images/spatialiserfix-audioclip.png)

2. Ensure the **Audio Source** is set to **Play On Awake** and **Loop**.

   ![Audio Source Play On Awake and Loop set to true](docs/images/spatialiserfix-loop.png)

3. Set **Spatial Blend** to 1(_3D_).

   ![Audio Source Spatial Blend slider set to 3D](docs/images/spatialiserfix-spatialblend.png)

**In Pure Data:**

Multiply the output of your patch with the stereo input from an **`adc~`** object, like the section highlighted in blue here:
![Pure Data adc~ object output](docs/images/spatialiserfix-adc.png)

This will effectively apply the spatialisation that Unity applies to Audio Sources by default, to the output of our PD patch. For more information, see the [LibPdIntegrationExamples](https://github.com/LibPdIntegration/LibPdIntegrationExamples) project, and particularly the comments in the [_FilteredNoise-ADC.pd_](https://github.com/LibPdIntegration/LibPdIntegrationExamples/blob/master/Assets/StreamingAssets/PdAssets/SpatialisationPatches/FilteredNoise-ADC.pd) patch.


## Pure Data Resources

- [Unity & Pd using libpd: The Basics! - youtube playlist](https://www.youtube.com/playlist?list=PLXGA7pVjV1jdfe2FaEs2EzuZ-16HLH1_0)
- [Pure Data Main Site](https://puredata.info/)
- [Pure Data Forum](https://forum.pdpatchrepo.info/)
- [Pure Data Manual](http://write.flossmanuals.net/pure-data/introduction2/)
- [Martin Brinkmann's Pd Patches](http://www.martin-brinkmann.de/pd-patches.html)

## Credits

LibPdIntegration is developed by [Niall Moody](http://www.niallmoody.com), with assistance from [Yann Seznec](http://www.yannseznec.com/). It is licensed under the [MIT License](LICENSE.txt).