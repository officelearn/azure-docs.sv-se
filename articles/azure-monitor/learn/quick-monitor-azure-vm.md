---
title: Övervaka en Azure-resurs med Azure Monitor
description: Lär dig hur du samlar in och analyserar data för en Azure-resurs i Azure Monitor.
ms.service: azure-monitor
ms. subservice: logs
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 03/10/2020
ms.openlocfilehash: 12616fbc80468d35ebb8b0bc5cd12bfd5871e788
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79503659"
---
# <a name="quickstart-monitor-an-azure-virtual-machine-with-azure-monitor"></a>Snabbstart: Övervaka en virtuell Azure-dator med Azure Monitor
[Azure Monitor](../overview.md) börjar samla in data från virtuella Azure-datorer så fort de skapas. I den här snabbstarten tar du en kort genomgång av de data som samlas in automatiskt för en Virtuell Azure-dator och hur du visar den i Azure-portalen. Du aktiverar sedan [Azure Monitor för virtuella datorer](../insights/vminsights-overview.md) för din virtuella dator som gör det möjligt för agenter på den virtuella datorn att samla in och analysera data från gästoperativsystemet, inklusive processer och deras beroenden.

För den här snabbstarten förutsätts det att du har en befintlig virtuell dator i Azure. Om inte du kan skapa en [Windows VM](../../virtual-machines/windows/quick-create-portal.md) eller skapa en [Linux-VM](../../virtual-machines/linux/quick-create-cli.md) efter våra VM-snabbstarter.

Mer detaljerade beskrivningar av övervakningsdata som samlats in från Azure-resurser finns i [Övervaka virtuella Azure-datorer med Azure Monitor](../insights/monitor-vm-azure.md).


## <a name="complete-the-monitor-an-azure-resource-quickstart"></a>Slutför snabbstarten för en Azure-resurs.
Slutför [Övervaka en Azure-resurs med Azure Monitor](quick-monitor-azure-resource.md) för att visa översiktssidan, aktivitetsloggen och måtten för en virtuell dator i din prenumeration. Virtuella Azure-datorer samlar in samma övervakningsdata som alla andra Azure-resurser, men det här är bara för värddatorn. Resten av den här snabbstarten fokuserar på att övervaka gästoperativsystemet och dess arbetsbelastningar.


## <a name="enable-azure-monitor-for-vms"></a>Aktivera Azure Monitor för virtuella datorer
Även om mått och aktivitetsloggar samlas in för värddatorn behöver du en agent och viss konfiguration för att samla in och analysera övervakningsdata från gästoperativsystemet och dess arbetsbelastningar. Azure Monitor för virtuella datorer installerar dessa agenter och innehåller ytterligare kraftfulla funktioner för övervakning av dina virtuella datorer.

1. Gå till menyn för din virtuella dator.
2. Klicka antingen på **Gå till insikter** från panelen på sidan **Översikt** eller klicka på Insikter från **övervakningsmenyn.** **Monitoring**

    ![Översiktssida](media/quick-monitor-azure-vm/overview-insights.png)

3. Om Azure Monitor för virtuella datorer ännu inte har aktiverats för den virtuella datorn klickar du på **Aktivera**. 

    ![Aktivera insikter](media/quick-monitor-azure-vm/enable-insights.png)

4. Om den virtuella datorn inte redan är ansluten till en Log Analytics-arbetsyta uppmanas du att välja en befintlig arbetsyta eller skapa en ny. Välj standard som är en arbetsyta med ett unikt namn i samma region som den virtuella datorn.

    ![Välj arbetsyta](media/quick-monitor-azure-vm/select-workspace.png)

5. Onboarding tar några minuter när tillägg är aktiverade och agenter installeras på din virtuella dator. När den är klar får du ett meddelande om att insikter har distribuerats. Klicka på **Azure Monitor** för att öppna Azure Monitor för virtuella datorer.

    ![Öppna Azure Monitor](media/quick-monitor-azure-vm/azure-monitor.png)

6. Du ser din virtuella dator med andra virtuella datorer i din prenumeration som är inbyggda. Välj fliken **Inte övervakad** om du vill visa virtuella datorer i prenumerationen som inte är inbyggda.

    ![Komma igång](media/quick-monitor-azure-vm/get-started.png)


## <a name="configure-workspace"></a>Konfigurera arbetsyta
När du skapar en ny Log Analytics-arbetsyta måste den konfigureras för att samla in loggar. Den här konfigurationen behöver bara utföras en gång eftersom konfigurationen skickas till alla virtuella datorer som ansluter till den.

1. Välj **Konfiguration för arbetsyta** och välj sedan arbetsytan.

2. Välj **avancerade inställningar**

    ![Avancerade inställningar i Log Analytics](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)

### <a name="data-collection-from-windows-vm"></a>Insamling av data från virtuella Windows-datorer


2. Välj **Data** och sedan **Windows-händelseloggar**.

3. Lägg till en händelselogg genom att skriva in loggens namn.  Skriv **System** och välj **+** sedan plustecknet .

4. Kontrollera allvarlighetsgraderna **Fel** och **Varning** i tabellen.

5. Välj **Spara** högst upp på sidan för att spara konfigurationen.

### <a name="data-collection-from-linux-vm"></a>Insamling av data från virtuella Linux-datorer

1. Välj **Syslog**.  

2. Lägg till en händelselogg genom att skriva in loggens namn.  Skriv **Syslog** och välj **+** sedan plustecknet .  

3. Avmarkera allvarlighetsgraderna **Info**, **Notice** och **Debug**i tabellen . 

4. Välj **Spara** högst upp på sidan för att spara konfigurationen.

## <a name="view-data-collected"></a>Visa data som samlas in

7. Klicka på din virtuella dator och välj sedan fliken **Prestanda.** Detta visar en utvald grupp av prestandaräknare som samlats in från gästoperativsystemet för din virtuella dator. Bläddra nedåt för att visa fler räknare och flytta musen över ett diagram för att visa medelvärden och percentiler vid olika tidpunkter.

    ![Prestanda](media/quick-monitor-azure-vm/performance.png)

9. Välj **Karta om** du vill öppna kartfunktionen som visar de processer som körs på den virtuella datorn och deras beroenden. Välj **Egenskaper** för att öppna egenskapsfönstret om det inte redan är öppet.

    ![Karta](media/quick-monitor-azure-vm/map.png)

11. Expandera processerna för den virtuella datorn. Välj en av processerna för att visa dess information och markera dess beroenden.

    ![Processer](media/quick-monitor-azure-vm/processes.png)

12. Välj din virtuella dator igen och välj sedan **Logga händelser**. 

    ![Logga händelser](media/quick-monitor-azure-vm/log-events.png)

13. Du ser en lista över tabeller som lagras på log analytics-arbetsytan för den virtuella datorn. Den här listan kommer att vara olika beroende på om du använder en virtuell Windows- eller Linux-dator. Klicka på tabellen **Händelse.** Detta inkluderar alla händelser från Windows-händelseloggen. Log Analytics öppnas med en enkel fråga för att hämta händelseloggposter.

    ![Log Analytics](media/quick-monitor-azure-vm/log-analytics.png)

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten har du aktiverat Azure Monitor för virtuella datorer för en virtuell dator och konfigurerat Log Analytics-arbetsytan för att samla in händelser för gästoperativsystemet. Om du vill lära dig hur du visar och analyserar data kan du fortsätta till självstudiekursen.

> [!div class="nextstepaction"]
> [Visa eller analysera data i Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md)
