---
title: Koda anpassad transformering med Media Services v3 REST – Azure | Microsoft Docs
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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2020
ms.locfileid: "65761796"
---
# <a name="how-to-encode-with-a-custom-transform---rest"></a>Koda med en anpassad transformering-REST

När du kodar med Azure Media Services kan du snabbt komma igång med en av de rekommenderade inbyggda för inställningarna, baserat på bransch bästa praxis, som du ser i själv studie kursen för [strömnings filer](stream-files-tutorial-with-rest.md#create-a-transform) . Du kan också bygga en anpassad för inställning för att rikta in dig på specifika scenario-eller enhets krav.

## <a name="considerations"></a>Överväganden

När du skapar anpassade för inställningar gäller följande aspekter:

* Alla värden för höjd och bredd på AVC-innehåll måste vara en multipel av 4.
* I Azure Media Services v3 är alla kodnings bit hastigheter i bitar per sekund. Detta skiljer sig från för inställningarna med våra v2-API: er, som använde kilobit/sekund som enhet. Om bit hastigheten i v2 exempelvis angavs som 128 (kilobit/sekund), skulle den vara inställd på 128000 (bitar/sekund) i v3.

## <a name="prerequisites"></a>Krav 

- [Skapa ett Media Services-konto](create-account-cli-how-to.md). <br/>Kom ihåg att komma ihåg resurs gruppens namn och namnet på Media Services kontot. 
- [Konfigurera PostMan för Azure Media Services REST API-anrop](media-rest-apis-with-postman.md).<br/>Kontrol lera att du följer det sista steget i avsnittet [Hämta Azure AD-token](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-custom-preset"></a>Definiera en anpassad för inställning

I följande exempel definieras begär ande texten för en ny transformering. Vi definierar en uppsättning utdata som vi vill ska genereras när denna transformering används. 

I det här exemplet lägger vi först till ett AacAudio-lager för ljud kodningen och två H264Video-lager för video kodningen. I video lager tilldelar vi etiketter så att de kan användas i utdatafilernas namn. Nu vill vi att utdata även ska innehålla miniatyrer. I exemplet nedan anger vi bilder i PNG-format, genererade med 50% av upplösningen för Indataporten och vid tre tidsstämplar – {25%, 50%, 75} av den angivna videons längd. Slutligen anger vi formatet för utdatafilerna – ett för video + ljud och en annan för miniatyr bilderna. Eftersom vi har flera H264Layers måste vi använda makron som producerar unika namn per lager. Vi kan antingen använda ett `{Label}` eller `{Bitrate}` -makro, exemplet visar det tidigare.

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

I det här exemplet skapar vi en **transformering** som baseras på den anpassade för inställning som vi definierade tidigare. När du skapar en transformering bör du först använda [Get](https://docs.microsoft.com/rest/api/media/transforms/get) för att kontrol lera om det redan finns en. Om transformeringen finns kan du använda den igen. 

I Postman-samlingen som du laddade ned väljer du transformeringar **och jobb**->**skapa eller uppdatera transformering**.

Metoden för att **Skicka** http-begäran liknar:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
```

Välj fliken **brödtext** och Ersätt texten med den JSON-kod som du [definierade tidigare](#define-a-custom-preset). För att Media Services ska kunna använda transformeringen på den angivna videon eller ljudet måste du skicka ett jobb under transformeringen.

Välj **Skicka**. 

För att Media Services ska kunna använda transformeringen på den angivna videon eller ljudet måste du skicka ett jobb under transformeringen. Ett komplett exempel som visar hur du skickar ett jobb under en transformering finns i [självstudie: strömma videofiler – rest](stream-files-tutorial-with-rest.md).

## <a name="next-steps"></a>Nästa steg

Se [andra rest-åtgärder](https://docs.microsoft.com/rest/api/media/)
