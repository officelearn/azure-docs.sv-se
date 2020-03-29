---
title: Så här skapar du en utbildningsdatauppsättning för en anpassad modell - Formulär recognizeer
titleSuffix: Azure Cognitive Services
description: Lär dig hur du säkerställer att din träningsdatauppsättning är optimerad för att träna en formulärrekänningsmodell.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: 71ad7c5dd3ad74082da552cd3c45142bc0c2d624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75380634"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Skapa en utbildningsdatauppsättning för en anpassad modell

När du använder anpassad modell för formulärrecenser tillhandahåller du dina egna träningsdata så att modellen kan träna till dina branschspecifika formulär. Du kan träna en modell med fem ifyllda formulär eller ett tomt formulär (du måste inkludera ordet "tom" i filnamnet) plus två ifyllda formulär. Även om du har tillräckligt med ifyllda formulär att träna med kan du förbättra modellens noggrannhet genom att lägga till ett tomt formulär i din träningsdatauppsättning.

Om du vill använda manuellt märkta träningsdata bör du börja med minst fem formulär av samma typ. Du kan fortfarande använda omärkta formulär och ett tomt formulär i samma datauppsättning.

## <a name="training-data-tips"></a>Tips för träningsdata

Det är viktigt att använda en datauppsättning som är optimerad för träning. Använd följande tips för att se till att du får bästa resultat från driften av [Train Custom Model:](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)

* Använd om möjligt textbaserade PDF-dokument i stället för bildbaserade dokument. Skannade PDF-filer hanteras som bilder.
* För ifyllda formulär använder du exempel som har alla sina fält ifyllda.
* Använd formulär med olika värden i varje fält.
* Om dina formulärbilder har lägre kvalitet använder du en större datauppsättning (till exempel 10–15 bilder).
* Den totala storleken på utbildningsdatauppsättningen kan vara upp till 500 sidor.

## <a name="general-input-requirements"></a>Allmänna inmatningskrav

Se till att din träningsdatauppsättning också följer indatakraven för allt formulärankänningsinnehåll. 

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="upload-your-training-data"></a>Ladda upp dina träningsdata

När du har sammanställt uppsättningen formulärdokument som du ska använda för utbildning måste du ladda upp den till en Azure blob-lagringsbehållare. Om du inte vet hur du skapar ett Azure-lagringskonto med en behållare följer du [snabbstarten för Azure Storage för Azure-portalen](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

### <a name="organize-your-data-in-subfolders-optional"></a>Ordna dina data i undermappar (valfritt)

Som standard använder [API:et för anpassad tågmodell](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) endast formulärdokument som finns i roten på lagringsbehållaren. Du kan dock träna med data i undermappar om du anger det i API-anropet. Normalt har brödtexten i [tågets anpassade modellanrop](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) följande formulär, var `<SAS URL>` är signatur-URL:en för delad åtkomst för din behållare:

```json
{
  "source":"<SAS URL>"
}
```

Om du lägger till följande innehåll i begärandetexten tränas API:et med dokument som finns i undermappar. Fältet `"prefix"` är valfritt och begränsar träningsdatauppsättningen till filer vars sökvägar börjar med den angivna strängen. Så ett `"Test"`värde av , till exempel, kommer att orsaka API att titta på endast de filer eller mappar som börjar med ordet "Test".

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  },
  "useLabelFile": false
}
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar en träningsdatauppsättning följer du en snabbstart för att träna en anpassad Formulär recognizer-modell och börja använda den i formulären.

* [Snabbstart: Träna en modell och extrahera formulärdata med hjälp av cURL](./quickstarts/curl-train-extract.md)
* [Snabbstart: Träna en modell och extrahera formulärdata med REST API med Python](./quickstarts/python-train-extract.md)
* [Träna med etiketter med hjälp av REST API och Python](./quickstarts/python-labeled-data.md)