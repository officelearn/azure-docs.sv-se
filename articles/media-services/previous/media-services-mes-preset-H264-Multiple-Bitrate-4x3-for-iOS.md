---
title: H264 Flera Bitrate 4x3 för iOS | Microsoft-dokument
description: Avsnittet innehåller en översikt över **H264 Multiple Bitrate 4x3 för** iOS-aktivitetsförinställning.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 7f587c46-bda5-49e6-abad-203380e1d1ab
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 45dbaea4e2c6f8cdbe3d33df5d3b82b9df519ff2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61463658"
---
# <a name="h264-multiple-bitrate-4x3-for-ios"></a>H264, flera bithastigheter, 4x3 för iOS
`Media Encoder Standard`definierar en uppsättning kodningsförinställningar som du kan använda när du skapar kodningsjobb. Du kan antingen `preset name` använda en för att ange i vilket format du vill koda mediefilen. Du kan också skapa egna JSON- eller XML-baserade förinställningar (med UTF-8- eller UTF-16-kodning. Du skulle sedan skicka den anpassade förinställningen till kodaren. En lista över alla förinställda `Media Encoder Standard` namn som stöds av den här kodaren finns i [Aktivitetsförinställningar för Media Encoder Standard](media-services-mes-presets-overview.md).  
  
 I det `H264 Multiple Bitrate 4x3 for iOS` här avsnittet visas förinställningen i XML- och JSON-format.  
  
 Den här förinställningen ger en uppsättning 8 GOP-anpassade MP4-filer, från 8500 kbps till 200 kbps och stereo AAC-ljud. För detaljerad information om profil, bithastighet, samplingsfrekvens etc. för den här förinställningen, undersök XML eller JSON som definieras nedan. Förklaringar av vad varje element i dessa förinställningar innebär och giltiga värden för varje element finns i [schemaavsnittet Media Encoder Standard.](media-services-mes-schema.md)  
  
> [!NOTE]
>  När du `Width` ändrar värdena och `Height` värdena mellan lager kontrollerar du att proportionerna förblir konsekventa. Till exempel: 1920x1080, 1280x720, 1080x576, 640x360. Du bör inte använda en blandning av proportioner, till exempel: 1280x720, 720x480, 640x360.  
  
 XML  
  
```  
<?xml version="1.0" encoding="utf-16"?>  
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">  
  <Encoding>  
    <H264Video>  
      <KeyFrameInterval>00:00:03</KeyFrameInterval>  
      <H264Layers>  
        <H264Layer>  
          <Bitrate>8500</Bitrate>  
          <Width>1920</Width>  
          <Height>1440</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>High</Profile>  
          <Level>5</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>8500</MaxBitrate>  
        </H264Layer>  
        <H264Layer>  
          <Bitrate>6500</Bitrate>  
          <Width>1280</Width>  
          <Height>960</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Main</Profile>  
          <Level>3.2</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>6500</MaxBitrate>  
        </H264Layer>  
        <H264Layer>  
          <Bitrate>5000</Bitrate>  
          <Width>1280</Width>  
          <Height>960</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Main</Profile>  
          <Level>3.2</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>5000</MaxBitrate>  
        </H264Layer>  
        <H264Layer>  
          <Bitrate>3500</Bitrate>  
          <Width>960</Width>  
          <Height>720</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Main</Profile>  
          <Level>3.1</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>3500</MaxBitrate>  
        </H264Layer>  
        <H264Layer>  
          <Bitrate>1200</Bitrate>  
          <Width>640</Width>  
          <Height>480</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Baseline</Profile>  
          <Level>3.1</Level>  
          <BFrames>0</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>false</AdaptiveBFrame>  
          <EntropyMode>Cavlc</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>1200</MaxBitrate>  
        </H264Layer>  
        <H264Layer>  
          <Bitrate>600</Bitrate>  
          <Width>640</Width>  
          <Height>480</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Baseline</Profile>  
          <Level>3</Level>  
          <BFrames>0</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>false</AdaptiveBFrame>  
          <EntropyMode>Cavlc</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>600</MaxBitrate>  
        </H264Layer>  
        <H264Layer>  
          <Bitrate>400</Bitrate>  
          <Width>480</Width>  
          <Height>360</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Baseline</Profile>  
          <Level>3</Level>  
          <BFrames>0</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>false</AdaptiveBFrame>  
          <EntropyMode>Cavlc</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>400</MaxBitrate>  
        </H264Layer>  
        <H264Layer>  
          <Bitrate>200</Bitrate>  
          <Width>400</Width>  
          <Height>300</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Baseline</Profile>  
          <Level>3</Level>  
          <BFrames>0</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>false</AdaptiveBFrame>  
          <EntropyMode>Cavlc</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>200</MaxBitrate>  
        </H264Layer>  
      </H264Layers>  
      <Chapters />  
    </H264Video>  
    <AACAudio>  
      <Profile>HEAACV2</Profile>  
      <Channels>2</Channels>  
      <SamplingRate>48000</SamplingRate>  
      <Bitrate>64</Bitrate>  
    </AACAudio>  
  </Encoding>  
  <Outputs>  
    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">  
      <MP4Format />  
    </Output>  
  </Outputs>  
</Preset>  
```  
  
 JSON  
  
```  
{  
  "Version": 1.0,  
  "Codecs": [  
    {  
      "KeyFrameInterval": "00:00:03",  
      "H264Layers": [  
        {  
          "Profile": "High",  
          "Level": "5",  
          "Bitrate": 8500,  
          "MaxBitrate": 8500,  
          "BufferWindow": "00:00:05",  
          "Width": 1920,  
          "Height": 1440,  
          "BFrames": 3,  
          "ReferenceFrames": 3,  
          "AdaptiveBFrame": true,  
          "Type": "H264Layer",  
          "FrameRate": "0/1"  
        },  
        {  
          "Profile": "Main",  
          "Level": "3.2",  
          "Bitrate": 6500,  
          "MaxBitrate": 6500,  
          "BufferWindow": "00:00:05",  
          "Width": 1280,  
          "Height": 960,  
          "BFrames": 3,  
          "ReferenceFrames": 3,  
          "AdaptiveBFrame": true,  
          "Type": "H264Layer",  
          "FrameRate": "0/1"  
        },  
        {  
          "Profile": "Main",  
          "Level": "3.2",  
          "Bitrate": 5000,  
          "MaxBitrate": 5000,  
          "BufferWindow": "00:00:05",  
          "Width": 1280,  
          "Height": 960,  
          "BFrames": 3,  
          "ReferenceFrames": 3,  
          "AdaptiveBFrame": true,  
          "Type": "H264Layer",  
          "FrameRate": "0/1"  
        },  
        {  
          "Profile": "Main",  
          "Level": "3.1",  
          "Bitrate": 3500,  
          "MaxBitrate": 3500,  
          "BufferWindow": "00:00:05",  
          "Width": 960,  
          "Height": 720,  
          "BFrames": 3,  
          "ReferenceFrames": 3,  
          "AdaptiveBFrame": true,  
          "Type": "H264Layer",  
          "FrameRate": "0/1"  
        },  
        {  
          "Profile": "Baseline",  
          "Level": "3.1",  
          "Bitrate": 1200,  
          "MaxBitrate": 1200,  
          "BufferWindow": "00:00:05",  
          "Width": 640,  
          "Height": 480,  
          "ReferenceFrames": 3,  
          "EntropyMode": "Cavlc",  
          "Type": "H264Layer",  
          "FrameRate": "0/1"  
        },  
        {  
          "Profile": "Baseline",  
          "Level": "3",  
          "Bitrate": 600,  
          "MaxBitrate": 600,  
          "BufferWindow": "00:00:05",  
          "Width": 640,  
          "Height": 480,  
          "ReferenceFrames": 3,  
          "EntropyMode": "Cavlc",  
          "Type": "H264Layer",  
          "FrameRate": "0/1"  
        },  
        {  
          "Profile": "Baseline",  
          "Level": "3",  
          "Bitrate": 400,  
          "MaxBitrate": 400,  
          "BufferWindow": "00:00:05",  
          "Width": 480,  
          "Height": 360,  
          "ReferenceFrames": 3,  
          "EntropyMode": "Cavlc",  
          "Type": "H264Layer",  
          "FrameRate": "0/1"  
        },  
        {  
          "Profile": "Baseline",  
          "Level": "3",  
          "Bitrate": 200,  
          "MaxBitrate": 200,  
          "BufferWindow": "00:00:05",  
          "Width": 400,  
          "Height": 300,  
          "ReferenceFrames": 3,  
          "EntropyMode": "Cavlc",  
          "Type": "H264Layer",  
          "FrameRate": "0/1"  
        }  
      ],  
      "Type": "H264Video"  
    },  
    {  
      "Profile": "HEAACV2",  
      "Channels": 2,  
      "SamplingRate": 48000,  
      "Bitrate": 64,  
      "Type": "AACAudio"  
    }  
  ],  
  "Outputs": [  
    {  
      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",  
      "Format": {  
        "Type": "MP4Format"  
      }  
    }  
  ]  
}  
```
