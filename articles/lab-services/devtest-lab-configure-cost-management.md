---
title: Visa de månatliga kostnadstrend beräknade labbkostnaden i Azure DevTest Labs | Microsoft Docs
description: Läs mer om Azure DevTest Labs månatliga uppskattade kostnaden trend diagrammet.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1f46fdc5-d917-46e3-a1ea-f6dd41212ba4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 13535dae82ef2c8896dad7d6221553d15e4e6a95
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="view-the-monthly-estimated-lab-cost-trend-in-azure-devtest-labs"></a>Visa de månatliga kostnadstrend beräknade labbkostnaden i Azure DevTest Labs
Funktionen hantering av kostnaden för DevTest Labs hjälper dig att spåra kostnaden för ditt labb. Den här artikeln beskrivs hur du använder den **månatlig uppskattad Kostnadstrend** diagram om du vill visa den aktuella kalendermånaden uppskattade kostnaden-till-date och planerade sista månad kostnaden för den aktuella kalendermånaden. Den här artikeln visar även hur för enklare hantering lab kostnader genom att ange utgiftsgränsen mål och tröskelvärden som, när uppnåtts utlösaren DevTest Labs att rapportera resultatet till dig.

## <a name="viewing-the-monthly-estimated-cost-trend-chart"></a>Visa diagrammet månatlig uppskattad Kostnadstrend
Följ dessa steg om du vill visa månatlig uppskattad Kostnadstrend diagrammet: 

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Om det behövs väljer **alla tjänster**, och välj sedan **DevTest Labs** från listan. (Ditt labb kanske redan visas på instrumentpanelen under **alla resurser**).
1. Lista över labs, Välj önskade labbet.  
1. På testmiljön **översikt** väljer **konfiguration och principer**.   
1. Vänster under **KOSTNADSUPPFÖLJNING**väljer **kostnadstrend**.

   Följande skärmbild visar ett exempel på ett diagram med kostnaden. 
   
    ![Kostnad diagram](./media/devtest-lab-configure-cost-management/graph.png)

Den **uppskattade kostnaden** värdet är den aktuella kalendermånaden uppskattade kostnaden hittills. Den **planerad kostnad** är uppskattade kostnaden för hela aktuell kalendermånad, beräknas med hjälp av lab kostnaden för föregående fem dagar.

Kostnadsbelopp som avrundat till närmaste heltal. Exempel: 

* 5.01 Avrundar upp till 6 
* 5.50 Avrundar upp till 6
* 5.99 Avrundar upp till 6

Eftersom den anger ovanför diagrammet kostnaderna visas som standard i diagrammet är *uppskattade* kostnader med [betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/) erbjuda priser. Du kan också ange egna utgifter mål som visas i diagram av [hantera kostnaden mål för övningen.](#managing-cost-targets-for-your-lab)

Dessutom kan följande är *inte* med i beräkningen:

* CSP och Dreamspark-prenumerationer stöds inte för närvarande eftersom Azure DevTest Labs använder den [Azure fakturering API: er](../billing/billing-usage-rate-card-overview.md) att beräkna labbet kostnad, som inte stöder CSP eller Dreamspark-prenumerationer.
* Allteftersom-taxan. För närvarande kan använda du inte allteftersom-taxan (visas i din prenumeration) att du har förhandlats med Microsoft eller Microsoft partner. Endast betala per användning priser används.
* Din skatter
* Din rabatt
* Valuta. För närvarande visas lab kostnaden bara i USD valuta.

## <a name="managing-cost-targets-for-your-lab"></a>Hantera kostnaden mål för ditt labb
DevTest Labs kan du hantera kostnader i ditt labb bättre genom att ange ett utgiftsgränsen mål som du sedan kan visas i diagrammet månatlig uppskattad Kostnadstrend. DevTest Labs kan också skicka ett meddelande när det angivna målet utgifter eller tröskelvärdet har uppnåtts. 

1. Om ditt labb **översikt** väljer **konfiguration och principer**.
1. Vänster under **KOSTNADSUPPFÖLJNING**väljer **kostnadstrend**.

    ![Hantera målknappen](./media/devtest-lab-configure-cost-management/cost-trend.png)

1. I den **kostnadstrend** väljer **hantera mål**.

    ![Hantera målknappen](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)

1. Ange önskat utgiftsgränsen mål och tröskelvärden i fönstret hantera mål. Du kan också ange om varje valda tröskelvärde rapporteras på kostnaden trend diagrammet eller via ett webhook-meddelande.

    ![Hantera mål-fönstret](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Välj en tidsperiod under vilken du vill kostnaden mål spåras.
      - **Månatliga**: kostnaden mål spåras per månad.
      - **Fast**: kostnaden mål spåras för datumintervallet som du anger i Start- och End datumfält. Detta kan normalt motsvarar hur länge projektet är schemalagd att köras.
   - Ange en **mål kostnaden**. Detta kanske till exempel hur mycket du planerar att spendera på den här övningen under en tidsperiod som du har definierat.
   - Välj om du vill aktivera eller inaktivera eventuella tröskelvärde du vill – stegvis med 25% – rapporteras till 125% av den angivna **mål kostnaden**.
      - **Meddela**: när den här tröskeln uppfylls, får du ett meddelande av en webhook-URL som du anger.
      - **Rita på diagrammet**: när det här tröskelvärdet har uppnåtts resultaten ritas på kostnaden trend diagrammet som du kan visa, enligt beskrivningen i [visa diagrammet månatlig uppskattad Kostnadstrend](#viewing-the-monthly-estimated-cost-trend-chart).
   - Om du väljer att **Avisera** när tröskelvärdet är uppfyllt, måste du ange en Webhooksadressen. Välj i området kostnaden integreringar **Klicka här om du vill lägga till en integration**.

      Ange en Webhooksadressen i meddelandefönstret konfigurera och välj sedan **OK**.

       ![Konfigurera meddelandefönstret](./media/devtest-lab-configure-cost-management/configure-notification.png)

      - Om du anger **Avisera**, måste du definiera en Webhooksadressen.
      - Likaså om du definierar en Webhooksadressen, måste du ange **meddelande** till **på** i fönstret kostnaden tröskelvärdet.
      - Du måste skapa en webhook innan du att ange den här.  

      Läs mer om webhooks [skapa en webhook eller API Azure-funktion](../azure-functions/functions-create-a-web-hook-or-api-function.md). 
 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Relaterade blogginlägg
* [Två saker att hålla dina kostnader på Spåra i DevTest Labs](https://blogs.msdn.microsoft.com/devtestlab/2016/06/21/keep-your-cost-on-track/)
* [Varför kostnad tröskelvärden?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/11/why-cost-thresholds/)

## <a name="next-steps"></a>Nästa steg
Här följer några saker att försöka:

* [Definiera principer för labbet](devtest-lab-set-lab-policy.md) – Lär dig att ange olika principer som används för att styra hur ditt labb och dess virtuella datorer används. 
* [Skapa den anpassade bilden](devtest-lab-create-template.md) – när du skapar en virtuell dator, anger du en bas som kan vara antingen en anpassad avbildning eller en Marketplace-avbildning. Den här artikeln beskrivs hur du skapar en anpassad avbildning från en VHD-fil.
* [Konfigurera Marketplace-bilder](devtest-lab-configure-marketplace-images.md) - DevTest Labs stöder skapandet av virtuella datorer baserat på Azure Marketplace-bilder. Den här artikeln visar hur du kan ange vilka eventuella Azure Marketplace-bilder kan användas när du skapar virtuella datorer i ett labb.
* [Skapa en virtuell dator i ett labb](devtest-lab-add-vm.md) -illustrerar hur du skapar en virtuell dator från en grundläggande bild (antingen anpassad eller Marketplace), och hur du arbetar med artefakter i den virtuella datorn.

