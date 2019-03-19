---
title: Självstudier – Övervaka och uppdatera virtuella Windows-datorer i Azure | Microsoft Docs
description: I den här självstudiekursen lär du dig hur du övervakar startdiagnostik och prestandamått och hur du hanterar paketuppdateringar på en virtuell Windows-dator
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 32e92cb8cd6cd5d16ea8d38d178bb440420e6784
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57546372"
---
# <a name="tutorial-monitor-and-update-a-windows-virtual-machine-in-azure"></a>Självstudier: Övervaka och uppdatera en virtuell Windows-dator i Azure

Övervakningsfunktionerna i Azure använder agenter för att samla in start- och prestandadata från virtuella Azure-datorer. Dessa data lagras sedan i Azure Storage och görs tillgängliga via portalen, Azure PowerShell-modulen och Azure CLI. Med uppdateringshantering kan du hantera uppdateringar och korrigeringar för dina virtuella Azure Windows-datorer.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Aktivera startdiagnostik på en virtuell dator
> * Visa startdiagnostik
> * Visa statistik för en virtuell värddator
> * Installera diagnostiktillägget
> * Visa VM-mått
> * Skapa en avisering
> * Hantera Windows-uppdateringar
> * Övervaka ändringar och inventering
> * Konfigurera avancerad övervakning

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell i en separat webbläsarflik genom att gå till [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

För att konfigurera övervaknings- och uppdateringshantering i Azure i den här självstudiekursen behöver du en virtuell Windows-dator i Azure. Ange först ett administratörsanvändarnamn och lösenord för den virtuella datorn med [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Skapa nu den virtuella datorn med hjälp av [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). I följande exempel skapas en virtuell dator med namnet *myVM* på platsen *EastUS* (Östra USA). Resursgruppen *myResourceGroupMonitorMonitor* och ytterligare nätverksresurser som behövs skapas om de inte redan finns:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Det tar några minuter att skapa resurserna och den virtuella datorn.

## <a name="view-boot-diagnostics"></a>Visa startdiagnostik

När virtuella Windows-datorer startar samlar startdiagnostikagenten in skärmutdata som kan användas i felsökningssyfte. Den här funktionen är aktiverad som standard. Hämtade skärmbilderna lagras i ett Azure storage-konto, som dessutom skapas som standard.

Du kan hämta startdiagnostikdata med kommandot [Get-AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/az.compute/get-azvmbootdiagnosticsdata). I följande exempel laddas startdiagnostik ned till roten på enhet *c:\*.

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName "myResourceGroupMonitor" -Name "myVM" -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>Visa värdmått

En virtuell Windows-dator har en dedikerad virtuell värddator i Azure som den interagerar med. Mått samlas in automatiskt för värden och kan visas på Azure Portal.

1. I Azure Portal: Klicka på **Resource Groups**, välj **myResourceGroupMonitor** och välj sedan **myVM** i resurslistan.
2. Om du vill visa den virtuella värddatorns status klickar du på **Mått** på bladet Virtuell dator och väljer något av värdmåtten under **Tillgängliga mått**.

    ![Visa värdmått](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>Installera diagnostiktillägget

Grundläggande värdmått är tillgängliga, men om du vill se mer detaljerade och VM-specifika mått måste du installera Azure-diagnostiktillägget på den virtuella datorn. Med Azure-diagnostiktillägget kan du få ut mer övervaknings- och diagnostikdata från den virtuella datorn. Du kan visa dessa prestandamått och skapa aviseringar baserat på hur det går för den virtuella datorn. Diagnostiktillägget installeras via Azure Portal på följande sätt:

1. I Azure Portal: Klicka på **Resource Groups**, välj **myResourceGroupMonitor** och välj sedan **myVM** i resurslistan.
2. Klicka på **Diagnosinställningar**. I listan kan du se att *Startdiagnostik* redan har aktiverats i föregående avsnitt. Markera kryssrutan för *Basmått*.
3. Klicka på **Aktivera övervakning på gästnivå**.

    ![Visa diagnostikmått](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>Visa VM-mått

Du kan visa VM-mått på samma sätt som du visade VM-värdmått:

1. I Azure Portal: Klicka på **Resource Groups**, välj **myResourceGroupMonitor** och välj sedan **myVM** i resurslistan.
2. Om du vill se hur det går för den virtuella datorn klickar du på **Mått** på bladet för den virtuella datorn och sedan på något av diagnostikmåtten under **Tillgängliga mått**.

    ![Visa VM-mått](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>Skapa aviseringar

Du kan skapa aviseringar baserat på specifika prestandamått. Aviseringar kan till exempel användas för att informera dig om när den genomsnittliga CPU-användning överskrider ett visst tröskelvärde eller när det lediga diskutrymmet understiger ett visst värde. Aviseringar kan visas i Azure Portal eller skickas via e-post. Du kan också utlösa Azure Automation-runbooks eller Azure Logic Apps när aviseringar skapas.

I följande exempel skapas en avisering för genomsnittlig CPU-användning.

1. I Azure Portal: Klicka på **Resource Groups**, välj **myResourceGroupMonitor** och välj sedan **myVM** i resurslistan.
2. Klicka på **Aviseringsregler** på bladet för den virtuella datorn och klicka sedan på **Lägg till metrisk varning** längst upp på aviseringsbladet.
3. Ange ett **namn** för aviseringen, till exempel *myAlertRule*
4. Om du vill utlösa en avisering när CPU-procenten har varit högre än 1,0 i fem minuter lämnar du alla andra standardinställningar valda.
5. Du kan också markera kryssrutan för *E-postägare, deltagare och läsare* om du vill skicka ett e-postmeddelande. Standardåtgärden är att visa en avisering i portalen.
6. Klicka på **OK**.

## <a name="manage-windows-updates"></a>Hantera Windows-uppdateringar

Med uppdateringshantering kan du hantera uppdateringar och korrigeringar för dina virtuella Azure Windows-datorer.
Du kan snabbt se status för tillgängliga uppdateringar, schemalägga installation av nödvändiga uppdateringar och granska distributionsresultat för att verifiera att uppdateringarna har tillämpats på den virtuella datorn, direkt från den virtuella datorn.

Prisinformation finns i [Automation-priser för uppdateringshantering](https://azure.microsoft.com/pricing/details/automation/)

### <a name="enable-update-management"></a>Aktivera uppdateringshantering

Så här aktiverar du uppdateringshantering för dina virtuella datorer:

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

Välj det Log Analytics-arbetsytan och automation-kontot och klicka på **aktivera** att aktivera lösningen. Det tar upp till 15 minuter att aktivera lösningen.

Om några av följande krav saknades under publiceringen läggs de till automatiskt:

* [Log Analytics](../../log-analytics/log-analytics-overview.md)-arbetsyta
* [Automation](../../automation/automation-offering-get-started.md)
* En [Hybrid runbook worker](../../automation/automation-hybrid-runbook-worker.md) aktiveras på den virtuella datorn

Skärmen **Uppdateringshantering** öppnas. Konfigurera platsen, Log Analytics-arbetsytan och Automation-konto för att använda och klicka på **aktivera**. Om fälten är nedtonade betyder det att någon annan automatiseringslösning är aktiverad för den virtuella datorn, och samma arbetsyta och Automation-konto måste användas.

![Aktivera lösningen för hantering av uppdateringar](./media/tutorial-monitoring/manageupdates-update-enable.png)

Det kan ta upp till 15 minuter att aktivera lösningen. Under tiden ska du inte stänga webbläsaren. När lösningen har aktiverats flödar information om saknade uppdateringar på den virtuella datorn till Azure Monitor-loggar. Det kan ta mellan 30 minuter och 6 timmar innan data blir tillgängliga för analys.

### <a name="view-update-assessment"></a>Visa kontroll av uppdateringar

När **uppdateringshantering** är aktiverat visas skärmen **Hantering av uppdateringar**. När utvärderingen av uppdateringarna är klar ser du en lista med uppdateringar som saknas på fliken  **Uppdateringar som saknas**.

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
> Om en uppdatering kräver omstart startas den virtuella datorn om automatiskt.

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

## <a name="monitor-changes-and-inventory"></a>Övervaka ändringar och inventering

Du kan samla in och visa inventeringar för program, filer, Linux-daemons, Windows-tjänster och Windows-registernycklar på dina datorer. Om du spårar konfigurationerna för dina datorer kan du identifiera driftproblem i miljön och bättre förstå datorernas tillstånd.

### <a name="enable-change-and-inventory-management"></a>Aktivera ändringsspårning och inventering

Så här aktiverar du ändringsspårning och inventering för din virtuella dator:

1. Välj **Virtuella datorer** till vänster på skärmen.
2. Välj en virtuell dator i listan.
3. Gå till avsnittet **Åtgärder** och klicka på **Inventering** eller **Ändringsspårning**. Skärmen **Aktivera ändringsspårning och inventering** öppnas.

Konfigurera platsen, Log Analytics-arbetsytan och Automation-konto för att använda och klicka på **aktivera**. Om fälten är nedtonade betyder det att någon annan automatiseringslösning är aktiverad för den virtuella datorn, och samma arbetsyta och Automation-konto måste användas. Även om lösningarna är uppdelade på menyn, ingår de i samma lösning. När du aktiverar den ena aktiveras båda för den virtuella datorn.

![Aktivera Ändringsspårning och lager](./media/tutorial-monitoring/manage-inventory-enable.png)

När lösningen har aktiverats kan det ta en stund medan lagerdata samlas in på den virtuella datorn innan data visas.

### <a name="track-changes"></a>Spåra ändringar

Välj **Ändringsspårning** under **ÅTGÄRDER** på den virtuella datorn. Klicka på **Redigera inställningar**. Sidan **Ändringsspårning** visas. Välj typ av inställning som du vill spåra och klicka sedan på **+ Lägg till** för att konfigurera inställningarna. De tillgängliga alternativen för Windows är:

* Windows-registret
* Windows-filer

Mer information om ändringsspårning finns i [Felsöka ändringar på en virtuell dator](../../automation/automation-tutorial-troubleshoot-changes.md)

### <a name="view-inventory"></a>Visa inventering

Välj **Inventering** under **ÅTGÄRDER** på den virtuella datorn. På fliken **Programvara** finns det en tabell som anger programvaran som har hittats. Utförlig information för varje programpost visas i tabellen. Uppgifterna omfattar bland annat programvarunamn, version, utgivare och datum för senaste uppdatering.

![Visa inventering](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Övervaka aktivitetsloggar och ändringar

Från sidan **Ändringsspårning** på din virtuella dator väljer du **Hantera aktivitetslogganslutning**. Den här uppgiften öppnar sidan **Azure-aktivitetslogg**. Välj **Anslut** för att ansluta Ändringsspårning till Azure-aktivitetsloggen för din virtuella dator.

När den här inställningen är aktiverad går du till sidan **Översikt** för din virtuella dator och väljer **Stoppa** för att stoppa din virtuella dator. När du uppmanas väljer du **Ja** för att stoppa den virtuella datorn. När den har frigjorts väljer du **Starta** för att starta om din virtuella dator.

När en virtuell dator stoppas och startas loggar den virtuella datorn en händelse i aktivitetsloggen. Gå tillbaka till sidan **Ändringsspårning**. Välj fliken **Händelser** längst ned på sidan. Efter ett tag visas händelserna i diagrammet och tabellen. Du kan välja varje händelse för att visa detaljerad information om händelsen.

![Visa händelser i aktivitetsloggen](./media/tutorial-monitoring/manage-activitylog-view-results.png)

Diagrammet visar ändringar som har skett över tid. När du har lagt till en aktivitetslogganslutning visar linjediagrammet högst upp händelser i Azure-aktivitetsloggen. Varje rad med stapeldiagram representerar en annan spårningsbar ändringstyp. Typerna är Linux-daemons, filer, Windows-registernycklar, programvara och Windows-tjänster. På fliken Ändra visas information för de ändringar som visas i visualiseringen i fallande tidsordning för när ändringen skedde (senaste först).

## <a name="advanced-monitoring"></a>Avancerad övervakning

Du kan använda mer avancerad övervakning av den virtuella datorn med lösningar som uppdateringshantering, ändringsspårning och inventering som tillhandahålls av [Azure Automation](../../automation/automation-intro.md).

När du har åtkomst till Log Analytics-arbetsytan hittar du nyckel och identifierare för arbetsytan genom att välja **Avancerade inställningar** under **INSTÄLLNINGAR**. Använd kommandot [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) för att lägga till Microsoft Monitoring Agent-tillägget i den virtuella datorn. Uppdatera variabelvärdena i exemplet nedan så att de återspeglar din Log Analytics-arbetsyta och arbetsytans ID.

```powershell
$workspaceId = "<Replace with your workspace Id>"
$key = "<Replace with your primary key>"

Set-AzVMExtension -ResourceGroupName "myResourceGroupMonitor" `
  -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
  -VMName "myVM" `
  -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
  -ExtensionType "MicrosoftMonitoringAgent" `
  -TypeHandlerVersion 1.0 `
  -Settings @{"workspaceId" = $workspaceId} `
  -ProtectedSettings @{"workspaceKey" = $key} `
  -Location "East US"
```

Du bör se den nya virtuella datorn på Log Analytics-arbetsytan efter några minuter.

![Log Analytics-arbetsytebladet](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen konfigurerade du och granskade virtuella datorer med Azure Security Center. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk
> * Skapa en resursgrupp och virtuell dator
> * Aktivera startdiagnostik på den virtuella datorn
> * Visa startdiagnostik
> * Visa värdmått
> * Installera diagnostiktillägget
> * Visa VM-mått
> * Skapa en avisering
> * Hantera Windows-uppdateringar
> * Övervaka ändringar och inventering
> * Konfigurera avancerad övervakning

Gå vidare till nästa kurs om du vill veta mer om Azure Security Center.

> [!div class="nextstepaction"]
> [Hantera säkerheten för virtuella datorer](./tutorial-azure-security.md)
