---
title: Definiera och använda innehållsarbetsflöden via granskningsverktyget - Innehållsmoderator
titleSuffix: Azure Cognitive Services
description: Du kan använda arbetsflödesdesignern för Azure Content Moderator för att definiera anpassade arbetsflöden och tröskelvärden baserat på dina innehållsprinciper.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: pafarley
ms.openlocfilehash: 0be77dc1ac249c37f9b59078451d8fbe35f17458
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "72754242"
---
# <a name="define-and-use-moderation-workflows"></a>Definiera och använda modereringsarbetsflöden

I den här guiden får du lära dig hur du [konfigurerar](../review-api.md#workflows) och använder arbetsflöden på webbplatsen [för granskningsverktyg.](https://contentmoderator.cognitive.microsoft.com) Arbetsflöden är molnbaserade anpassade filter som du kan använda för att hantera innehåll mer effektivt. Arbetsflöden kan ansluta till en mängd olika tjänster för att filtrera innehåll på olika sätt och sedan vidta lämpliga åtgärder. Den här guiden visar hur du använder content moderator-kopplingen (som ingår som standard) för att filtrera innehåll och ställa in mänskliga granskningar i ett typiskt modereringsscenario.

## <a name="create-a-new-workflow"></a>Skapa ett nytt arbetsflöde

Gå till verktyget Granskning av [innehållsmodererator](https://contentmoderator.cognitive.microsoft.com/) och logga in. Välj **Arbetsflöden**på fliken **Inställningar** .

![Inställning av arbetsflöden](images/2-workflows-0.png)

På nästa skärm väljer du **Lägg till arbetsflöde**.

![Lägga till ett arbetsflöde](images/2-workflows-1.png)

### <a name="assign-a-name-and-description"></a>Tilldela ett namn och en beskrivning

Namnge arbetsflödet, ange en beskrivning och välj om arbetsflödet ska hantera bilder eller text.

![Arbetsflödesnamn och beskrivning](images/image-workflow-create.PNG)

### <a name="define-evaluation-criteria"></a>Definiera utvärderingskriterier

På nästa skärm går du till avsnittet **Om.** Välj **Villkor**i den övre rullgardinsmenyn . På så sätt kan du konfigurera villkoret som arbetsflödet ska vidta åtgärder för. Om du vill använda flera villkor väljer du **Kombination** i stället. 

Välj sedan en koppling. I det här exemplet används **Innehållsmoderator**. Beroende på vilken koppling du väljer får du olika alternativ för datautdata. Se avsnittet Kopplingar i guiden Granska [verktygsinställningar](./configure.md#connectors) om du vill veta hur du konfigurerar andra kopplingar.

![Välj arbetsflödeskoppling](images/image-workflow-connect-to.PNG)

Välj önskad utgång att använda och ställa in villkoren för att kontrollera den mot.

![Definiera arbetsflödesvillkor](images/image-workflow-condition.PNG)

### <a name="define-the-action"></a>Definiera åtgärden

Gå till avsnittet **Sedan,** där du väljer en åtgärd. I följande exempel skapas en bildgranskning och en tagg tilldelas. Du kan också lägga till en alternativ sökväg (Else) och ange en åtgärd även för det.

![Definiera arbetsflödesåtgärd](images/image-workflow-action.PNG)

### <a name="save-the-workflow"></a>Spara arbetsflödet

Observera arbetsflödesnamnet. Du behöver namnet för att starta ett modereringsjobb med arbetsflödes-API:et (se nedan). Spara slutligen arbetsflödet med knappen **Spara** högst upp på sidan.

## <a name="test-the-workflow"></a>Testa arbetsflödet

Nu när du har definierat ett anpassat arbetsflöde testar du det med exempelinnehåll. Gå till **Arbetsflöden** och välj motsvarande knappen **Kör arbetsflöde.**

![Arbetsflödestest](images/image-workflow-execute.PNG)

Spara den här [exempelbilden](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg) på den lokala enheten. Välj sedan **Välj filer** och ladda upp bilden till arbetsflödet.

![En löpare med ett citat ovanpå bilden](images/sample-text.jpg)

### <a name="track-progress"></a>Spåra förlopp

Du kan visa hur arbetsflödet fortskrider i nästa popup-fönster.

![Spåra arbetsflödeskörning](images/image-workflow-job.PNG)

### <a name="verify-workflow-action"></a>Verifiera arbetsflödesåtgärd

Gå till fliken **Bild** under **Granska** och kontrollera att det finns en nyskapd bildgranskning.

![Granska bilder](images/image-workflow-review.PNG)

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig hur du konfigurerar och använder modereringsarbetsflöden från [verktyget Granskning](https://contentmoderator.cognitive.microsoft.com)av innehållsmodererator . Nästa, se [REST API guide](../try-review-api-workflow.md) för att lära dig hur du skapar arbetsflöden programmässigt.
