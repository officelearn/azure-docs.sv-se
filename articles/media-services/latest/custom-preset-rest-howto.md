---
title: Koda anpassade transformering med hjälp av Media Services v3 REST – Azure | Microsoft Docs
description: Det här avsnittet visar hur du använder Azure Media Services v3 att koda en anpassad transformering med hjälp av REST.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 03/12/2019
ms.author: juliako
ms.openlocfilehash: 5fe66634d4f95ba84d13a3d355f28afbedf0f7f0
ms.sourcegitcommit: 4133f375862fdbdec07b70de047d70c66ac29d50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/15/2019
ms.locfileid: "58012651"
---
# <a name="how-to-encode-with-a-custom-transform-by-using-rest"></a>Koda med en anpassad transformering med hjälp av REST

När kodning med Azure Media Services, du kan komma igång snabbt med en av de rekommenderade inbyggda förinställningar, baserat på branschens bästa praxis, som visas i den [Streaming filer](stream-files-tutorial-with-rest.md#create-a-transform) självstudien. Du kan också skapa en anpassad förinställning om du vill rikta in dina specifika krav för scenario eller enhet.

## <a name="considerations"></a>Överväganden

När du skapar anpassade förinställningar, gäller följande:

* Alla värden för höjd och bredd på AVC innehåll måste vara en multipel av 4.
* I Azure Media Services v3 är alla kodning bithastighet i bitar per sekund. Detta skiljer sig från förinställningar med våra v2 API: er, som används kilobit per sekund som enheten. Till exempel om bithastigheten i v2 har angetts som 128 (kbit/s) skulle i v3 det ställas in till 128000 (bitar per sekund).

## <a name="prerequisites"></a>Förutsättningar 

- [Skapa ett Media Services-konto](create-account-cli-how-to.md). <br/>Se till att komma ihåg resursgruppens namn och namnet på Media Services-konto. 
- [Konfigurera Postman för Azure Media Services REST API-anrop](media-rest-apis-with-postman.md).<br/>Se till att följa det sista steget i avsnittet [hämta Azure AD Token](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-custom-preset"></a>Definiera en anpassad förinställning

I följande exempel definierar begärandetexten för en ny omvandling. Vi definierar en uppsättning utdata som vi vill genereras när den här transformeringen används. 

I det här exemplet vi först lägga till ett AacAudio lager för ljud encoding och två H264Video lager för den videokodning. I video lager tilldela vi etiketter, så att de kan användas i utdata-filnamn. Nu ska vill vi utdata till att även omfatta miniatyrer. I exemplet nedan anger vi bilder i PNG-format, genereras på 50% av lösningen på indatavideon och på tre tidsstämplar - {25%, 50%, 75} med längden på indatavideon. Slutligen kan vi ange formatet för utdatafilerna – en för video och ljud, och en annan för miniatyrbilderna. Eftersom vi har flera H264Layers, måste vi använda makron som producerar unika namn per lager. Vi kan använda en `{Label}` eller `{Bitrate}` makro exemplet visar tidigare.

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
                            "filenamePattern": "Video-{Basename}-{Label}{Extension}",
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

## <a name="create-a-new-transform"></a>Skapa en ny omvandling  

I det här exemplet skapar vi en **transformera** som baseras på anpassad förinställning som vi definierade tidigare. När du skapar en transformering, bör du börja med [få](https://docs.microsoft.com/rest/api/media/transforms/get) att kontrollera om det redan finns. Om vi finns återanvända den. 

I den Postman-samling som du laddade ned, väljer **transformeringar och jobb**->**skapa eller uppdatera transformera**.

Den **PLACERA** HTTP-frågemetoden liknar:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
```

Välj den **brödtext** fliken och Ersätt innehållet med json kod som du [definierade tidigare](#define-a-custom-preset). Du måste skicka ett jobb under den transformeringen för Media Services att tillämpa transformering på angivna video eller ljud.

Välj **Skicka**. 

Du måste skicka ett jobb under den transformeringen för Media Services att tillämpa transformering på angivna video eller ljud. Ett komplett exempel som visar hur du skickar ett jobb under en transformering, se [självstudien: Stream videofiler - REST](stream-files-tutorial-with-rest.md).

## <a name="next-steps"></a>Nästa steg

Se [andra REST-åtgärder](https://docs.microsoft.com/rest/api/media/)
