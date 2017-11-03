---
title: "Azure övervakning och uppdatering och Windows-datorer | Microsoft Docs"
description: "Självstudiekurs – övervaka och uppdatera en virtuell dator i Windows med Azure PowerShell"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/04/2017
ms.author: davidmu
ms.custom: mvc
ms.openlocfilehash: a37aed8b3321d3518ffd73e09f5bb21266a7e577
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-update-a-windows-virtual-machine-with-azure-powershell"></a>Övervaka och uppdatera en virtuell Windows-dator med Azure PowerShell

Azure övervakning använder agenter för att samla in start-och prestandadata från Azure virtuella datorer, lagra dessa data i Azure-lagring och göra det tillgängligt via portalen, Azure PowerShell-modulen och Azure CLI. Uppdateringshantering kan du hantera uppdateringar och korrigeringsfiler för virtuella datorerna i Windows Azure.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Aktivera startdiagnostikinställningar på en virtuell dator
> * Visa startdiagnostik
> * Visa VM värden mått
> * Installera tillägget diagnostik
> * Visa VM mått
> * Skapa en avisering
> * Hantera Windows-uppdateringar
> * Konfigurera avancerad övervakning

Den här självstudien kräver Azure PowerShell-modul version 3.6 eller senare. Kör ` Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).

Du måste ha en befintlig virtuell dator för att slutföra exemplet i den här självstudiekursen. Om det behövs, detta [skriptexempel](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) kan skapa en åt dig. När du arbetar med kursen, Ersätt resursgrupp, namn och plats där det behövs.

## <a name="view-boot-diagnostics"></a>Visa startdiagnostik

Eftersom Windows-datorer starta avbildar Start diagnostikagenten utdata som kan användas för felsökning. Den här funktionen är aktiverad som standard. Fångade skärmdumpar lagras i Azure storage-konto som skapas också som standard. 

Du kan hämta Start diagnostikdata med den [Get-AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmbootdiagnosticsdata) kommando. I följande exempel hämtas startdiagnostikinställningar till roten av den * c:\* enhet. 

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup -Name myVM -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>Visa värden mått

En virtuell Windows-dator har en dedikerad värd eller virtuell dator i Azure som den interagerar med. Mått samlas in automatiskt för värden och kan visas i Azure-portalen.

1. I Azure-portalen klickar du på **resursgrupper**väljer **myResourceGroup**, och välj sedan **myVM** i resurslistan.
2. Klicka på **mått** på VM-bladet och välj sedan någon av mätvärdena som är värd under **tillgängliga mått** att se hur den Virtuella värden utförs.

    ![Visa värden mått](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>Installera tillägg för diagnostik

De grundläggande värden är tillgänglig, men att se mer detaljerad och VM-specifika statistik, och du behöver installera Azure-diagnostik-tillägget på den virtuella datorn. Azure diagnostics-tillägget kan ytterligare övervakning och diagnostikdata som ska hämtas från den virtuella datorn. Du kan visa dessa prestandamått och skapa varningar baserat på hur den virtuella datorn utför. Diagnostiska tillägget installeras via Azure portal på följande sätt:

1. I Azure-portalen klickar du på **resursgrupper**väljer **myResourceGroup**, och välj sedan **myVM** i resurslistan.
2. Klicka på **diagnos inställningar**. I listan visas som *starta diagnostik* redan aktiverat från föregående avsnitt. Klicka på kryssrutan för *grundläggande mått*.
3. Klicka på den **aktivera övervakning av gästnivå** knappen.

    ![Visa diagnostik mått](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>Visa VM mått

Du kan visa VM mått på samma sätt som du granskat värden VM mått:

1. I Azure-portalen klickar du på **resursgrupper**väljer **myResourceGroup**, och välj sedan **myVM** i resurslistan.
2. Klicka för att se hur den virtuella datorn utför **mått** på VM-bladet och välj sedan någon av diagnostik mått under **tillgängliga mått**.

    ![Visa VM mått](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>Skapa aviseringar

Du kan skapa varningar baserat på specifika prestandamått. Aviseringar kan användas för att meddela dig när Genomsnittlig CPU-användning överskrider ett visst tröskelvärde eller tillgängligt diskutrymme sjunker under ett visst belopp, till exempel. Aviseringar visas i Azure-portalen eller kan skickas via e-post. Du kan också utlösa Azure Automation-runbooks eller Logikappar i Azure som svar på varningar genereras.

I följande exempel skapas en avisering för Genomsnittlig CPU-användning.

1. I Azure-portalen klickar du på **resursgrupper**väljer **myResourceGroup**, och välj sedan **myVM** i resurslistan.
2. Klicka på **Varna regler** på VM-bladet Klicka **Lägg till mått avisering** överst på bladet aviseringar.
4. Ange en **namn** om varningen som *myAlertRule*
5. För att utlösa en avisering när CPU-procent överskrider 1.0 5 minuter, lämnar du alla andra standardinställningar som valts.
6. Du kan också markera kryssrutan för *e-ägare, deltagare och läsare* att skicka e-postmeddelande. Standardåtgärden är att visa ett meddelande i portalen.
7. Klicka på **OK**.

## <a name="manage-windows-updates"></a>Hantera Windows-uppdateringar

Uppdateringshantering kan du hantera uppdateringar och korrigeringsfiler för virtuella datorerna i Windows Azure.
Direkt från din virtuella dator du kan snabbt bedöma statusen för uppdateringar, schemalägga installationen av obligatoriska uppdateringar och granska resultatet av distributionen för att kontrollera uppdateringar har använts på den virtuella datorn.

Information om priser finns [Automation priser för uppdateringshantering](https://azure.microsoft.com/pricing/details/automation/)

### <a name="enable-update-management"></a>Aktivera uppdateringshantering

Aktivera uppdateringshantering för den virtuella datorn:
 
1. På vänster sida av skärmen väljer **virtuella datorer**.
2. Välj en virtuell dator i listan.
3. På skärmen VM i den **Operations** klickar du på **uppdateringshantering**. Den **aktivera uppdateringshantering** skärmen öppnas.

Verifieringen utförs för att fastställa om uppdateringen management har aktiverats för den här virtuella datorn. Verifieringen inkluderar söker efter en logganalys-arbetsytan och länkade Automation-konto, och om lösningen i arbetsytan.

Logganalys-arbetsytan används för att samla in data som genereras av funktioner och tjänster, till exempel hantering av uppdateringar. Arbetsytan innehåller en enda plats för att granska och analysera data från flera källor. Om du vill utföra ytterligare åtgärder på virtuella datorer som kräver uppdateringar kan Azure Automation du köra skript mot virtuella datorer, såsom att hämta och installera uppdateringar.

Valideringen kontrollerar även om den virtuella datorn har etablerats med Microsoft Monitoring Agent (MMA) och worker-hybrid. Den här agenten används för att kommunicera med den virtuella datorn och få information om uppdateringsstatus. 

Om förutsättningarna inte uppfylls visas en banderoll som ger dig möjlighet att aktivera lösningen.

![Uppdatera Management publicera configuration banderoll](./media/tutorial-monitoring/manageupdates-onboard-solution-banner.png)

Klicka på banderollen för att aktivera lösningen. Om någon av följande förutsättningar hittades saknas efter valideringen läggs de automatiskt:

* [Logga Analytics](../../log-analytics/log-analytics-overview.md) arbetsytan
* [Automation](../../automation/automation-offering-get-started.md)
* En [runbook worker-Hybrid](../../automation/automation-hybrid-runbook-worker.md) är aktiverat på den virtuella datorn

Den **aktivera uppdateringshantering** skärmen öppnas. Konfigurera inställningarna och klicka på **aktivera**.

![Aktivera uppdateringshantering](./media/tutorial-monitoring/manageupdates-update-enable.png)

Aktivera lösningen kan ta upp till 15 minuter och under tiden ska du inte stänga webbläsarfönstret. När lösningen har aktiverats, flödar information om saknar uppdateringar på den virtuella datorn till logganalys.
Det kan ta mellan 30 minuter och 6 timmar för att data ska vara tillgängliga för analys.

### <a name="view-update-assessment"></a>Visa kontroll av uppdateringar

När **uppdateringshantering** är aktiverat visas skärmen **Hantering av uppdateringar**. Du kan se en lista med uppdateringar som saknas på fliken  **Uppdateringar som saknas**.

 ![Visa status för uppdateringen](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Schemalägga en uppdateringsdistribution

För att installera uppdateringar schemalägger du en distribution som passar ditt schema och servicefönster.
Du kan välja vilka uppdateringstyper som ska tas med i distributionen. Till exempel kan du inkludera kritiska eller säkerhetsuppdateringar och utelämna samlade uppdateringar.

Schemalägga en ny distribution av uppdatering för den virtuella datorn genom att klicka på **schema uppdateringsdistribution** överst i den **uppdateringshantering** skärmen. I den **ny uppdatera distribution** skärmen, anger du följande information:

* **Namn** – Ett unikt namn som identifierar uppdateringsdistributionen.
* **Uppdatera klassifikationen** -Välj typerna av programvara distributionen av uppdateringen ingår i distributionen. Klassificeringstyper:
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
Observera att den **schemalagda** tabellen visar distributionsschemat du skapat.

> [!WARNING]
> Den virtuella datorn startas automatiskt efter uppdateringar som kräver en omstart.

### <a name="view-results-of-an-update-deployment"></a>Visa resultat för en uppdateringsdistribution

När den schemalagda distributionen har påbörjats kan du se status för distributionen på fliken **Uppdateringsdistributioner** på skärmen **Hantering av uppdateringar**.
Om distributionen körs visas status **Pågår**. När distributionen har slutförts ändras status till **Lyckades**.
Om det uppstod något fel med en eller flera uppdateringar i distributionen visas status **Misslyckades delvis**.
Klicka på den slutförda uppdateringsdistributionen för att visa instrumentpanelen för distributionen.

   ![Uppdatera distributionen instrumentpanelen distribution](./media/tutorial-monitoring/manageupdates-view-results.png)

I **Uppdatera resultat** panelen visas en sammanfattning av det totala antalet uppdateringar och distributionsresultat på den virtuella datorn.
I tabellen till höger visas detaljer för varje uppdatering och installationsresultaten, som kan ha ett av följande värden:

* **Inget försök** -uppdateringen installerades inte eftersom det inte har tillräckligt med tid tillgänglig baserat på Underhåll fönstervaraktigheten som definierats.
* **Lyckades** -uppdateringen är klar
* **Det gick inte** -uppdateringen misslyckades

Klicka på **Alla loggar** om du vill se alla loggposter som har skapats för distributionen.

Klicka på den **utdata** ruta visas jobbström runbook ansvarar för att hantera distributionen av uppdateringen på målet VM.

Klicka på **Fel** om du vill se detaljerad information om fel som uppstått vid distributionen.

## <a name="advanced-monitoring"></a>Avancerad övervakning 

Du kan göra mer avancerad övervakning av den virtuella datorn med hjälp av [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview). Om du inte redan gjort det. Du kan registrera dig för en [kostnadsfri utvärderingsversion](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-trial) av Operations Management Suite.

När du har åtkomst till OMS-portalen hittar du den arbetsyta och arbetsytan identifierare på bladet inställningar. Använd den [Set AzureRmVMExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmextension) kommando för att lägga till OMS-tillägget på den virtuella datorn. Uppdatera variabelvärden i den nedan exempel för att återspegla du OMS arbetsytenyckel och arbetsytan Id.  

```powershell
$omsId = "<Replace with your OMS Id>"
$omsKey = "<Replace with your OMS key>"

Set-AzureRmVMExtension -ResourceGroupName myResourceGroup `
  -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
  -VMName myVM `
  -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
  -ExtensionType "MicrosoftMonitoringAgent" `
  -TypeHandlerVersion 1.0 `
  -Settings @{"workspaceId" = $omsId} `
  -ProtectedSettings @{"workspaceKey" = $omsKey} `
  -Location eastus
```

Du bör se den nya virtuella datorn i OMS-arbetsytan efter några minuter. 

![OMS-bladet](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>Nästa steg
I kursen får du konfigurerade och granskas virtuella datorer med Azure Security Center. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk
> * Skapa en resursgrupp och en virtuell dator 
> * Aktivera startdiagnostikinställningar på den virtuella datorn
> * Visa startdiagnostik
> * Visa värden mått
> * Installera tillägget diagnostik
> * Visa VM mått
> * Skapa en avisering
> * Hantera Windows-uppdateringar
> * Konfigurera avancerad övervakning

Gå vidare till nästa kurs vill veta mer om Azure security center.

> [!div class="nextstepaction"]
> [Hantera säkerheten för virtuella datorer](./tutorial-azure-security.md)