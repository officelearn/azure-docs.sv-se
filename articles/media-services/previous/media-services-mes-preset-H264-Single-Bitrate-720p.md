---
title: H264, enkel bithastighet, 720p Media Encoder Standard förinställda – Azure | Microsoft Docs
description: Avsnittet ger en översikt över den **H264, enkel bithastighet, 720p** uppgiften förinställd.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: f66da66c-9f21-441d-8038-51e3094e9307
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 8eb01ba54c51508b7e3ec8f40e54ac6bcbc4f8a5
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "55993256"
---
# <a name="h264-single-bitrate-720p"></a>H264, Enkel bithastighet, 720p
`Media Encoder Standard` definierar en uppsättning kodning förinställningar som du kan använda när du skapar kodningsjobb. Du kan använda en `preset name` att ange i vilket format du vill koda din mediefil. Du kan också skapa egna JSON eller XML-baserade förinställningar (med UTF-8- eller UTF-16-kodning. Sedan skickar du den anpassade förinställda till kodaren. Lista över alla förvalda namn som stöds av det här `Media Encoder Standard` encoder, se [uppgift förinställningar för Media Encoder Standard](media-services-mes-presets-overview.md).  
  
 Det här avsnittet beskrivs de `H264 Single Bitrate 720p` förinställda i XML- och JSON-format.  
  
 Den här förinställda ger en enda MP4-filen med en bithastighet 4500 kbit/s och stereo AAC-ljud. Detaljerad information om profilen med flera bithastigheter, sampling hastighet och så vidare på detta förinställda ska undersöka XML eller JSON som definieras nedan. Förklaringar av vad varje element i dessa förinställningar innebär och de giltiga värdena för varje element finns i den [Media Encoder Standard schemat](media-services-mes-schema.md) avsnittet.  
  
 XML  
  
```  
<?xml version="1.0" encoding="utf-16"?>  
<Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">  
  <Encoding>  
    <H264Video>  
      <KeyFrameInterval>00:00:02</KeyFrameInterval>  
      <SceneChangeDetection>true</SceneChangeDetection>  
      <H264Layers>  
        <H264Layer>  
          <Bitrate>4500</Bitrate>  
          <Width>1280</Width>  
          <Height>720</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Auto</Profile>  
          <Level>auto</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>4500</MaxBitrate>  
        </H264Layer>  
      </H264Layers>  
      <Chapters />  
    </H264Video>  
    <AACAudio>  
      <Profile>AACLC</Profile>  
      <Channels>2</Channels>  
      <SamplingRate>48000</SamplingRate>  
      <Bitrate>128</Bitrate>  
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
      "KeyFrameInterval": "00:00:02",  
      "SceneChangeDetection": true,  
      "H264Layers": [  
        {  
          "Profile": "Auto",  
          "Level": "auto",  
          "Bitrate": 4500,  
          "MaxBitrate": 4500,  
          "BufferWindow": "00:00:05",  
          "Width": 1280,  
          "Height": 720,  
          "BFrames": 3,  
          "ReferenceFrames": 3,  
          "AdaptiveBFrame": true,  
          "Type": "H264Layer",  
          "FrameRate": "0/1"  
        }  
      ],  
      "Type": "H264Video"  
    },  
    {  
      "Profile": "AACLC",  
      "Channels": 2,  
      "SamplingRate": 48000,  
      "Bitrate": 128,  
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
