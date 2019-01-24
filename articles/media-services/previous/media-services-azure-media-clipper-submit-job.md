---
title: Skicka urklippningsjobb för Azure Media Clipper | Microsoft Docs
description: Steg för att skicka urklippningsjobb från Azure Media Clipper
services: media-services
keywords: Clip, underklipp, kodning, media
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 0894c3677b87fe48c130d648253dadd0d43429f4
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54821455"
---
# <a name="submit-clipping-jobs-from-azure-media-clipper"></a>Skicka urklippningsjobb från Azure Media Clipper
Azure Media Clipper kräver en **submitSubclipCallback** metoden implementeras för att hantera urklippet jobböverföring. Den här funktionen är för att implementera en HTTP POST Clipper utdata till en webbtjänst. Den här webbtjänsten är där du kan skicka kodningsjobbet. Utdata från Clipper är antingen en Media Encoder Standard kodning förinställning för renderade jobb eller REST API-nyttolasten för dynamiskt manifestfilter-anrop. Den här direkt modellen är nödvändigt eftersom autentiseringsuppgifter för media services-konto inte är säkra i klientens webbläsare.

Följande sekvensdiagram illustrerar arbetsflödet mellan webbläsarklienten, din webbtjänst och Azure Media Services: ![Azure Media Clipper sekvensdiagram](media/media-services-azure-media-clipper-submit-job/media-services-azure-media-clipper-sequence-diagram.PNG)

I det föregående diagrammet fyra entiteterna är: slutanvändarens webbläsare, webbtjänsten, CDN-slutpunkten som är värd för Clipper resurser och Azure Media Services. När användaren navigerar till webbsidan, hämtar sidan Clipper JavaScript och CSS-resurser från värdbaserade CDN-slutpunkten. Användaren konfigurerar urklippet jobbet eller dynamiskt manifestfilter skapa anrop från webbläsaren. När användaren skickar jobbet eller filter skapa anropet kan placeras webbläsaren jobbet nyttolasten till en webbtjänst som du behöver distribuera. Den här webbtjänsten slutligen skickar jobbet urklippet eller filter skapa anrop till Azure Media Services med hjälp av media services-autentiseringsuppgifter.

Följande kodexempel illustrerar ett exempel **submitSubclipCallback** metod. I den här metoden kan implementera du HTTP POST av Media Encoder Standard förinställningen för kodningen. Om INLÄGGET lyckades (**resultatet**), **löftet** är löst, annars avvisades med felinformation.

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
Utdata från skikca in jobb är antingen Media Encoder Standard förinställningen för kodningen för renderade jobbet eller REST API-nyttolast för dynamiska manifestfilter.

## <a name="submitting-encoding-job-to-create-video"></a>Skicka kodningsjobb skapa video
Du kan skicka en Media Encoder Standard kodningsjobb för att skapa ett frame exakta videoklipp. Kodning jobb producerar återges videor, fragmenterad en ny MP4-fil.

Jobbet utdata kontraktet för renderade urklippet är ett JSON-objekt med följande egenskaper:

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

Kodningsjobb med den associerade förinställning för att utföra kodningsjobbet skicka Media Encoder Standard. Se nedan för information om hur du överför kodning jobb med den [.NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-encode-with-media-encoder-standard) eller [REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-encode-asset).

## <a name="quickly-creating-video-clips-without-encoding"></a>Skapa snabbt videoklipp utan kodning
Alternativ till att skapa ett kodningsjobb, kan du använda Azure Media Clipper för att skapa dynamiska manifestfilter. Filter kräver inte kodning och kan skapas snabbt när en ny tillgång inte skapas. Kontraktet utdata för ett filter urklippet är ett JSON-objekt med följande egenskaper:

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

Om du vill skicka ett REST-anrop för att skapa dynamiskt manifestfilter skicka den associerade filtret nyttolasten med hjälp av den [REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest).