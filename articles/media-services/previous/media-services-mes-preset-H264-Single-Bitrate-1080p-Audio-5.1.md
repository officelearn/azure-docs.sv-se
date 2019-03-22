---
title: H264, Enkel bithastighet, 1080p, Audio 5.1 | Microsoft Docs
description: Avsnittet ger en översikt över den **H264, enkel bithastighet 1080p, Audio 5.1** uppgiften förinställd.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: b42238de-2a3c-4683-ae7f-7ce19ad5162e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 83d1c279552a75b959c19fb37e859806571f48eb
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259497"
---
# <a name="h264-single-bitrate-1080p-audio-51"></a>H264, Enkel bithastighet, 1080p, Audio 5.1

`Media Encoder Standard` definierar en uppsättning kodning förinställningar som du kan använda när du skapar kodningsjobb. Du kan använda en `preset name` att ange i vilket format du vill koda din mediefil. Du kan också skapa egna JSON eller XML-baserade förinställningar (med UTF-8- eller UTF-16-kodning. Sedan skickar du den anpassade förinställda till kodaren. Lista över alla förvalda namn som stöds av det här `Media Encoder Standard` encoder, se [uppgift förinställningar för Media Encoder Standard](media-services-mes-presets-overview.md).  
  
 Det här avsnittet beskrivs de `H264 Single Bitrate 1080p Audio 5.1` förinställda i XML- och JSON-format...  
  
 Den här förinställningen skapar en enda MP4-fil med en bithastighet 6750 kbit/s och AAC 5.1 ljud. Detaljerad information om profilen med flera bithastigheter, sampling hastighet och så vidare på detta förinställda ska undersöka XML eller JSON som definieras nedan. Förklaringar av vad varje element innebär och de giltiga värdena för varje element finns i den [Media Encoder Standard schemat](media-services-mes-schema.md).  
  
 XML  
  
```  
<?xml version="1.0" encoding="utf-16"?>  
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">  
  <Encoding>  
    <H264Video>  
      <KeyFrameInterval>00:00:02</KeyFrameInterval>  
      <SceneChangeDetection>true</SceneChangeDetection>  
      <H264Layers>  
        <H264Layer>  
          <Bitrate>6750</Bitrate>  
          <Width>1920</Width>  
          <Height>1080</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Auto</Profile>  
          <Level>auto</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>6750</MaxBitrate>  
        </H264Layer>  
      </H264Layers>  
      <Chapters />  
    </H264Video>  
    <AACAudio>  
      <Profile>AACLC</Profile>  
      <Channels>6</Channels>  
      <SamplingRate>48000</SamplingRate>  
      <Bitrate>384</Bitrate>  
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
          "Bitrate": 6750,  
          "MaxBitrate": 6750,  
          "BufferWindow": "00:00:05",  
          "Width": 1920,  
          "Height": 1080,  
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
      "Channels": 6,  
      "SamplingRate": 48000,  
      "Bitrate": 384,  
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
