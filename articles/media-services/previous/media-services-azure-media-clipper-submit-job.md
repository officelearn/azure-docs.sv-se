---
title: Skicka in urklipps jobb i Azure Media Clipper | Microsoft Docs
description: Steg för att skicka urklipps jobb från Azure Media Clipper
services: media-services
keywords: klipp; subclip; encoding; Media
author: Juliako
manager: femila
ms.author: juliako
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 04d0d2bb8939c8036ec6817c58f9ac2fbb3acd72
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684974"
---
# <a name="submit-clipping-jobs-from-azure-media-clipper"></a>Skicka urklipps jobb från Azure Media Clipper 

Azure Media Clipper kräver att en **submitSubclipCallback** -Metod implementeras för att hantera urklipps jobb. Den här funktionen används för att implementera ett HTTP-inlägg av Clipper-utdata till en webb tjänst. I den här webb tjänsten kan du skicka kodnings jobbet. Utdata från Clipper är antingen en Media Encoder Standard kodning för återgivna jobb eller REST API nytto last för dynamiska manifest filter anrop. Den här direkt modellen är nödvändig eftersom autentiseringsuppgifterna för Media Services-kontot inte är säkra i klientens webbläsare.

Följande sekvensdiagram illustrerar arbets flödet mellan webb läsar klienten, din webb tjänst och Azure Media Services: ![Azure Media Clipper sequence-diagram](media/media-services-azure-media-clipper-submit-job/media-services-azure-media-clipper-sequence-diagram.PNG)

I föregående diagram är de fyra entiteterna: användarens webbläsare, din webb tjänst, CDN-slutpunkten som är värd för Clipper-resurserna och Azure Media Services. När slutanvändaren navigerar till din webb sida hämtar sidan Clipper-JavaScript-och CSS-resurserna från värd-CDN-slutpunkten. Slutanvändaren konfigurerar urklipps jobbet eller anropet för att skapa dynamiskt manifest filter från webbläsaren. När slutanvändaren skickar ett anrop till jobbet eller filtret skapar webbläsaren jobbets nytto Last till en webb tjänst som du måste distribuera. Den här webb tjänsten skickar slutligen ut urklipps jobbet eller filtret för att skapa filter till Azure Media Services med autentiseringsuppgifterna för Media Services-kontot.

Följande kod exempel illustrerar en Sample **submitSubclipCallback** -metod. I den här metoden implementerar du HTTP-posten för den Media Encoder Standard kodnings för inställningen. Om inlägget lyckades (**resultat**) löses **löftet** , annars avvisas det med fel information.

```javascript
// Submit Subclip Callback
//
// Parameter:
// - subclip: object that represents the subclip (output contract).
//
// Returns: a Promise object that, when resolved, returns true if the operation was accept in the back-end; otherwise, returns false.
var onSubmitSubclip = function (subclip) {
    var promise = new Promise(function (resolve, reject) {
        // TODO: perform the back-end AJAX request to submit the subclip job.
        var result = true;

        if (/* everything turned out fine */ result) {
            resolve(result);
        }
        else {
            reject(Error("error details"));
        }
    });

    return promise;
};

var subclipper = new subclipper({
    selector: '#root',
    restVersion: '2.0',
    submitSubclipCallback: onSubmitSubclip,
});
```
Utdata från jobb sändningen är antingen den Media Encoder Standard kodnings inställningen för återgivna jobb eller REST API nytto last för dynamiska manifest filter.

## <a name="submitting-encoding-job-to-create-video"></a>Skickar kodnings jobb för att skapa video
Du kan skicka ett Media Encoder Standard encoding-jobb för att skapa ett inramat video klipp. Kodnings jobb producerar åter givning av videor, en ny fragmenterad MP4-fil.

Jobbets utgående kontrakt för renderat Urklipp är ett JSON-objekt med följande egenskaper:

```json
{
  /* Required */
  "name": "My New Subclip Output Name",

  /* Required: string specifying the format of the Json file.
   */
  /* NOTE: The REST API version, currently 2.0 is supported
   */
  "restVersion": "2.0"

  /* Required: array containing all the input Ids (both "asset" or "filter" type) used in the subclipper timeline;
  it must contain at least one item. */
  /* NOTE: when "output"."type" === "job", all items must match the "inputsIds"[i]."type" === "asset" condition;
  this array can be used in the back-end to get the input assets for the subclipping job with the Media Encoder Standard (MES) processor. */
  /* NOTE: when "output"."type" === "filter", there must be a single item in the array ("inputsIds".length === 1)
  that can be used in the back-end to get the source for the dynamic manifest filter (create or update). */
  "inputsIds": [{
    /* Required */
    "id": "my-asset-id-1",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }, {
    /* Required */
    "id": "my-asset-id-2",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }],

  /* Required */
  "output": {

    /* Required: must be one of the following values: "job" or "filter" */
    "type": "job",

    /* Required if "type" === "job" */
    /* NOTE: This is the preset for the Media Encoder Standard (MES) processor that can be used in the back-end to submit the subclip job.
    The encoding profile ("Codecs" property) depends on the "singleBitrateMp4Profile" and "multiBitrateMp4Profile" option parameters
    specified when creating the widget instance. */
    /* REFERENCE: https://docs.microsoft.com/azure/media-services/media-services-advanced-encoding-with-mes */
    "job": {
      "Version": 1.0,
      "Codecs": [{
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": true,
          "H264Layers": [{
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
          }],
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
      "Outputs": [{
        "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
        "Format": {
          "Type": "MP4Format"
        }
      }],
      "Sources": [{
        "AssetId": "my-asset-id-1",
        "StartTime": "0.00:01:15.000",
        "Duration": "0.00:00:25.000"
      }, {
        "AssetId": "my-asset-id-2",
        "StartTime": "0.00:20:04.000",
        "Duration": "0.00:33:07.500"
      }]
    }
  }
}
```

Om du vill utföra kodnings jobbet skickar du Media Encoder Standard encoding-jobbet med tillhör Ande för inställning. Se den här artikeln för information om hur du skickar kodnings jobb med [.NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-encode-with-media-encoder-standard) eller [REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-encode-asset).

## <a name="quickly-creating-video-clips-without-encoding"></a>Skapa video klipp snabbt utan kodning
Alternativt för att skapa ett kodnings jobb kan du använda Azure Media Clipper för att skapa dynamiska manifest filter. Filter kräver inte kodning och kan skapas snabbt eftersom en ny till gång inte skapas. Utmatnings kontraktet för ett filter Urklipp är ett JSON-objekt med följande egenskaper:

```json
{
  /* Required: Filter name (Alphanumeric characters and hyphens only, no whitespace) */
  "name": "FilterName",

  /* Required: string specifying the format of the Json file.
   */
  /* NOTE: This subclipper version always returns '2.0' in this field.
   */
  "restVersion": "2.0"

  /* Required: array containing all the input Ids (both "asset" or "filter" type) used in the subclipper timeline;
  it must contain at least one item. */
  /* NOTE: when "output"."type" === "job", all items must match the "inputsIds"[i]."type" === "asset" condition;
  this array can be used in the back-end to get the input assets for the subclipping job with the Media Encoder Standard (MES) processor. */
  /* NOTE: when "output"."type" === "filter", there must be a single item in the array ("inputsIds".length === 1)
  that can be used in the back-end to get the source for the dynamic manifest filter (create or update). */
  "inputsIds": [{
    /* Required */
    "id": "my-asset-id",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }],

  /* Required */
  "output": {

    /* Required: must be one of the following values: "job" or "filter" */
    "type": "filter",

    /* Required if "type" === "filter" */
    /* REFERENCE: https://docs.microsoft.com/rest/api/media/operations/assetfilter */
    "filter": {
      "PresentationTimeRange": {
        "StartTimestamp": "340000000",
        "EndTimestamp": "580000000",
        "Timescale": "10000000"
      },
      "Tracks": [{
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "video",
          "Operator": "Equal"
        }]
      }, {
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "audio",
          "Operator": "Equal"
        }, {
          "Property": "Name",
          "Value": "audio-track-1",
          "Operator": "Equal"
        }]
      }, {
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "text",
          "Operator": "Equal"
        }, {
          "Property": "Language",
          "Value": "en",
          "Operator": "Equal"
        }]
      }]
    }
  }
}
```

Skicka ett REST-anrop för att skapa ett dynamiskt manifest filter genom att skicka den associerade filter nytto lasten med hjälp av [REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest).
