---
title: Hantera virtuell Azure-dator med inventeringssamling | Microsoft Docs
description: Hantera virtuell dator med inventeringssamlingen
services: automation
keywords: inventory, automation, change, tracking
author: jennyhunter-msft
ms.author: jehunte
ms.date: 09/13/2017
ms.topic: hero-article
manager: carmonm
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 5291d112c2cdf157543fe301d5a5c80f3fe561ae
ms.contentlocale: sv-se
ms.lasthandoff: 09/25/2017

---

# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Hantera en virtuell Azure-dator med inventeringssamling

Inventeringssamling kan aktiveras för en virtuell Azure-dator via datorns resurssida. Den här metoden ger ett webbläsarbaserat användargränssnitt för att installera och konfigurera inventeringssamling.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.
Om du inte har en virtuell Azure-dator ska du skapa en [virtuell dator](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-portal) innan du börjar.

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Aktivera inventeringssamling via en virtuell dators resurssida

1. Välj **Virtuella datorer** på skärmen till vänster.
1. Välj en virtuell dator i listan.
1. Välj **Inventering (förhandsversion)** på resursmenyn under **Åtgärder**.
1. En banderoll visas högst upp på sidan, om att lösningen inte är aktiverad. Klicka på banderollen för att aktivera lösningen.
1. Välj en Log Analytics-arbetsyta där du lagrar dina dataloggar. Om det inte finns några tillgängliga arbetsytor för dig för en regionen uppmanas du att skapa en standardarbetsyta och ett Automation-konto. Klicka på **Aktivera** för att börjar integrera datorn.

   ![Visa integreringsalternativ](./media/automation-vm-inventory/inventory-onboarding-options.png)  

1. Ett statusfält meddelar dig att lösningen aktiveras. Processen kan ta upp till 15 minuter. Under den här tiden kan du stänga bladet eller ha det öppet så får du en rapport när lösningen har aktiverats. Du kan övervaka distributionsstatusen via meddelandefönstret.

   ![Visa inventeringslösningen direkt efter integreringen](./media/automation-vm-inventory/inventory-onboarded.png)

1. När distributionen är klar försvinner statusfältet. Systemet samlar då fortfarande in inventeringsdata och dessa data kanske inte visas än. En fullständig insamling av data kan ta 24 timmar.

## <a name="configure-your-inventory-settings"></a>Konfigurera inventeringsinställningarna

Som standard konfigureras programvara, Windows-tjänster och Linux-daemon för insamling. Om du vill inventera Windows-registret och filer kan du konfigurera inställningarna för inventeringsinsamling.

1. I vyn **Inventering (förhandsversion)** väljer du knappen **Redigera inställningar** högst upp på sidan.
2. Om du vill lägga till en ny insamlingsinställning går du till inställningskategorin du vill lägga till genom att använda flikarna **Windows-registret**, **Windows-filer** och **Linux-filer**. Klicka på **Lägg till** högst upp på sidan.
3. Om du vill visa detaljer och beskrivningar för varje inställningsegenskap går du till [sidan med inventeringsdokumentation](https://aka.ms/configinventorydocs).

## <a name="disconnecting-your-virtual-machine-from-management"></a>Koppla bort den virtuella datorn från hantering

Ta bort datorn från inventeringshantering:

1. Klicka på **Log Analytics** i den vänstra menyn i Azure-portalen och klickmarkera sedan den arbetsyta du använde när du integrerade den virtuella datorn.
1. På sidan Log Analytics väljer du **Virtuella datorer** under kategorin **Datakällor för arbetsyta** i resursmenyn. 
1. Välj den virtuella dator du vill koppla bort från listan. Den har en grön bock bredvid texten ”Den här arbetsytan” i kolumnen **OMS-anslutning**. Klicka på **Koppla från** högst upp på nästa sida och **Ja** i bekräftelsedialogrutan för att koppla bort datorn från hantering.

## <a name="next-steps"></a>Nästa steg

* Information om hur du hanterar ändringar i inställningarna för filer och registret finns i [Ändringsspårning](../log-analytics/log-analytics-change-tracking.md).
* Information om hur du hanterar Windows- och paketuppdateringar på virtuella datorer finns i [Uppdateringshantering](../operations-management-suite/oms-solution-update-management.md)

