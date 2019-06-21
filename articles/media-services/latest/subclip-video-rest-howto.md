---
title: Underklipp en video när kodning med Azure Media Services REST API
description: Det här avsnittet beskriver hur du underklipp en video när kodning med Azure Media Services med hjälp av REST
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
ms.openlocfilehash: df8c8a4040b4aae4379b4bfe0e9a16337588dd1b
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305203"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>Underklipp en video när encoding med Media Services - REST

Du kan trimma eller en video på underklipp när du kodar den med hjälp av en [jobbet](https://docs.microsoft.com/rest/api/media/jobs). Den här funktionen fungerar med alla [transformera](https://docs.microsoft.com/rest/api/media/transforms) som skapats med hjälp av antingen den [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) förinställningar, eller [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) förinställningar. 

REST-exempel i det här avsnittet skapas ett jobb som tar bort en video som skickar den ett kodningsjobb. 

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra stegen som beskrivs i det här avsnittet måste ha du till:

- [Skapa ett Azure Media Services-konto](create-account-cli-how-to.md).
- [Konfigurera Postman för Azure Media Services REST API-anrop](media-rest-apis-with-postman.md).
    
    Se till att följa det sista steget i avsnittet [hämta Azure AD Token](media-rest-apis-with-postman.md#get-azure-ad-token). 
- Skapa en transformering och utdata tillgångar. Du kan se hur du skapar en transformering och utdata tillgångar i den [koda en fjärrfil baserat på URL: en och strömma video – REST](stream-files-tutorial-with-rest.md) självstudien.
- Granska den [Encoding konceptet](encoding-concept.md) avsnittet.

## <a name="create-a-subclipping-job"></a>Skapa ett underklippning-jobb

1. Välj i Postman-samling som du laddade ned **transformeringar och jobb** -> **skapa jobb med Sub avklippta**.
    
    Den **PLACERA** begäran som ser ut så här:
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. Uppdatera värdet för ”transformName” miljövariabeln med namnet på din transformeringen. 
1. Välj den **brödtext** fliken och uppdatera ”myOutputAsset” med dina utdata Certifikattillgångens namn.

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

    Du ser den **svar** med information om ett jobb som har skapats och har skickats och jobbets status. 

## <a name="next-steps"></a>Nästa steg

[Koda med en anpassad transformering](custom-preset-rest-howto.md) 
