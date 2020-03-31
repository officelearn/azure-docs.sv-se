---
title: Koda anpassad transformering med Media Services v3 REST - Azure | Microsoft-dokument
description: Det här avsnittet visar hur du använder Azure Media Services v3 för att koda en anpassad transformering med REST.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/14/2019
ms.author: juliako
ms.openlocfilehash: 30e22cb786e5dc2a667fe41ca8edf398cf0b7613
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "65761796"
---
# <a name="how-to-encode-with-a-custom-transform---rest"></a>Så här kodar du med en anpassad transformering - REST

När du kodar med Azure Media Services kan du komma igång snabbt med en av de rekommenderade inbyggda förinställningarna, baserat på branschens bästa praxis, vilket visas i självstudiekursen [för strömmande filer.](stream-files-tutorial-with-rest.md#create-a-transform) Du kan också skapa en anpassad förinställning för att rikta in dig på ditt specifika scenario eller enhetskrav.

## <a name="considerations"></a>Överväganden

När du skapar anpassade förinställningar gäller följande överväganden:

* Alla värden för höjd och bredd på AVC-innehåll måste vara en multipel av 4.
* I Azure Media Services v3 är alla kodningbithastigheter i bitar per sekund. Detta skiljer sig från förinställningar med våra v2 API: er, som använde kilobit / sekund som enhet. Om bithastigheten i v2 till exempel angavs som 128 (kilobit/sekund) i v3 skulle den vara inställd på 128000 (bitar/sekund).

## <a name="prerequisites"></a>Krav 

- [Skapa ett Media Services-konto](create-account-cli-how-to.md). <br/>Se till att komma ihåg resursgruppsnamnet och mediatjänstkontonamnet. 
- [Konfigurera Postman för REST API-anrop för Azure Media Services](media-rest-apis-with-postman.md).<br/>Se till att följa det sista steget i avsnittet [Hämta Azure AD-token](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-custom-preset"></a>Definiera en anpassad förinställning

I följande exempel definieras begäranden för en ny transformering. Vi definierar en uppsättning utdata som vi vill ska genereras när den här transformeringen används. 

I det här exemplet lägger vi först till ett AacAudio-lager för ljudkodning och två H264Video-lager för videokodningen. I videolagren tilldelar vi etiketter så att de kan användas i utdatafilnamnen. Därefter vill vi att utdata även ska innehålla miniatyrer. I exemplet nedan anger vi bilder i PNG-format, genererade med 50 % av upplösningen på indatavideon och vid tre tidsstämplar - {25 %, 50 %, 75} av indatavideons längd. Slutligen anger vi formatet för utdatafilerna - en för video + ljud och en annan för miniatyrerna. Eftersom vi har flera H264Layers, måste vi använda makron som producerar unika namn per lager. Vi kan antingen `{Label}` `{Bitrate}` använda ett eller makro, visar exemplet den förra.

```json
{
    "properties": {
        "description": "Basic Transform using a custom encoding preset",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
                    "codecs": [
                        {
                            "@odata.type": "#Microsoft.Media.AacAudio",
                            "channels": 2,
                            "samplingRate": 48000,
                            "bitrate": 128000,
                            "profile": "AacLc"
                        },
                        {
                            "@odata.type": "#Microsoft.Media.H264Video",
                            "keyFrameInterval": "PT2S",
                            "stretchMode": "AutoSize",
                            "sceneChangeDetection": false,
                            "complexity": "Balanced",
                            "layers": [
                                {
                                    "width": "1280",
                                    "height": "720",
                                    "label": "HD",
                                    "bitrate": 3400000,
                                    "maxBitrate": 3400000,
                                    "bFrames": 3,
                                    "slices": 0,
                                    "adaptiveBFrame": true,
                                    "profile": "Auto",
                                    "level": "auto",
                                    "bufferWindow": "PT5S",
                                    "referenceFrames": 3,
                                    "entropyMode": "Cabac"
                                },
                                {
                                    "width": "640",
                                    "height": "360",
                                    "label": "SD",
                                    "bitrate": 1000000,
                                    "maxBitrate": 1000000,
                                    "bFrames": 3,
                                    "slices": 0,
                                    "adaptiveBFrame": true,
                                    "profile": "Auto",
                                    "level": "auto",
                                    "bufferWindow": "PT5S",
                                    "referenceFrames": 3,
                                    "entropyMode": "Cabac"
                                }
                            ]
                        },
                        {
                            "@odata.type": "#Microsoft.Media.PngImage",
                            "stretchMode": "AutoSize",
                            "start": "25%",
                            "step": "25%",
                            "range": "80%",
                            "layers": [
                                {
                                    "width": "50%",
                                    "height": "50%"
                                }
                            ]
                        }
                    ],
                    "formats": [
                        {
                            "@odata.type": "#Microsoft.Media.Mp4Format",
                            "filenamePattern": "Video-{Basename}-{Label}-{Bitrate}{Extension}",
                            "outputFiles": []
                        },
                        {
                            "@odata.type": "#Microsoft.Media.PngFormat",
                            "filenamePattern": "Thumbnail-{Basename}-{Index}{Extension}"
                        }
                    ]
                }
            }
        ]
    }
}

```

## <a name="create-a-new-transform"></a>Skapa en ny transformering  

I det här exemplet skapar vi en **transformering** som baseras på den anpassade förinställning som vi definierade tidigare. När du skapar en transformering bör du först använda [Hämta](https://docs.microsoft.com/rest/api/media/transforms/get) för att kontrollera om det redan finns någon. Om transformeringen finns kan du återanvända den. 

I postmanssamlingen som du hämtade väljer du **Transformer och Jobb**->**Skapa eller Uppdatera transformering**.

Metoden **PUT** HTTP-begäran liknar:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
```

Välj fliken **Brödtext** och ersätt brödtexten med den json-kod som du [definierade tidigare](#define-a-custom-preset). För att Media Services ska kunna tillämpa transformeringen på den angivna videon eller ljudet måste du skicka ett jobb under transformeringen.

Välj **Skicka**. 

För att Media Services ska kunna tillämpa transformeringen på den angivna videon eller ljudet måste du skicka ett jobb under transformeringen. Ett komplett exempel som visar hur du skickar ett jobb under en transformering finns i [Självstudiekurs: Strömma videofiler - REST](stream-files-tutorial-with-rest.md).

## <a name="next-steps"></a>Nästa steg

Se [andra REST-operationer](https://docs.microsoft.com/rest/api/media/)
