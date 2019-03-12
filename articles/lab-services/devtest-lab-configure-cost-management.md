---
title: Visa månatliga uppskattade labbkostnadstrend i Azure DevTest Labs | Microsoft Docs
description: Läs mer om Azure DevTest Labs-trenddiagram för månatliga uppskattade kostnaden.
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
ms.date: 03/07/2019
ms.author: spelluru
ms.openlocfilehash: bc81f479305f39024b8d946e1ace3fc84ecb6253
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588864"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>Spåra kostnader i samband med ett labb i Azure DevTest Labs
Den här artikeln innehåller information om hur du spårar kostnaden för ditt labb. Den visar hur du visar beräknade kostnad trent för den aktuella kalendermånaden för övningen. Artikeln beskriver också hur du visar månad hittills kostnad per resurs i laboratoriet.

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>Visa månatliga uppskattade labbkostnadstrend 
I det här avsnittet får du lära dig hur du använder den **månatliga uppskattade kostnaden Trend** diagram om du vill visa den aktuella kalendermånaden uppskattad kostnad hittills och kostnadsprognoser i slutet av månaden för den aktuella kalendermånaden. Du också lära dig hur du hanterar labbkostnader genom att ange utgiftsgränsen mål och tröskelvärden som, när uppnåtts utlösaren DevTest Labs och rapporterar resultaten till dig.

Följ dessa steg om du vill visa månatliga uppskattade kostnaden Trend diagrammet: 

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla tjänster**, och välj sedan **DevTest Labs** i listan.
3. Listan över labbar, väljer du ditt labb.  
4. Välj **konfiguration och principer** på den vänstra menyn.  
4. Välj **kostnaden trend** i den **Kostnadsuppföljning** avsnitt i den vänstra menyn. Följande skärmbild visar ett exempel på ett diagram med kostnaden. 
   
    ![Kostnad diagram](./media/devtest-lab-configure-cost-management/graph.png)

    Den **uppskattad kostnad** värdet är den aktuella kalendermånaden uppskattad kostnad hittills. Den **beräknad kostnad** är den uppskattade kostnaden för den hela aktuella kalendermånaden, beräknas med hjälp av lab kostnaden för de senaste fem dagarna.

    Kostnadsbelopp avrundas uppåt till närmaste heltal. Exempel: 

    * 5.01 Avrundar upp till 6 
    * 5.50 Avrundar upp till 6
    * 5.99 Avrundar upp till 6

    Kostnaderna som visas som standard i diagrammet är som det säger ovanför diagrammet *uppskattade* kostnader med hjälp av [användningsbaserad](https://azure.microsoft.com/offers/ms-azr-0003p/) erbjuder priser. Du kan också ange egna utgifter mål som visas i diagrammen av [hantera kostnaden mål för övningen.](#managing-cost-targets-for-your-lab)

    Följande kostnader är *inte* ingår i beräkningen:

    * CSP och Dreamspark-prenumerationer stöds inte för tillfället eftersom Azure DevTest Labs använder den [Azure fakturering API: er](../billing/billing-usage-rate-card-overview.md) att beräkna labbet kostnad, som inte stöder CSP eller Dreamspark-prenumerationer.
    * Priserna för ditt erbjudande. För närvarande kan använda du inte erbjudandet priserna (visas i din prenumeration) att du har förhandlas med Microsoft eller Microsoft partner. Endast för användningsbaserad betalning används.
    * Din skatter
    * Slevy
    * Faktureringsvalutan. För närvarande visas lab kostnaden endast i USD valuta.

### <a name="managing-cost-targets-for-your-lab"></a>Hantera kostnaden mål för övningen
DevTest Labs kan du bättre hantera kostnaderna i labbet genom att ange en utgiftsgräns mål som du kan visa i diagrammet månatliga uppskattade kostnaden Trend. DevTest Labs kan också skicka ett meddelande när den angivna målsökvägen utgifter eller tröskelvärdet har uppnåtts. 

1. På den **kostnaden trend** väljer **hantera target**.

    ![Hantera målknappen](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. På den **hantera target** anger du ett utgiftsgränsen mål och tröskelvärden. Du kan också ange om varje valda tröskelvärde rapporteras på trenddiagram kostnaden eller via ett webhook-meddelande.

    ![Hantera mål-fönstret](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Välj en tidsperiod under vilken du vill att kostnaden mål som spåras.
      - **Månatliga**: kostnaden mål spåras per månad.
      - **Fast**: kostnaden mål spåras för datumintervall som du anger i start- och slutdatumen. Dessa värden representerar vanligtvis hur länge ditt projekt är schemalagd att köras.
   - Ange en **rikta kostnaden**. Till exempel hur mycket du planerar att spendera på den här övningen under den tidsperioden som du har definierat.
   - Välj om du vill aktivera eller inaktivera eventuella tröskelvärde du vill rapporteras – i steg om 25% – upp till 125% av dina angivna **rikta kostnaden**.
      - **Meddela**: När det här tröskelvärdet har uppnåtts, visas ett meddelande med en webhook-URL som du anger.
      - **Vykreslit v grafu**: När den här tröskeln uppfylls resultatet ritas på kostnaden trend diagrammet som du kan visa, enligt beskrivningen i [visa diagrammet månatliga uppskattade kostnaden Trend](#viewing-the-monthly-estimated-cost-trend-chart).
   - Om du väljer att **meddela** när tröskelvärdet är uppfyllt, måste du ange en webhook-URL. I området kostnaden integreringar väljer **Klicka här för att lägga till en integration**. Ange en **Webhooksadressen** i Konfigurera meddelandefönstret och välj sedan **OK**.

       ![Konfigurera meddelandefönstret](./media/devtest-lab-configure-cost-management/configure-notification.png)

      - Om du anger **meddela**, måste du definiera en webhook-URL.
      - På samma sätt, om du definierar en webhook-URL måste du ange **meddelande** till **på** i fönstret kostnaden tröskelvärdet.
      - Du måste skapa en webhook innan den här.  

      Läs mer om webhooks [skapa en webhook eller API Azure-funktion](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

## <a name="view-cost-by-resource"></a>Visa kostnaden per resurs 
Funktionen trend månatlig kostnad för i labb kan du se hur mycket du använt i den aktuella kalendermånaden. Den visar även projektion utgifter fram till slutet av månaden, baserat på dina utgifter under de senaste sju dagarna. För att förstå varför utgifter i labbet uppfyller tröskelvärden för tidigt på du kan använda den **kostnad per resurs** funktion som visar månad hittills kostnaden **per resurs** i en tabell.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla tjänster**, och välj sedan **DevTest Labs** i listan.
3. Listan över labbar, Välj önskade labbet.  
4. Välj **konfiguration och principer** på den vänstra menyn.
5. Välj **kostnad per resurs** i den **Kostnadsuppföljning** avsnitt i den vänstra menyn. Du kan se kostnaderna för varje resurs som är associerad med ett labb. 

    ![Kostnad efter resurs](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

Den här funktionen hjälper dig att enkelt identifiera de resurser som kostar mest så att du kan vidta åtgärder för att minska lab utgifter. Till exempel baseras kostnaden för en virtuell dator på storleken på den virtuella datorn. Ju större storleken på den virtuella datorn, mer är kostnaden. Hittar du enkelt storleken på en virtuell dator och ägare, så att du kan kommunicera med VM-ägaren att förstå varför behövs sådana VM-storlek och om det finns en risk att minska storleken.

[Princip för automatisk avstängning](devtest-lab-get-started-with-lab-policies.md#set-auto-shutdown) hjälper dig att minska kostnaderna genom att stänga av lab virtuella datorer på en viss tid på dagen. En lab-användare kan dock välja bort avstängning principen, vilket ökar kostnaden för att köra den virtuella datorn. Du kan välja en virtuell dator i tabellen för att se om det har valts bort av principen för automatisk avstängning. Om så är fallet kan du kommunicera med VM-ägaren att hitta varför den virtuella datorn har valts bort av principen.
 
## <a name="next-steps"></a>Nästa steg
Här följer några saker du kan prova sedan:

* [Definiera labbprinciper](devtest-lab-set-lab-policy.md) – Lär dig att ange olika principer används för att styra hur ditt labb och dess virtuella datorer används. 
* [Skapa en anpassad avbildning](devtest-lab-create-template.md) – när du skapar en virtuell dator kan du ange en bastabell som kan vara en anpassad avbildning eller en Marketplace-avbildning. Den här artikeln visar hur du skapar en anpassad avbildning från en VHD-fil.
* [Konfigurera Marketplace-avbildningar](devtest-lab-configure-marketplace-images.md) – DevTest Labs stöder skapandet av virtuella datorer baserat på Azure Marketplace-avbildningar. Den här artikeln visar hur du kan ange vilka eventuella Azure Marketplace-avbildningar kan vara används när du skapar virtuella datorer i ett labb.
* [Skapa en virtuell dator i ett labb](devtest-lab-add-vm.md) -illustrerar hur du skapar en virtuell dator från en grundläggande avbildning (antingen anpassad eller Marketplace), och hur du arbetar med artefakter i den virtuella datorn.

