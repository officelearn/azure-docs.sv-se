---
title: Subclip en video vid kodning med Azure Media Services REST
description: I det här avsnittet beskrivs hur du underklipp en video när du kodar med Azure Media Services med REST
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
ms.openlocfilehash: c39aded55fe36cb130459a4f6f119f872b1adbc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514331"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>Subclip en video vid kodning med Media Services - REST

Du kan trimma eller klippa en video när du kodar den med hjälp av ett [jobb](https://docs.microsoft.com/rest/api/media/jobs). Den här funktionen fungerar med alla [transformeringar](https://docs.microsoft.com/rest/api/media/transforms) som skapas med hjälp av antingen [förinställningarna BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) eller [förinställningarna StandardEncoderPreset.](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) 

I avsnittet REST i det här avsnittet skapas ett jobb som trimmar en video när den skickar ett kodningsjobb. 

## <a name="prerequisites"></a>Krav

För att kunna utföra stegen som beskrivs i det här avsnittet måste du:

- [Skapa ett Azure Media Services-konto](create-account-cli-how-to.md).
- [Konfigurera Postman för REST API-anrop för Azure Media Services](media-rest-apis-with-postman.md).
    
    Se till att följa det sista steget i avsnittet [Hämta Azure AD-token](media-rest-apis-with-postman.md#get-azure-ad-token). 
- Skapa en transformering och en utdatatillgångar. Du kan se hur du skapar en transformering och en utdatatillgångar i [koda en fjärrfil baserat på URL och strömma videon -](stream-files-tutorial-with-rest.md) REST-självstudien.
- Gå igenom [begreppsämnet Kodning.](encoding-concept.md)

## <a name="create-a-subclipping-job"></a>Skapa ett underklippsjobb

1. I postmansamlingen som du hämtade väljer du **Omvandlar och jobb** -> **Skapa jobb med underurklipp**.
    
    **PUT-begäran** ser ut så här:
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. Uppdatera värdet för miljövariabeln "transformName" med ditt transformeringsnamn. 
1. Välj fliken **Brödtext** och uppdatera "myOutputAsset" med ditt utdatatillgångsnamn.

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

    Du ser **svar med** information om jobbet som skapades och skickades och jobbets status. 

## <a name="next-steps"></a>Nästa steg

[Så här kodar du med en anpassad transformering](custom-preset-rest-howto.md) 
