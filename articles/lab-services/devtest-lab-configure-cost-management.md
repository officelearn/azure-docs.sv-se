---
title: Visa den månatliga uppskattade labbkostnadstrenden i Azure DevTest Labs
description: Den här artikeln innehåller information om hur du spårar kostnaden för ditt labb (månatliga uppskattade kostnadstrenddiagram) i Azure DevTest Labs.
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
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 2e4fe55fac5edf73e16df05bd38cc2712a94377d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721735"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>Spåra kostnader som är associerade med ett labb i Azure DevTest Labs
Den här artikeln innehåller information om hur du spårar kostnaden för ditt labb. Den visar hur du visar den beräknade kostnaden trent för den aktuella kalendermånaden för labbet. Artikeln visar också hur du visar månadskostnad per resurs i labbet.

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>Visa den månatliga uppskattade labbkostnadstrenden 
I det här avsnittet får du lära dig hur du använder diagrammet **Månatlig beräknad kostnadstrend** för att visa den aktuella kalendermånadens uppskattade hittillskostnad och den beräknade kostnaden i slutet av månaden för den aktuella kalendermånaden. Du lär dig också hur du hanterar labbkostnader genom att ange utgiftsmål och tröskelvärden som, när de nås, utlöser DevTest Labs för att rapportera resultaten till dig.

Så här visar du diagrammet Uppskattad kostnad i månaden: 

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Alla tjänster**och välj sedan **DevTest Labs** i listan.
3. Välj ditt labb i listan över labb.  
4. Välj **Konfiguration och principer** på den vänstra menyn.  
4. Välj **Kostnadstrend** i avsnittet **Kostnadsspårning** på den vänstra menyn. Följande skärmbild visar ett exempel på ett kostnadsdiagram. 
   
    ![Kostnadsdiagram](./media/devtest-lab-configure-cost-management/graph.png)

    **Det uppskattade kostnadsvärdet** är den aktuella kalendermånadens uppskattade hittillskostnad. Den **beräknade kostnaden** är den uppskattade kostnaden för hela den aktuella kalendermånaden, beräknad med hjälp av labbkostnaden för de föregående fem dagarna.

    Kostnadsbeloppen avrundas uppåt till nästa heltal. Ett exempel: 

   * 5,01 rundor upp till 6 
   * 5,50 rundor upp till 6
   * 5,99 rundor upp till 6

     Som det står ovanför diagrammet är de kostnader som du ser som standard i diagrammet *uppskattade* kostnader med hjälp av erbjudanden om [användningsbaserad](https://azure.microsoft.com/offers/ms-azr-0003p/) betalning. Du kan också ange egna utgiftsmål som visas i diagrammen genom [att hantera kostnadsmålen för ditt labb.](#managing-cost-targets-for-your-lab)

     Följande kostnader ingår *inte* i kostnadsberäkningen:

   * CSP- och Dreamspark-prenumerationer stöds för närvarande inte eftersom Azure DevTest Labs använder [Azure-fakturerings-API:erna](../cost-management-billing/manage/usage-rate-card-overview.md) för att beräkna labbkostnaden, som inte stöder CSP- eller Dreamspark-prenumerationer.
   * Dina erbjudandepriser. För närvarande kan du inte använda de erbjudandepriser (som visas under din prenumeration) som du har förhandlat fram med Microsoft- eller Microsoft-partner. Endast pay-as-you-go-priser används.
   * Dina skatter
   * Dina rabatter
   * Din faktureringsvalutan. För närvarande visas labbkostnaden endast i USD-valuta.

### <a name="managing-cost-targets-for-your-lab"></a>Hantera kostnadsmål för ditt labb
Med DevTest Labs kan du hantera kostnaderna i labbet bättre genom att ange ett utgiftsmål som du sedan kan visa i diagrammet Uppskattad kostnadtrend per månad. DevTest Labs kan också skicka ett meddelande till dig när det angivna målutgiftet eller tröskelvärdet har uppnåtts. 

1. På sidan **Kostnadstrend** väljer du **Hantera mål**.

    ![Knappen Hantera mål](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. På sidan **Hantera mål** anger du ett utgiftsmål och tröskelvärden. Du kan också ange om varje valt tröskelvärde ska rapporteras i kostnadstrenddiagrammet eller via ett webhook-meddelande.

    ![Hantera målfönstret](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Välj en tidsperiod under vilken du vill att kostnadsmål ska spåras.
      - **Månad**: kostnadsmål spåras per månad.
      - **Fast**: Kostnadsmål spåras för det datumintervall som du anger i start- och slutdatumen. Vanligtvis representerar dessa värden hur länge projektet är schemalagt att köras.
   - Ange en **målkostnad**. Till exempel, hur mycket du planerar att spendera på det här labbet under den tidsperiod du definierade.
   - Välj det här alternativet om du vill aktivera eller inaktivera alla tröskelvärden som du vill ska rapporteras – i steg om 25 % – upp till 125 % av den angivna **målkostnaden**.
      - **Meddela:** När det här tröskelvärdet är uppfyllt meddelas du av en webhook-URL som du anger.
      - **Diagram i diagram**: När det här tröskelvärdet är uppfyllt ritas resultaten på kostnadstrenddiagrammet som du kan visa, enligt beskrivningen i Visa diagrammet Uppskattad kostnadtrend per månad.
   - Om du väljer att **meddela** när tröskelvärdet uppfylls måste du ange en webhook-URL. I området Kostnadsintegrationer väljer du **Klicka här för att lägga till en integration**. Ange en **Webhook-URL** i fönstret Konfigurera meddelande och välj sedan **OK**.

       ![Konfigurera meddelandefönstret](./media/devtest-lab-configure-cost-management/configure-notification.png)

     - Om du anger **Meddela**måste du definiera en webhook-URL.
     - Om du definierar en webhook-URL måste du också ange **Meddelande** **till På** i tröskelfönstret Kostnad.
     - Du måste skapa en webhook innan du anger det här.  

       Mer information om webhooks finns i [Skapa en webhook- eller API Azure-funktion](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

## <a name="view-cost-by-resource"></a>Visa kostnad per resurs 
Den månatliga kostnadstrendfunktionen i labben gör att du kan se hur mycket du har spenderat under den aktuella kalendermånaden. Det visar också prognosen för utgifterna fram till slutet av månaden, baserat på dina utgifter under de senaste sju dagarna. För att hjälpa dig att förstå varför utgifterna i labbet uppfyller tröskelvärden tidigt kan du använda **resursfunktionen kostnad per resurs** som visar kostnaden för månad **per resurs** i en tabell.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Alla tjänster**och välj sedan **DevTest Labs** i listan.
3. Välj önskat labb i listan över labb.  
4. Välj **Konfiguration och principer** på den vänstra menyn.
5. Välj **Kostnad per resurs** i avsnittet **Kostnadsspårning** på den vänstra menyn. Du ser kostnaderna som är associerade med varje resurs som är associerad med ett labb. 

    ![Kostnad efter resurs](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

Den här funktionen hjälper dig att enkelt identifiera de resurser som kostar mest så att du kan vidta åtgärder för att minska labbutgifterna. Kostnaden för en virtuell dator baseras till exempel på storleken på den virtuella datorn. Ju större storlek på den virtuella datorn, mer är kostnaden. Du kan enkelt hitta storleken på en virtuell dator och ägaren, så att du kan prata med den virtuella datorns ägare för att förstå varför sådan VM-storlek behövs och om det finns en chans att sänka storleken.

[Principen för automatisk avstängning](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy) hjälper dig att minska kostnaderna genom att stänga av virtuella labb-datorer vid en viss tidpunkt på dagen. En labbanvändare kan dock välja bort avstängningsprincipen, vilket ökar kostnaden för att köra den virtuella datorn. Du kan välja en virtuell dator i tabellen för att se om den har valts bort från principen för automatisk avstängning. Om så är fallet kan du prata med den virtuella datorns ägare för att ta reda på varför den virtuella datorn har valts bort från principen.
 
## <a name="next-steps"></a>Nästa steg
Här är några saker du kan prova härnäst:

* [Definiera labbprinciper](devtest-lab-set-lab-policy.md) – Lär dig hur du anger de olika principer som används för att styra hur ditt labb och dess virtuella datorer används. 
* [Skapa anpassad avbildning](devtest-lab-create-template.md) – När du skapar en virtuell dator anger du en bas som kan vara antingen en anpassad avbildning eller en Marketplace-avbildning. Den här artikeln illustrerar hur du skapar en anpassad avbildning från en VHD-fil.
* [Konfigurera Marketplace-avbildningar](devtest-lab-configure-marketplace-images.md) – DevTest Labs stöder att skapa virtuella datorer baserat på Azure Marketplace-avbildningar. Den här artikeln visar hur du anger vilka, om några, Azure Marketplace-avbildningar som kan användas när du skapar virtuella datorer i ett labb.
* [Skapa en virtuell dator i ett labb](devtest-lab-add-vm.md) - Illustrerar hur du skapar en virtuell dator från en basavbildning (antingen anpassad eller Marketplace) och hur du arbetar med artefakter i den virtuella datorn.

