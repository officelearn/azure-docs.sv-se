---
title: VM uppdatering och hantering med Azure Stack | Microsoft Docs
description: Lär dig hur du använder Azure Monitor för virtuella datorer, uppdateringshantering, ändringsspårning och inventering lösningar i Azure Automation för att hantera Windows- och Linux-datorer som distribueras i Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: jeffgilb
ms.reviewer: rtiberiu
ms.lastreviewed: 03/20/2019
ms.openlocfilehash: cb8258c0f837d0e70ba87a26246f055b0efe5c00
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316159"
---
# <a name="azure-stack-vm-update-and-management"></a>Azure Stack-VM-uppdatering och hantering
Du kan använda följande funktioner i Azure Automation-lösningen för att hantera Windows- och Linux-datorer som distribueras med hjälp av Azure Stack:

- **[Hantering av uppdateringar](https://docs.microsoft.com/azure/automation/automation-update-management)**. Du kan snabbt bedöma status för tillgängliga uppdateringar på alla agentdatorer och hantera processen för att installera nödvändiga uppdateringar för dessa Windows och Linux-datorer med lösningen för uppdateringshantering.

- **[Ändringsspårning](https://docs.microsoft.com/azure/automation/automation-change-tracking)**. Ändringar av installerad programvara, tjänster för Windows, Windows-registret och filer och Linux-Daemon på de övervakade servrarna skickas till Azure Monitor-tjänsten i molnet för bearbetning. Logiken tillämpas på den mottagna data och Molntjänsten innehåller data. Du kan enkelt se ändringar som gjorts i din serverinfrastruktur med hjälp av informationen på instrumentpanelen för ändringsspårning.

- **[Inventering](https://docs.microsoft.com/azure/automation/automation-vm-inventory)**. Inventeringssamling för en virtuell dator i Azure Stack ger ett webbläsarbaserat användargränssnitt för att installera och konfigurera inventeringssamling.

- **[Azure Monitor för virtuella datorer](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)**. Azure Monitor för virtuella datorer övervakar dina Azure och Azure Stack--datorer (VM) och VM-skalningsuppsättningar i stor skala. Den analyserar prestandan och hälsotillståndet för dina Windows- och Linux-datorer och övervakar sina processer och beroenden på andra resurser och externa processer. 

> [!IMPORTANT]
> Dessa lösningar är samma som de som används för att hantera virtuella Azure-datorer. Både Azure och Azure Stack-datorer hanteras på samma sätt, från samma gränssnitt med samma verktyg. De virtuella datorer i Azure Stack är också samma pris som virtuella Azure-datorer när du använder lösningar för uppdateringshantering, ändringsspårning, inventering och övervaka virtuella datorer i Azure med Azure Stack.

## <a name="prerequisites"></a>Förutsättningar
Flera förutsättningar måste vara uppfyllda innan du använder dessa funktioner för att uppdatera och hantera virtuella datorer i Azure Stack. Dessa inkluderar steg som måste vidtas i Azure portal samt Azure Stack-administrationsportalen.

### <a name="in-the-azure-portal"></a>I Azure-portalen
Om du vill använda i Azure Monitor för virtuella datorer, inventering, ändringsspårning och uppdatering av Azure automation-funktioner för virtuella datorer i Azure Stack, måste du först aktivera dessa lösningar i Azure.

> [!TIP]
> Om du redan har dessa funktioner som aktiverats för virtuella Azure-datorer kan använda du dina befintliga LogAnalytics arbetsyta-autentiseringsuppgifter. Om du redan har en LogAnalytics WorkspaceID och den primärnyckeln som du vill använda kan gå vidare till [nästa avsnitt](./vm-update-management.md#in-the-azure-stack-administration-portal). I annat fall Fortsätt i det här avsnittet för att skapa ett nytt LogAnalytics arbetsytan och automation-konto.

Det första steget i aktiveringen av dessa lösningar är att [skapa en arbetsyta för LogAnalytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace) i Azure-prenumerationen. En Log Analytics-arbetsyta är en unik Azure Monitor-loggar miljö med en egen databas, datakällor och lösningar. När du har skapat en arbetsyta, Observera WorkspaceID och nyckel. Gå till bladet för arbetsytan om du vill visa den här informationen, klickar du på **avancerade inställningar**, och granska de **arbetsyte-ID** och **primärnyckel** värden. 

Därefter måste du [skapa ett Automation-konto](https://docs.microsoft.com/azure/automation/automation-create-standalone-account). Ett Automation-konto är en behållare för dina Azure Automation-resurser. Det är ett sätt att avgränsa dina miljöer eller att ytterligare ordna dina automatiseringsarbetsflöden och resurser. När automation-kontot har skapats kan behöver du aktivera lagret, spårning och uppdatera hanteringsfunktioner. Följ dessa steg om du vill aktivera varje funktion för att göra detta:

1. Gå till Automation-kontot som du vill använda i Azure-portalen.

2. Välj lösning för att aktivera (antingen **inventering**, **ändringsspårning**, eller **uppdateringshantering**).

3. Använd den **Välj arbetsyta...**  listrutan att välja att använda Log Analytics-arbetsytan.

4. Kontrollera att alla återstående information är korrekt och klicka sedan på **aktivera** att aktivera lösningen.

5. Upprepa steg 2 – 4 för att aktivera alla tre lösningar. 

   [![](media/vm-update-management/1-sm.PNG "Aktivera funktioner för automation-konto")](media/vm-update-management/1-lg.PNG#lightbox)

### <a name="enable-azure-monitor-for-vms"></a>Aktivera Azure Monitor för virtuella datorer

Azure Monitor för virtuella datorer övervakar dina Azure-datorer (VM) och VM-skalningsuppsättningar i stor skala. Den analyserar prestandan och hälsotillståndet för dina Windows- och Linux-datorer och övervakar sina processer och beroenden på andra resurser och externa processer.

Som en lösning för har Azure Monitor för virtuella datorer stöd för övervakning av prestanda och programberoenden för virtuella datorer som hanteras lokalt eller i en annan molnleverantör. Tre huvudfunktioner ger djupgående insikter:

1. Logiska komponenterna i Azure virtuella datorer som kör Windows och Linux: Mäts mot förkonfigurerade health-villkor, och de varnar dig när det utvärderade villkoret uppfylls. 

2. Fördefinierade populära prestandadiagram: Visa core prestandamått från gästoperativsystemet för virtuell dator.

3. Beroendekarta: Visar komponenterna som är sammankopplade med den virtuella datorn från olika resursgrupper och prenumerationer.

När Log Analytics-arbetsytan skapas, måste du aktivera prestandaräknare på arbetsytan för samlingen på Linux- och Windows-datorer, samt installera och aktivera ServiceMap och InfrastructureInsights lösningen i din arbetsyta. Processen beskrivs i den [distribuera Azure Monitor för virtuella datorer](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-onboard#deploy-azure-monitor-for-vms) guide.

### <a name="in-the-azure-stack-administration-portal"></a>I Azure Stack-administrationsportalen
När du har aktiverat Azure Automation-lösningar i Azure-portalen, därefter måste du logga in på administrationsportalen för Azure Stack som en administratör för moln och ladda ned den **Azure Monitor-, Update- och konfigurationshantering** och **Azure Monitor-, Update- och konfigurationshantering för Linux** tillägget Azure Stack marketplace-objekt. 

   ![Azure Monitor, uppdatering och konfiguration hantering tillägget marketplace-objekt](media/vm-update-management/2.PNG) 

Om du vill aktivera i Azure Monitor för virtuella datorer kartan lösning och få insikter om nätverk beroenden, måste du också hämta den **Beroendeagenten för Azure Monitor**:

   ![Azure Monitor Dependency Agent](media/vm-update-management/2-dependency.PNG) 

## <a name="enable-update-management-for-azure-stack-virtual-machines"></a>Aktivera uppdateringshantering för virtuella datorer i Azure Stack
Följ dessa steg om du vill aktivera uppdateringshantering för virtuella datorer i Azure Stack.

1. Logga in på användarportalen för Azure Stack.

2. I Azure Stack-användarportalen, går du till bladet tillägg för virtuella datorer som du vill aktivera dessa lösningar, klickar du på **+ Lägg till**väljer den **Azure-uppdateringen och konfigurationshantering** tillägget och klickar på **skapa**:

   [![](media/vm-update-management/3-sm.PNG "Bladet för VM-tillägg")](media/vm-update-management/3-lg.PNG#lightbox)

3. Ange den tidigare skapade WorkspaceID och den primärnyckeln för att länka agenten med arbetsytan LogAnalytics och klicka på **OK** att distribuera tillägget.

   [![](media/vm-update-management/4-sm.PNG "Att tillhandahålla WorkspaceID och nyckel")](media/vm-update-management/4-lg.PNG#lightbox) 

4. Mer information finns i den [dokumentation om automation update management](https://docs.microsoft.com/azure/automation/automation-update-management), måste du aktivera lösningen för uppdateringshantering för varje virtuell dator som du vill hantera. Välj för att aktivera lösningen för alla virtuella datorer som rapporterar till arbetsytan **uppdateringshantering**, klickar du på **hantera datorer**, och välj sedan den **aktivera på alla tillgängliga och framtida datorer** alternativet.

   [![](media/vm-update-management/5-sm.PNG "Att tillhandahålla WorkspaceID och nyckel")](media/vm-update-management/5-lg.PNG#lightbox) 

   > [!TIP]
   > Upprepa detta steg om du vill aktivera varje lösning för Azure Stack virtuella datorer som rapporterar till arbetsytan. 
  
När tillägget för Azure-uppdateringen och konfigurationshantering är aktiverat, utförs en genomsökning två gånger per dag för varje hanterad virtuell dator. API: et anropas varje kvart att fråga efter den senaste uppdateringstiden att fastställa om statusen har ändrats. Om statusen har ändrats, har en fullständig genomsökning initierats.

När de virtuella datorerna har genomsökts visas de i Azure Automation-konto i lösningen för uppdateringshantering: 

   [![](media/vm-update-management/6-sm.PNG "Att tillhandahålla WorkspaceID och nyckel")](media/vm-update-management/6-lg.PNG#lightbox) 

> [!IMPORTANT]
> Det kan ta mellan 30 minuter och 6 timmar innan instrumentpanelen visar uppdaterade data från hanterade datorer.

De virtuella datorer i Azure Stack ingår nu i schemalagda uppdateringsdistributioner tillsammans med virtuella Azure-datorer.

## <a name="enable-azure-monitor-for-vms-running-on-azure-stack"></a>Aktivera Azure Monitor för virtuella datorer som körs på Azure Stack
När den virtuella datorn har den **Azure Monitor-, Update- och konfigurationshantering** och **Beroendeagenten för Azure Monitor** tillägg har installerats, den börjar rapporterar data de [Azure Monitor för virtuella datorer](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview) lösning. 

> [!TIP]
> Den **Beroendeagenten för Azure Monitor** tillägget kräver inte några parametrar. I Azure Monitor för virtuella datorer kartan beroendeagenten överföra inte några data själva och det kräver inte ändringar i brandväggar eller portar. Kartdata överförs alltid genom Log Analytics-agenten i Azure Monitor-tjänsten, antingen direkt eller via den [OMS-gatewayen](https://docs.microsoft.com/azure/azure-monitor/platform/gateway) om din IT-säkerhetsprinciper inte tillåter att datorer i nätverket för att ansluta till internet.

Azure Monitor för virtuella datorer innehåller en uppsättning prestandadiagram som är inriktade på flera nyckeltal (KPI: er) för att bestämma hur bra en virtuell dator fungerar. Diagrammen visar Resursanvändning under en viss tid så att du kan identifiera flaskhalsar, avvikelser, eller växla till ett perspektiv som lista varje dator om du vill visa resursanvändningen baserat på mått som har valts. Det finns ett stort antal element att överväga när du hanterar prestanda, rör Azure Monitor för nyckeltal för virtuella datorer Övervakare viktiga operativsystemet processor, minne, nätverkskort och diskanvändning. Prestanda kompletterar hälsotillstånd övervakningsfunktionen och hjälper till att exponera problem som indikerar ett möjligt system komponentfel, support justering och optimering för att uppnå effektivitet eller stöd för kapacitetsplanering.

   ![Fliken för Azure Monitor-prestanda](https://docs.microsoft.com/azure/azure-monitor/insights/media/vminsights-performance/vminsights-performance-aggview-01.png)

Visa de identifierade programkomponenterna i Windows och Linux-datorer som körs i Azure Stack kan observeras på två sätt med Azure Monitor för virtuella datorer från en virtuell dator direkt eller i grupper med virtuella datorer från Azure Monitor.
Den [med hjälp av Azure Monitor för virtuella datorer (förhandsversion) kartan för att förstå programkomponenter](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-maps) artikeln hjälper dig att förstå upplevelse mellan två perspektiv och hur du använder funktionen kartan.

   ![Fliken för Azure Monitor-prestanda](https://docs.microsoft.com/azure/azure-monitor/insights/media/vminsights-maps/map-multivm-azure-monitor-01.png)


## <a name="enable-update-management-using-a-resource-manager-template"></a>Aktivera uppdateringshantering med en Resource Manager-mall
Om du har ett stort antal virtuella datorer i Azure Stack kan du använda [Azure Resource Manager-mallen](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) att enkelt distribuera lösningen på virtuella datorer. Mallen distribuerar tillägget Microsoft Monitoring Agent till en befintlig Azure Stack VM och lägger till den till en befintlig Azure LogAnalytics arbetsyta.
 
## <a name="next-steps"></a>Nästa steg
[Optimera prestanda för SQL Server-VM](azure-stack-sql-server-vm-considerations.md)




