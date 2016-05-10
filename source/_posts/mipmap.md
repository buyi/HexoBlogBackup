title: mipmap
date: 2015-07-01 01:32:39
tags:
---
mipmaps最近进入我的视野，为了一探究竟，我上网查找了相关资料，做了以下总结。

##1.针对mipmaps的官方描述

In 3D computer graphics, mipmaps (also MIP maps) are pre-calculated, optimized collections of images that accompany a main texture, intended to increase rendering speed and reduce aliasing artifacts. They are widely used in 3D computer games, flight simulators and other 3D imaging systems for texture filtering. Their use is known as mipmapping. The letters “MIP” in the name are an acronym of the Latin phrase multum in parvo, meaning “much in little”.[1] Since mipmaps cannot be calculated in real time, additional storage space is required to take advantage of them. They also form the basis of wavelet compression.

在三维计算机图形学中，mipmaps是为了加快渲染速度和减少图像锯齿，伴随主纹理而预先计算，优化过的图片组成的文件。它在3d计算机游戏中 飞行模拟器和其他3d动画系统应用广泛。它通常被当作mipmapping来使用，“MIP”来自于拉丁语 multum in parvo 的首字母，意思是“放置很多东西的小空间”。因为mipmaps不能实时计算，所以需要多余的计算空间，它也遵循小波压缩规则（the basis of wavelet compression）。

##2.mipmap与drawable的异同

There are two distinct uses of mipmaps.
使用mipmaps有两个特别之处。

1) For launcher icons when building density specific APKs. Some developers build separate APKs for every density, to keep the APK size down. However some launchers (shipped with some devices, or available on the Play Store) use larger icon sizes than the standard 48dp. Launchers use getDrawableForDensity and scale down if needed, rather than up, so the icons are high quality. For example on an hdpi tablet the launcher might load the xhdpi icon. By placing your launcher icon in the mipmap-xhdpi directory, it will not be stripped the way a drawable-xhdpi directory is when building an APK for hdpi devices. If you're building a single APK for all devices, then this doesn't really matter as the launcher can access the drawable resources for the desired density.

为启动图标构建density特定apk时。
有些开发者为不同的密度来构建不同的apk，来使得开发包大小减少。然而一些启动器会使用大雨标准48dp的更大的图标。启动器会根据实际情况调用getDrawableForDensity来进行缩小而不是增大。所以图标一般都是高质量的。譬如在一个hdpi的平板上，启动器可能会加载xhdpi的图标。通过在mipmap-xhdpi文件夹中放置你的应用图标，它不会在为了hdpi设备打包时被排除。如果你为所有设备只打一个apk包，那么放在哪里都无关紧要，因为启动器可以按照制定密度去获取对应的drawable资源。

2) The actual mipmap API from 4.3.  It's not used by the Android Open Source Project launchers and I'm not aware of any other launcher using.

4.3之后有了真正的mipmap API。但是官方的launcher并没有使用。所以我也不关心其他的启动器是否使用。


##3. 官方对于mipmap的使用

Different home screen launcher apps on different devices show app launcher icons at various resolutions. When app resource optimization techniques remove resources for unused screen densities, launcher icons can wind up looking fuzzy because the launcher app has to upscale a lower-resolution icon for display. To avoid these display issues, apps should use the mipmap/ resource folders for launcher icons. The Android system preserves these resources regardless of density stripping, and ensures that launcher apps can pick icons with the best resolution for display.

不同的桌面启动器在不同的设备上会展示不同分辨率的app启动图标。当app资源优化技术去除了未用屏幕密度资源时，启动图标有可能看起来发虚，因为启动器会为了更好的显示而拉伸低密度的图标。为了避免这些显示问题，我们应该使用mipmap资源文件夹。Android系统会不管当前密度而始终保留该资源，以便让启动器能够挑选一个最佳分辨率的图标进行显示。

Make sure launcher apps show a high-resolution icon for your app by moving all densities of your launcher icons to density-specific res/mipmap/ folders (for example res/mipmap-mdpi/ and res/mipmap-xxxhdpi/). The mipmap/ folders replace the drawable/ folders for launcher icons. For xxhpdi launcher icons, be sure to add the higher resolution xxxhdpi versions of the icons to enhance the visual experience of the icons on higher resolution devices.

为了让启动app能够显示高分辨率的图标，我们可以将所有启动图标分别移至对应的mipmap文件夹中。这样对于启动图标而言，mipmap文件夹会代替drawable文件夹。对于xxhpdi启动图标而言，请确保添加更高版本（xxxhdpi）的图标，以增强在高分辨率设备的视觉体验。

Note: Even if you build a single APK for all devices, it is still best practice to move your launcher icons to the mipmap/ folders.

即使你是为所有设备打一个包，也建议你进行启动图标的迁移。

综上所述，mipmap应该是一个为了加快图片渲染速度，提高图片质量，降低GPU负载的图片处理技术。但是目前为止没有大规模使用。而android官方应用它的主要目的为了针对launcher图标在缩放图片时能更好地保留图片细节。
