---
title: Skapa en tränings data uppsättning för en anpassad modell för formulär igenkänning
titleSuffix: Azure Cognitive Services
description: Lär dig hur du ser till att din tränings data uppsättning är optimerad för att träna en formulär igenkännings modell.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: da9445b12ce6f35d249fc3af1a4a0ef560ba35de
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87905099"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Skapa en tränings data uppsättning för en anpassad modell

När du använder den anpassade modellen för formulär igenkänning kan du ange dina egna utbildnings data så att modellen kan träna till dina branschspecifika formulär. 

Om du är utbildning utan manuella etiketter kan du använda fem ifyllda formulär eller ett tomt formulär (du måste inkludera ordet "tomt" i fil namnet) plus två ifyllda formulär. Även om du har tillräckligt med ifyllda formulär kan du förbättra modellens precision genom att lägga till ett tomt formulär i din tränings data uppsättning.

Om du vill använda manuellt märkta utbildnings data måste du börja med minst fem ifyllda formulär av samma typ. Du kan fortfarande använda omärkta formulär och ett tomt formulär utöver den obligatoriska data uppsättningen.

## <a name="training-data-tips"></a>Tips om tränings data

Det är viktigt att använda en data uppsättning som är optimerad för utbildning. Använd följande tips för att se till att du får bästa möjliga resultat från åtgärden [träna anpassad modell](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) :

* Använd om möjligt textbaserade PDF-dokument i stället för bildbaserade dokument. Skannade PDF-filer hanteras som bilder.
* För ifyllda formulär använder du exempel som har alla sina fält ifyllda.
* Använd formulär med olika värden i varje fält.
* Om dina formulär bilder är av lägre kvalitet kan du använda en större data uppsättning (till exempel 10-15 bilder).
* Den totala storleken på tränings data uppsättningen kan vara upp till 500 sidor.

## <a name="general-input-requirements"></a>Allmänna ingångs krav

Se till att din tränings data uppsättning också följer inmatnings kraven för all formulär tolknings innehåll. 

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="upload-your-training-data"></a>Ladda upp dina utbildnings data

När du har lagt samman uppsättningen med formulär dokument som du ska använda för utbildning måste du ladda upp den till en Azure Blob Storage-behållare. Om du inte vet hur du skapar ett Azure Storage-konto med en behållare följer du [Azure Storage snabb start för Azure Portal](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). Använd standard prestanda nivån.

Om du vill använda manuellt märkta data måste du också ladda upp *.labels.js* och *.ocr.jspå* filer som motsvarar dina utbildnings dokument. Du kan använda [exempel etikett verktyget](./quickstarts/label-tool.md) (eller ditt eget användar gränssnitt) för att generera dessa filer.

### <a name="organize-your-data-in-subfolders-optional"></a>Organisera dina data i undermappar (valfritt)

Som standard använder [träna anpassad modell](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) -API bara formulär dokument som finns i roten för din lagrings behållare. Du kan dock träna med data i undermappar om du anger den i API-anropet. Normalt har själva bröd texten i det [anpassade modell](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) anropet följande format, där `<SAS URL>` är URL: en för signaturen för delad åtkomst för din behållare:

```json
{
  "source":"<SAS URL>"
}
```

Om du lägger till följande innehåll i begär ande texten tränar API: n med dokument som finns i undermappar. `"prefix"`Fältet är valfritt och begränsar tränings data uppsättningen till filer vars sökvägar börjar med den angivna strängen. Värdet `"Test"` , till exempel, kommer att orsaka att API: t bara tittar på de filer eller mappar som börjar med ordet "test".

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

Nu när du har lärt dig hur du skapar en tränings data uppsättning följer du en snabb start för att träna en anpassad formulär igenkännings modell och börja använda den i formulären.

* [Träna en modell och extrahera formulär data med hjälp av sväng](./quickstarts/curl-train-extract.md)
* [Träna en modell och extrahera formulär data med hjälp av REST API och python](./quickstarts/python-train-extract.md)
* [Träna med etiketter med hjälp av verktyget för exempel märkning](./quickstarts/label-tool.md)
* [Träna med etiketter med hjälp av REST API och Python](./quickstarts/python-labeled-data.md)
