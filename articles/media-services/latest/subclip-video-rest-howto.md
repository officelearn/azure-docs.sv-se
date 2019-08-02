---
title: Under klipp en video vid kodning med Azure Media Services REST API
description: I det här avsnittet beskrivs hur du klipper en video när du kodar med Azure Media Services med hjälp av REST
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/10/2019
ms.author: juliako
ms.openlocfilehash: 3557aef6213955ef77542bffafe0a2b0c374ed68
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704452"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>Under klipp en video vid kodning med Media Services REST

Du kan trimma eller under klipp en video när du kodar den med ett [jobb](https://docs.microsoft.com/rest/api/media/jobs). Den här funktionen fungerar med [](https://docs.microsoft.com/rest/api/media/transforms) alla transformeringar som har skapats med antingen [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) för inställningar eller [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) för inställningar. 

RESTEN av exemplet i det här avsnittet skapar ett jobb som rensar en video när den skickar ett kodnings jobb. 

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen som beskrivs i det här avsnittet måste ha du till:

- [Skapa ett Azure Media Services-konto](create-account-cli-how-to.md).
- [Konfigurera Postman för Azure Media Services REST API-anrop](media-rest-apis-with-postman.md).
    
    Kontrol lera att du följer det sista steget i avsnittet [Hämta Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token)-token. 
- Skapa en transformering och en utmatnings till gångar. Du kan se hur du skapar en transformering och en utmatnings till gångar i [koda en fjärrfil baserat på URL och strömma video rest-](stream-files-tutorial-with-rest.md) kursen.
- Läs avsnittet [encoding Concept](encoding-concept.md) .

## <a name="create-a-subclipping-job"></a>Skapa ett under urklipps jobb

1. I Postman-samlingen som du laddade ned väljer du **transformeringar och jobb** -> **skapa jobb med under Urklipp**.
    
    **Placerings** förfrågan ser ut så här:
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. Uppdatera värdet för miljövariabeln "transformName" med Transformations namnet. 
1. Välj fliken **brödtext** och uppdatera "myOutputAsset" med namnet på din utmatnings till gång.

    ```
    {
      "properties": {
        "description": "A Job with transform cb9599fb-03b3-40eb-a2ff-7ea909f53735 and single clip.",
       
        "input": {
          "@odata.type": "#Microsoft.Media.JobInputHttp",
          "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
          "files": [
                "Ignite-short.mp4"
            ],
          "start": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT10S"
          },
          "end": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT40S"
          }
        },
      
        "outputs": [
          {
            "@odata.type": "#Microsoft.Media.JobOutputAsset",
            "assetName": "myOutputAsset"
          }
        ],
        "priority": "Normal"
      }
    }
    ```
1. Tryck på **Skicka**.

    Du kan se **svaret** med information om jobbet som skapades och skickades och jobbets status. 

## <a name="next-steps"></a>Nästa steg

[Koda med en anpassad transformering](custom-preset-rest-howto.md) 
