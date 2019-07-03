---
title: Hur du skapar en datauppsättning för träning för en anpassad modell - formuläret Igenkännande
titleSuffix: Azure Cognitive Services
description: Lär dig att se till att din datauppsättning för träning är optimerad för att träna en modell för formuläret Igenkännande.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: ad9bba53390e3c4262f999ebcc57ab354f1e3d69
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537621"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Skapa en datauppsättning för träning för en anpassad modell

När du använder formuläret Igenkännande anpassade modellen kan ange du dina egna utbildningsdata så att modellen kan träna dina branschspecifika former. Du kan träna en modell med fem fyllts i formulär eller ett tomt formulär (du måste inkludera ordet ”tom” i filnamnet) plus två fyllts i formulär. Även om du har tillräckligt med fyllts i formulär för att träna med kan att lägga till ett tomt formulär i datauppsättningen utbildning förbättra modellen.

## <a name="training-data-tips"></a>Tips för utbildning-data

Det är viktigt att du använder en datauppsättning som är optimerad för utbildning. Använd följande tips för att säkerställa att du får bästa resultat från den [Träningsmodell](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel) igen:

* Använd om möjligt textbaserade PDF-dokument i stället för avbildningsbaserad dokument. Genomsökt PDF-filer hanteras som bilder.
* Använd ett tomt formulär och två ifyllda formulär om du har dem tillgängliga.
* Använd exemplen som har alla deras fält som fyllts för fyllts i formulär.
* Använd formulär med olika värden i varje fält.
* Om dina formulär-avbildningar har lägre kvalitet, använda en större mängd data (till exempel 10 – 15 avbildningar).

## <a name="general-input-requirements"></a>Allmänna krav för indata

Kontrollera att din datauppsättning för träning också följer inkommande kraven för allt innehåll i formuläret Igenkännande. 

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="upload-your-training-data"></a>Ladda upp dina utbildningsdata

När du har samlat uppsättning formuläret dokument som du vill använda för träning, måste du överföra den till en Azure blob storage-behållare. Om du inte vet hur du skapar ett Azure storage-konto med en behållare, efter den [Azure Storage-Snabbstart för Azure-portalen](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

### <a name="organize-your-data-in-subfolders-optional"></a>Ordna data i undermappar (valfritt)

Som standard den [Träningsmodell](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel) API kommer endast att använda formatet dokument som finns i roten av din lagringsbehållare. Du kan dock träna med data i undermappar om du anger den i API-anrop. Normalt brödtexten i den [Träningsmodell](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel) anropet har följande format, där `<SAS URL>` är URL: en för delad åtkomst signaturen för din behållare:

```json
{
  "source":"<SAS URL>"
}
```

Om du lägger till följande innehåll i begärandetexten kommer API: et träna med dokument som finns i undermappar. Den `"prefix"` fältet är valfritt och begränsar data träningsmängden till filer vars sökvägar som börjar med den angivna strängen. Så värdet `"Test"`, till exempel leder till API för att titta på endast de filer eller mappar som börjar med ordet ”Test”.

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  }
}
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar en datauppsättning för träning, följa en Snabbstart för att träna en modell med anpassade formulär Igenkännande och börja använda det på dina formulär.

* [Snabbstart: Träna en modell och extrahera formulärdata med hjälp av cURL](./quickstarts/curl-train-extract.md)
* [Snabbstart: Träna en modell och extrahera formulärdata med hjälp av REST-API med Python](./quickstarts/python-train-extract.md)

