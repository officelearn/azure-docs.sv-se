---
title: Definiera och använda innehåll arbetsflöden via granskningsverktyget - Content Moderator
titlesuffix: Azure Cognitive Services
description: Du kan använda Azure Content Moderator Arbetsflödesdesignern för att definiera anpassade arbetsflöden och tröskelvärden baserat på dina principer för innehåll.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 04/04/2019
ms.author: sajagtap
ms.openlocfilehash: 006f7d6691b8872aaa7ff8ccacff484585761d00
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61271180"
---
# <a name="define-and-use-moderation-workflows"></a>Definiera och Använd moderering arbetsflöden

I den här guiden kommer du lära dig hur du konfigurerar och använder [arbetsflöden](../review-api.md#workflows) på den [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com) webbplats. Arbetsflöden är molnbaserade anpassade filter som du kan använda för att hantera innehåll på ett mer effektivt. Arbetsflöden kan ansluta till en mängd olika tjänster för att filtrera innehåll på olika sätt och vidta lämplig åtgärd. Den här guiden visar hur du använder Content Moderator-anslutningen (som ingår som standard) för att filtrera innehåll och ställa in mänsklig granskning i ett scenario med vanliga moderering.

## <a name="create-a-new-workflow"></a>Skapa ett nytt arbetsflöde

Gå till den [Content Moderator-granskningsverktyget](https://contentmoderator.cognitive.microsoft.com/) och logga in. På den **inställningar** fliken **arbetsflöden**.

![Arbetsflöden som inställning](images/2-workflows-0.png)

På nästa skärm väljer **lägga till arbetsflödet**.

![Lägg till ett arbetsflöde](images/2-workflows-1.png)

### <a name="assign-a-name-and-description"></a>Tilldela ett namn och beskrivning

Namnge ditt arbetsflöde, ange en beskrivning och välj om arbetsflödet ska hantera bilder eller text.

![Arbetsflödesnamn och beskrivning](images/image-workflow-create.PNG)

### <a name="define-evaluation-criteria"></a>Definiera kriterier för utvärdering

På nästa skärm, går du till den **om** avsnittet. I den övre nedrullningsbara menyn väljer du **villkor**. Detta kan du konfigurera det villkor som arbetsflödet ska utföra åtgärder. Om du vill använda flera villkor väljer **kombination** i stället. 

Välj sedan en koppling. Det här exemplet används **Content Moderator**. Beroende på den koppling som du väljer får du olika alternativ för utdata. Se den [kopplingar](./configure.md#connectors) i inställningar-guiden granska verktyget för att lära dig hur du ställer in andra anslutningsappar.

![Välj för Arbetsflödeskoppling](images/image-workflow-connect-to.PNG)

Välj önskad utdata du använder och ange villkor för att kontrollera att den mot.

![Definiera arbetsflödesvillkor](images/image-workflow-condition.PNG)

### <a name="define-the-action"></a>Definiera åtgärden

Gå till den **sedan** avsnitt, där du kan välja en åtgärd. I följande exempel skapar en avbildning granskning och tilldelar en tagg. Du kan också lägga till en alternativ sökväg (annars) och anger en åtgärd för att även.

![Definiera arbetsflödesåtgärden](images/image-workflow-action.PNG)

### <a name="save-the-workflow"></a>Spara arbetsflödet

Obs Arbetsflödesnamnet; Du behöver namnet som ska starta ett jobb för moderering med arbetsflödet API (se nedan). Slutligen kan spara arbetsflödet med hjälp av den **spara** längst upp på sidan.

## <a name="test-the-workflow"></a>Testa arbetsflödet

Nu när du har definierat ett anpassat arbetsflöde kan du testa den innehåll. Gå till **arbetsflöden** och välj sedan motsvarande **köra arbetsflödet** knappen.

![Arbetsflödet test](images/image-workflow-execute.PNG)

Spara den [exempelbild](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg) till din lokala enhet. Välj sedan **väljer fil(er)** och överför avbildningen till arbetsflödet.

![En runner med en offert ovanpå avbildningen](images/sample-text.jpg)

### <a name="track-progress"></a>Spåra förlopp

Du kan visa förloppet för arbetsflödet i nästa popup-fönstret.

![Spåra arbetsflödeskörning](images/image-workflow-job.PNG)

### <a name="verify-workflow-action"></a>Kontrollera arbetsflödesåtgärden

Gå till den **bild** fliken **granska** och kontrollera att det finns en granskning av nyligen skapade avbildningen.

![Granska bilder](images/image-workflow-review.PNG)

## <a name="next-steps"></a>Nästa steg

I den här guiden beskrivs hur du konfigurerar och använder moderering arbetsflöden från Content Moderator [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com). Sedan kan du se den [REST API-guiden](../try-review-api-workflow.md) att lära dig hur du skapar arbetsflöden programmässigt.
