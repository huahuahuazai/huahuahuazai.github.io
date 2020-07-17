---
layout: post
title: "unity 音频资源属性及导入处理"
categories: unity
tags: Audio
author: FSH
---

* content
{:toc}


#### 一、音频资源属性

![7.png](http://ww1.sinaimg.cn/large/006zwgbUly1ggtqjo9wgkj30hh07e0sz.jpg)






- Force To Mono:强制单声道，启用选项，unity将在打包之前把多声道音频混合成单声道
    - Normalize：启用此选项后，音频将在“ Force To Mono”混音过程中标准化处理
- Load In Background:后台加载，启用此选项，片段的加载将在单独的线程上延迟发生，而不会阻塞主线程；
- Ambisonic：音频源以一种表示声场的格式存储音频，该声场可以根据听众的方位进行旋转。对于360度视频和XR
应用程序，如果您的音频文件包含Ambisonic编码的音频，请启用此选项；
- Load Type：加载方式
    - Decompress OnLoad：在音频加载后马上解压缩。对较小的压缩声音使用此选项可以避免动态解压缩的性能开销，不过在加载时解压缩Vorbis编码的声音将使用大约十倍于内存的内存（对于ADPCM编码约为3.5倍），因此请勿对大文件使用此选项
    - Compressed In Memory：将声音压缩在内存中并在播放时解压缩。 此选项具有轻微的性能开销（特别是对于Ogg / Vorbis压缩文件），因此仅将其用于加载时解压缩将使用大量内存的较大的文件。 解压缩发生在混音器线程上，可以在探查器窗口的音频窗格中的"DSP CPU"部分进行监视
    - Streming：动态解码声音。 此方法使用最少量的内存来缓冲从磁盘中逐步读取并在运行中解码的压缩数据。 解压缩发生在单独的流线程上，可以在Profiler窗口的音频窗格的"Streaming CPU"部分中监视其CPU使用情况。 即使没有加载任何音频数据，Streaming的剪辑也有大约200KB的消耗
- Preload Audio Data：预加载，如果启用，则在加载场景时将预先加载音频剪辑；Streaming加载类型该选项不可用；
- Compression Format：压缩格式
    - PCM:此选项以较大的文件大小为代价提供了更高的质量，最适合于非常短的声音效果;
    - Vorbis：压缩后的文件较小，但与PCM音频相比质量较低。可通过“质量”滑块配置压缩量。此格式最适合中等长度的声音效果和音乐；
    - ADPCM:这种格式对于包含大量噪音且需要大量播放的声音非常有用，例如脚步声，撞击声，武器。 压缩比是PCM的3.5倍，而且CPU使用率远低于MP3 / Vorbis格式，这使其成为上述类别声音的首选;
- Sample Rate Setting：采样率设置
    - Preserve Sample Rate：保留采样率,此设置使采样率保持不变;
    - Optimize Sample Rate: 优化采样率,此设置根据所分析的最高频率内容自动优化采样率;
    - Override Sample Rate: 覆盖采样率此设置允许手动覆盖采样率，因此可以有效地用于丢弃频率内容;
        - Sample Rate：采样率，手动设置；

#### 二、音频资源导入设置

1、配置策略
音频处理分为三个级别：短音频(1s以内)，中长音频(1s-5s),长音频(5s以上)

- 短音频处理策略：

属性设置 | 快速响应 | 无需快速响应
---|---|---
1s以内的音频 | Load Type = DecompressOnLoad；compressionFormat = ADPCM；loadInBackground = false； | Load Type = CompressedInMemory；compressionFormat = ADPCM；loadInBackground = false；
1s - 5s的音频 | Load Type = CompressedInMemory；compressionFormat = ADPCM；loadInBackground = false | Load Type = CompressedInMemory；compressionFormat = Vorbis；quality = 0.3f；loadInBackground = false 
5s以上的音频 | Load Type = Streaming；compressionFormat = Vorbis；quality = 0.3f；loadInBackground = true | 与快速响应相同

- 通用设置
    - sampleRateSetting = AudioSampleRateSetting.OverrideSampleRate;
    - sampleRateOverride = 22050;
    - ambisonic = false;
    - preloadAudioData = false;
    - forceToMono = clip.channels <= 1;


2、完整代码

```c#
    public override uint GetVersion() { return 1; }

    /// <summary>
    /// 音频导入后
    /// </summary>
    /// <param name="clip"></param>
    void OnPostprocessAudio(AudioClip clip)
    {
        AudioImporter importer = assetImporter as AudioImporter;
        if (importer.userData != GetVersion().ToString())
        {
            importer.ambisonic = false;
            importer.preloadAudioData = false;
            importer.forceToMono = clip.channels <= 1;
            importer.userData = GetVersion().ToString();

            AudioImporterSampleSettings setting = importer.defaultSampleSettings;
            setting.sampleRateSetting = AudioSampleRateSetting.OverrideSampleRate;
            setting.sampleRateOverride = 22050;

            if (clip.length > 5)
            {
                importer.loadInBackground = true;

                setting.loadType = AudioClipLoadType.Streaming;
                setting.compressionFormat = AudioCompressionFormat.Vorbis;
                setting.quality = 0.3f;
            }
            else if (clip.length > 1)
            {
                importer.loadInBackground = false;

                setting.loadType = AudioClipLoadType.CompressedInMemory;
                setting.compressionFormat = AudioCompressionFormat.ADPCM;
            }
            else
            {
                importer.loadInBackground = false;

                setting.loadType = AudioClipLoadType.DecompressOnLoad;
                setting.compressionFormat = AudioCompressionFormat.ADPCM;
            }

            importer.defaultSampleSettings = setting;

            importer.SaveAndReimport();
            EditorUtility.SetDirty(importer);
        }
    }
```