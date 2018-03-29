---
title: Azure övervakning och uppdatering och Windows-datorer | Microsoft Docs
description: Självstudiekurs – övervaka och uppdatera en virtuell dator i Windows med Azure PowerShell
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: fdb8009e3dbca1037cae61ec8627f73190a8263d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="monitor-and-update-a-windows-virtual-machine-with-azure-powershell"></a>Övervaka och uppdatera en virtuell Windows-dator med Azure PowerShell

Azure övervakning använder agenter för att samla in start-och prestandadata från Azure virtuella datorer, lagra dessa data i Azure-lagring och göra det tillgängligt via portalen, Azure PowerShell-modulen och Azure CLI. Uppdateringshantering kan du hantera uppdateringar och korrigeringsfiler för virtuella datorerna i Windows Azure.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Aktivera startdiagnostikinställningar på en virtuell dator
> * Visa startdiagnostik
> * Visa VM värden mått
> * Installera tillägget diagnostik
> * Visa VM-mått
> * Skapa en avisering
> * Hantera Windows-uppdateringar
> * Konfigurera avancerad övervakning

Den här självstudien kräver Azure PowerShell-modul version 3.6 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).

Du måste ha en befintlig virtuell dator för att kunna utföra exemplet i självstudien. Om det behövs kan du skapa en med detta [skriptexempel](../scripts/virtual-machines-windows-powershell-sample-create-vm.md). När du arbetar med kursen, Ersätt resursgrupp, namn och plats där det behövs.

## <a name="view-boot-diagnostics"></a>Visa startdiagnostik

Eftersom Windows-datorer starta avbildar Start diagnostikagenten utdata som kan användas för felsökning. Den här funktionen är aktiverad som standard. Fångade skärmdumpar lagras i Azure storage-konto som skapas också som standard.

Du kan hämta Start diagnostikdata med den [Get-AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmbootdiagnosticsdata) kommando. I följande exempel hämtas startdiagnostikinställningar till roten av den * c:\* enhet.

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup -Name myVM -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>Visa värdmått

En virtuell Windows-dator har en dedikerad värd eller virtuell dator i Azure som den interagerar med. Mått samlas in automatiskt för värden och kan visas i Azure-portalen.

1. I Azure Portal: Klicka på **Resursgrupper**, välj **myResourceGroup** och välj sedan **myVM** i resurslistan.
2. Klicka på **mått** på VM-bladet och välj sedan någon av mätvärdena som är värd under **tillgängliga mått** att se hur den Virtuella värden utförs.

    ![Visa värdmått](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>Installera diagnostiktillägget

De grundläggande värden är tillgänglig, men att se mer detaljerad och VM-specifika statistik, och du behöver installera Azure-diagnostik-tillägget på den virtuella datorn. Med Azure-diagnostiktillägget kan du få ut mer övervaknings- och diagnostikdata från den virtuella datorn. Du kan visa dessa prestandamått och skapa aviseringar baserat på hur det går för den virtuella datorn. Diagnostiktillägget installeras via Azure Portal på följande sätt:

1. I Azure Portal: Klicka på **Resursgrupper**, välj **myResourceGroup** och välj sedan **myVM** i resurslistan.
2. Klicka på **Diagnosinställningar**. I listan kan du se att *Startdiagnostik* redan har aktiverats i föregående avsnitt. Markera kryssrutan för *Basmått*.
3. Klicka på den **aktivera övervakning av gästnivå** knappen.

    ![Visa diagnostikmått](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>Visa VM-mått

Du kan visa VM-mått på samma sätt som du visade VM-värdmått:

1. I Azure Portal: Klicka på **Resursgrupper**, välj **myResourceGroup** och välj sedan **myVM** i resurslistan.
2. Om du vill se hur det går för den virtuella datorn klickar du på **Mått** på bladet för den virtuella datorn och sedan på något av diagnostikmåtten under **Tillgängliga mått**.

    ![Visa VM-mått](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>Skapa aviseringar

Du kan skapa aviseringar baserat på specifika prestandamått. Aviseringar kan till exempel användas för att informera dig om när den genomsnittliga CPU-användning överskrider ett visst tröskelvärde eller när det lediga diskutrymmet understiger ett visst värde. Aviseringar kan visas i Azure Portal eller skickas via e-post. Du kan också utlösa Azure Automation-runbooks eller Azure Logic Apps när aviseringar skapas.

I följande exempel skapas en avisering för genomsnittlig CPU-användning.

1. I Azure Portal: Klicka på **Resursgrupper**, välj **myResourceGroup** och välj sedan **myVM** i resurslistan.
2. Klicka på **Aviseringsregler** på bladet för den virtuella datorn och klicka sedan på **Lägg till metrisk varning** längst upp på aviseringsbladet.
3. Ange ett **namn** för aviseringen, till exempel *myAlertRule*
4. Om du vill utlösa en avisering när CPU-procenten har varit högre än 1,0 i fem minuter lämnar du alla andra standardinställningar valda.
5. Du kan också markera kryssrutan för *E-postägare, deltagare och läsare* om du vill skicka ett e-postmeddelande. Standardåtgärden är att visa en avisering i portalen.
6. Klicka på **OK**.

## <a name="manage-windows-updates"></a>Hantera Windows-uppdateringar

Uppdateringshantering kan du hantera uppdateringar och korrigeringsfiler för virtuella datorerna i Windows Azure.
Du kan snabbt se status för tillgängliga uppdateringar, schemalägga installation av nödvändiga uppdateringar och granska distributionsresultat för att verifiera att uppdateringarna har tillämpats på den virtuella datorn, direkt från den virtuella datorn.

Prisinformation finns i [Automation-priser för uppdateringshantering](https://azure.microsoft.com/pricing/details/automation/)

### <a name="enable-update-management"></a>Aktivera uppdateringshantering

Aktivera uppdateringshantering för den virtuella datorn:

1. Välj **Virtuella datorer** till vänster på skärmen.
2. Välj en virtuell dator i listan.
3. Klicka på **Hantering av uppdateringar** i avsnittet **Åtgärder** på VM-skärmen. Skärmen **Aktivera hantering av uppdateringar** visas.

Verifieringen utförs för att fastställa om uppdateringshantering är aktiverat för den här virtuella datorn.
Verifieringen söker efter en Log Analytics-arbetsyta och ett länkat Automation-konto, och om lösningen är i arbetsytan.

En [Log Analytics](../../log-analytics/log-analytics-overview.md)-arbetsyta används för att samla in data som genereras av funktioner och tjänster som uppdateringshantering.
Arbetsytan tillhandahåller en enda plats för att granska och analysera data från flera källor.
Om du vill utföra ytterligare åtgärder på virtuella datorer som kräver uppdateringar kan Azure Automation köra runbooks mot virtuella datorer, till exempel ladda ned och installera uppdateringar.

Verifieringsprocessen kontrollerar också om den virtuella datorn har etablerats med MMA och Automation Hybrid Runbook Worker.
Den här agenten används för att kommunicera med den virtuella datorn och hämta information om uppdateringsstatus.

Välj Log Analytics-arbetsytan och Automation-kontot och klicka på **Aktivera** för att aktivera lösningen. Det tar upp till 15 minuter att aktivera lösningen.

Om några av följande krav saknades under publiceringen läggs de till automatiskt:

* [Log Analytics](../../log-analytics/log-analytics-overview.md)-arbetsyta
* [Automation](../../automation/automation-offering-get-started.md)
* En [Hybrid runbook worker](../../automation/automation-hybrid-runbook-worker.md) aktiveras på den virtuella datorn

Skärmen **Uppdateringshantering** öppnas. Konfigurera platsen, Log Analytics-arbetsytan och Automation-kontot som ska användas och klicka på **Aktivera**. Om fälten är nedtonade betyder det att någon annan automatiseringslösning är aktiverad för den virtuella datorn, och samma arbetsyta och Automation-konto måste användas.

![Aktivera lösningen för hantering av uppdateringar](./media/tutorial-monitoring/manageupdates-update-enable.png)

Det kan ta upp till 15 minuter att aktivera lösningen. Under tiden ska du inte stänga webbläsaren. När lösningen har aktiverats flödar information om saknade uppdateringar på den virtuella datorn till Log Analytics. Det kan ta mellan 30 minuter och 6 timmar innan data blir tillgängliga för analys.

### <a name="view-update-assessment"></a>Visa kontroll av uppdateringar

När **uppdateringshantering** är aktiverat visas skärmen **Hantering av uppdateringar**. Efter utvärdering av uppdateringar är klar kan du visa en lista med uppdateringar som saknas på den **saknar uppdateringar** fliken.

 ![Visa uppdateringsstatus](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Schemalägga en uppdateringsdistribution

För att installera uppdateringar schemalägger du en distribution som passar ditt schema och servicefönster. Du kan välja vilka uppdateringstyper som ska tas med i distributionen. Du kan till exempel ta med kritiska uppdateringar eller säkerhetsuppdateringar och exkludera samlade uppdateringar.

Schemalägg en ny uppdateringsdistribution för den virtuella datorn genom att klicka på **Distribution av schemauppdatering** längst upp på skärmen **Hantering av uppdateringar**. På skärmen **Ny uppdateringsdistribution** anger du följande information:

* **Namn** – Ett unikt namn som identifierar uppdateringsdistributionen.
* **Uppdatera klassificering** – Välj vilka typer av programvara som ska tas med i uppdateringsdistributionen. Klassificeringstyper:
  * Kritiska uppdateringar
  * Säkerhetsuppdateringar
  * Samlade uppdateringar
  * Funktionspaket
  * Service pack
  * Definitionsuppdateringar
  * Verktyg
  * Uppdateringar

* **Schemainställningar** – Du kan antingen godkänna standarddatumet och -tiden, d.v.s. 30 minuter efter aktuell tid, eller så kan du ange en annan tid.
  Du kan också ange om distributionen ska ske en gång eller ange ett schema med återkommande tider. Klicka på alternativet Återkommande under Återkommande för att ange ett återkommande schema.

  ![Inställningsskärmen för uppdateringsschema](./media/tutorial-monitoring/manageupdates-schedule-win.png)

* **Underhållsperiod (minuter)** – Ange den tidsperiod som uppdateringsdistributionen ska utföras inom.  På så sätt försäkrar du dig om att ändringarna utförs inom ditt definierade servicefönster.

När du har konfigurerat schemat klickar du på **Skapa**. Därmed återgår du till statusinstrumentpanelen.
Observera att tabellen **Schemalagt** visar det distributionsschema som du skapade.

> [!WARNING]
> Den virtuella datorn startas automatiskt efter uppdateringar som kräver en omstart.

### <a name="view-results-of-an-update-deployment"></a>Visa resultat för en uppdateringsdistribution

När den schemalagda distributionen startar kan du se status för distributionen på fliken **Uppdateringsdistributioner** på skärmen **Uppdateringshantering**.
Om distributionen körs visas status **Pågår**. När distributionen har slutförts ändras status till **Lyckades**.
Om det uppstod något fel med en eller flera uppdateringar i distributionen visas status **Misslyckades delvis**.
Klicka på den slutförda uppdateringsdistributionen för att visa instrumentpanelen för distributionen.

![Statusinstrumentpanel för Uppdatera distribution för specifik distribution](./media/tutorial-monitoring/manageupdates-view-results.png)

På panelen för **uppdateringsresultat** visas en sammanfattning av det totala antalet uppdateringar och distributionsresultat för den virtuella datorn.
I tabellen till höger visas detaljer för varje uppdatering och installationsresultaten, som kan ha ett av följande värden:

* **Inget försök har gjorts** – Uppdateringen installerades inte eftersom det inte fanns tillräckligt med tid utifrån det underhållsfönster som definierats.
* **Lyckades** – Uppdateringen lyckades
* **Misslyckades** – Uppdateringen misslyckades

Klicka på **Alla loggar** om du vill se alla loggposter som har skapats för distributionen.

Klicka på panelen **Utdata** om du vill se jobbströmmen för den runbook som ansvarar för att hantera uppdateringsdistributionen på den virtuella måldatorn.

Klicka på **Fel** om du vill se detaljerad information om fel som uppstått vid distributionen.

## <a name="monitor-changes-and-inventory"></a>Övervaka ändringar och lager

Du kan samla in och visa inventeringar för program, filer, Linux-daemons, Windows-tjänster och Windows-registernycklar på dina datorer. Om du spårar konfigurationerna för dina datorer kan du identifiera driftproblem i miljön och bättre förstå datorernas tillstånd.

### <a name="enable-change-and-inventory-management"></a>Aktivera ändrings-och Programvaruinventering

Aktivera ändrings- och lager hantering för den virtuella datorn:

1. Välj **Virtuella datorer** till vänster på skärmen.
2. Välj en virtuell dator i listan.
3. På skärmen VM i den **Operations** klickar du på **inventering** eller **ändringsspårning**. Den **Aktivera spårning av ändringar och inventering** skärmen öppnas.

Konfigurera platsen, Log Analytics-arbetsytan och Automation-kontot som ska användas och klicka på **Aktivera**. Om fälten är nedtonade betyder det att någon annan automatiseringslösning är aktiverad för den virtuella datorn, och samma arbetsyta och Automation-konto måste användas. Eventhough lösningarna är separat på menyn, de är samma lösning. Aktiverar en kan både för den virtuella datorn.

![Aktivera ändrings- och spåra lager](./media/tutorial-monitoring/manage-inventory-enable.png)

När lösningen har aktiverats kan det ta en stund medan inventering som samlas in på den virtuella datorn innan data visas.

### <a name="track-changes"></a>Spåra ändringar

På VM-Välj **ändringsspårning** under **OPERATIONS**. Klicka på **redigera inställningar för**, **ändringsspårning** visas. Välj typ av inställning du vill spåra och klicka sedan på **+ Lägg till** att konfigurera inställningarna. Tillgängliga alternativ för Windows är:

* Windows-registret
* Windows-filer

Detaljerad information om spårning av ändringar finns [felsöka ändringar på en virtuell dator](../../automation/automation-tutorial-troubleshoot-changes.md)

### <a name="view-inventory"></a>Visa lager

På VM-Välj **inventering** under **OPERATIONS**. På fliken **Programvara** finns det en tabell som anger programvaran som har hittats. Utförlig information för varje programpost visas i tabellen. Dessa uppgifter inkluderar programvarunamn, version, utgivare, uppdateras senast.

![Visa lager](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Övervaka aktivitetsloggar och ändringar

Från sidan **Ändringsspårning** på din virtuella dator väljer du **Hantera aktivitetslogganslutning**. Den här uppgiften öppnar sidan **Azure-aktivitetslogg**. Välj **Anslut** för att ansluta Ändringsspårning till Azure-aktivitetsloggen för din virtuella dator.

När den här inställningen är aktiverad går du till sidan **Översikt** för din virtuella dator och väljer **Stoppa** för att stoppa din virtuella dator. När du uppmanas väljer du **Ja** för att stoppa den virtuella datorn. När den har frigjorts väljer du **Starta** för att starta om din virtuella dator.

När en virtuell dator stoppas och startas loggar den virtuella datorn en händelse i aktivitetsloggen. Gå tillbaka till sidan **Ändringsspårning**. Välj fliken **Händelser** längst ned på sidan. Efter ett tag visas händelserna i diagrammet och tabellen. Varje händelse kan väljas för att visa detaljerad information om händelsen.

![Visa ändringar i aktivitetsloggen](./media/tutorial-monitoring/manage-activitylog-view-results.png)

Diagrammet visar ändringar som har skett över tid. När du har lagt till en aktivitetslogganslutning visar linjediagrammet högst upp händelser i Azure-aktivitetsloggen. Varje rad med stapeldiagram representerar en annan spårningsbar ändringstyp. Typerna är Linux-daemons, filer, Windows-registernycklar, programvara och Windows-tjänster. På fliken Ändra visas information för de ändringar som visas i visualiseringen i fallande tidsordning för när ändringen skedde (senaste först).

## <a name="advanced-monitoring"></a>Avancerad övervakning

Du kan göra mer avancerad övervakning av den virtuella datorn med hjälp av lösningar som uppdateringshantering och ändra och lagret tillhandahålls av Azure Automation. [Operations Management Suite](../../automation/automation-intro.md).

När du har åtkomst till logganalys-arbetsytan hittar du den arbetsyta och arbetsytan identifierare på genom att välja **avancerade inställningar** under **inställningar**. Använd den [Set AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) kommando för att lägga till tillägg för Microsoft Monitoring agent till den virtuella datorn. Uppdatera variabelvärden i den nedan exempel för att återspegla du nyckeln för logganalys-arbetsyta och arbetsytan Id.

```powershell
$workspaceId = "<Replace with your workspace Id>"
$key = "<Replace with your primary key>"

Set-AzureRmVMExtension -ResourceGroupName myResourceGroup `
  -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
  -VMName myVM `
  -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
  -ExtensionType "MicrosoftMonitoringAgent" `
  -TypeHandlerVersion 1.0 `
  -Settings @{"workspaceId" = $workspaceId} `
  -ProtectedSettings @{"workspaceKey" = $key} `
  -Location eastus
```

Du bör se den nya virtuella datorn i arbetsytan loggen Anaytics efter några minuter.

![OMS-bladet](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>Nästa steg

I kursen får du konfigurerade och granskas virtuella datorer med Azure Security Center. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk
> * Skapa en resursgrupp och en virtuell dator
> * Aktivera startdiagnostik på den virtuella datorn
> * Visa startdiagnostik
> * Visa värdmått
> * Installera tillägget diagnostik
> * Visa VM-mått
> * Skapa en avisering
> * Hantera Windows-uppdateringar
> * Konfigurera avancerad övervakning

Gå vidare till nästa kurs vill veta mer om Azure security center.

> [!div class="nextstepaction"]
> [Hantera säkerheten för virtuella datorer](./tutorial-azure-security.md)