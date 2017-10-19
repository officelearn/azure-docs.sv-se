---
title: Hantera en virtuell Azure-dator med inventeringssamling | Microsoft Docs
description: Hantera en virtuell dator med inventeringssamling
services: automation
keywords: inventory, automation, change, tracking
author: jennyhunter-msft
ms.author: jehunte
ms.date: 09/13/2017
ms.topic: hero-article
manager: carmonm
ms.openlocfilehash: bfdd67ac1018f242e415da1a66d93f22bea8d054
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Hantera en virtuell Azure-dator med inventeringssamling

Du kan aktivera inventeringssamling för en virtuell Azure-dator via den virtuella datorns resurssida. Den här metoden ger ett webbläsarbaserat användargränssnitt för att installera och konfigurera inventeringssamling.

## <a name="before-you-begin"></a>Innan du börjar
Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/).
Om du inte har en virtuell Azure-dator ska du skapa en [virtuell dator](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-portal).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal
Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Aktivera inventeringssamling via en virtuell dators resurssida

1. I rutan till vänster på Azure Portal väljer du **Virtuella datorer**.
2. Från listan med virtuella datorer väljer du en virtuell dator.
3. Välj **Inventering (förhandsversion)** på **resursmenyn** under **Åtgärder**.  
    En banderoll visas högst upp i fönstret, om att lösningen inte är aktiverad. 
4. Markera banderollen för att aktivera lösningen.
5. Välj en Log Analytics-arbetsyta där du lagrar dina dataloggar.  
    Om det inte finns några tillgängliga arbetsytor för dig för en regionen uppmanas du att skapa en standardarbetsyta och ett Automation-konto. 
6. Klicka på **Aktivera** för att börjar integrera datorn.

   ![Visa integreringsalternativ](./media/automation-vm-inventory/inventory-onboarding-options.png)  
    Ett statusfält meddelar dig att lösningen aktiveras. Processen kan ta upp till 15 minuter. Under den här tiden kan du stänga fönstret eller ha det öppet så får du ett meddelande när lösningen har aktiverats. Du kan övervaka distributionsstatusen via meddelandefönstret.

   ![Visa inventeringslösningen direkt efter integreringen](./media/automation-vm-inventory/inventory-onboarded.png)

När distributionen är klar försvinner statusfältet. Systemet samlar fortfarande in inventeringsdata och dessa data kanske inte visas än. En fullständig insamling av data kan ta 24 timmar.

## <a name="configure-your-inventory-settings"></a>Konfigurera inventeringsinställningarna

Som standard konfigureras programvara, Windows-tjänster och Linux-daemon för insamling. Om du vill inventera Windows-registret och filer kan du konfigurera inställningarna för inventeringsinsamling.

1. I vyn **Inventering (förhandsversion)** väljer du knappen **Redigera inställningar** högst upp i fönstret.
2. Om du vill lägga till en ny insamlingsinställning går du till inställningskategorin du vill lägga till genom att välja **Windows-registret**, **Windows-filer** och **Linux-filer**. 
3. Välj **Lägg till** högst upp i fönstret.
4. Om du vill visa detaljer och beskrivningar för varje inställningsegenskap går du till [sidan med inventeringsdokumentation](https://aka.ms/configinventorydocs).

## <a name="disconnect-your-virtual-machine-from-management"></a>Koppla bort den virtuella datorn från hantering

Ta bort den virtuella datorn från inventeringshantering:

1. Välj **Log Analytics** i den vänstra rutan på Azure Portal och välj sedan den arbetsyta du använde när du integrerade den virtuella datorn.
2. I fönstret **Log Analytics** väljer du **Virtuella datorer** under kategorin **Datakällor för arbetsyta** på **resursmenyn**. 
3. Välj den virtuella dator du vill koppla bort från listan. Den virtuella datorn har en grön bock bredvid texten **Den här arbetsytan** i kolumnen **OMS-anslutning**. 
4. Överst på nästa sida väljer du **Koppla från**.
5. Välj **Ja** i bekräftelsefönstret.  
    Den här åtgärden kopplar från datorn från hanteringen.

## <a name="next-steps"></a>Nästa steg

* Information om hur du hanterar ändringar i inställningarna för filer och registret finns i [Track software changes in your environment with the Change Tracking solution](../log-analytics/log-analytics-change-tracking.md) (Spåra programändringar i miljön med lösningen Ändringsspårning).
* Information om hur du hanterar Windows- och paketuppdateringar på virtuella datorer finns i [uppdateringshanteringslösningen i OMS](../operations-management-suite/oms-solution-update-management.md).
