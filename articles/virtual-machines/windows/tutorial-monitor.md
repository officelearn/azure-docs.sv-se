---
title: Självstudie – övervaka virtuella Windows-datorer i Azure
description: I den här självstudien får du lära dig hur du övervakar prestanda och identifierade program komponenter som körs på dina virtuella Windows-datorer.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/27/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 13e5cc9ee45cf230668ef7a7cbe85b6437044643
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74064759"
---
# <a name="tutorial-monitor-a-windows-virtual-machine-in-azure"></a>Självstudie: övervaka en virtuell Windows-dator i Azure

Azure Monitoring använder agenter för att samla in start-och prestanda data från virtuella Azure-datorer, lagra data i Azure Storage och göra dem tillgängliga via portalen, Azure PowerShell-modulen och Azure CLI. Avancerad övervakning levereras med Azure Monitor for VMs genom att samla in prestanda mått, identifiera program komponenter som är installerade på den virtuella datorn och innehåller prestanda diagram och beroende karta.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Aktivera startdiagnostik på en virtuell dator
> * Visa startdiagnostik
> * Visa statistik för en virtuell värddator
> * Aktivera Azure Monitor for VMs
> * Visa prestanda mått för virtuella datorer
> * Skapa en avisering

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

När virtuella Windows-datorer startar samlar startdiagnostikagenten in skärmutdata som kan användas i felsökningssyfte. Den här funktionen är aktiverad som standard. De fångade skärmarna lagras i ett Azure Storage-konto, som också skapas som standard.

Du kan hämta startdiagnostikdata med kommandot [Get-AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/az.compute/get-azvmbootdiagnosticsdata). I följande exempel laddas startdiagnostik ned till roten på enhet *c:\*.

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName "myResourceGroupMonitor" -Name "myVM" -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>Visa värdmått

En virtuell Windows-dator har en dedikerad virtuell värddator i Azure som den interagerar med. Mått samlas in automatiskt för värden och kan visas på Azure Portal.

1. I Azure Portal: Klicka på **Resource Groups**, välj **myResourceGroupMonitor** och välj sedan **myVM** i resurslistan.
2. Om du vill visa den virtuella värddatorns status klickar du på **Mått** på bladet Virtuell dator och väljer något av värdmåtten under **Tillgängliga mått**.

    ![Visa värdmått](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="enable-advanced-monitoring"></a>Aktivera Avancerad övervakning

Så här aktiverar du övervakning av den virtuella Azure-datorn med Azure Monitor for VMs:

1. I Azure Portal: Klicka på **Resource Groups**, välj **myResourceGroupMonitor** och välj sedan **myVM** i resurslistan.

2. På sidan virtuell dator i den **övervakning** väljer **Insights (förhandsversion)** .

3. På den **Insights (förhandsversion)** väljer **Prova nu**.

    ![Aktivera Azure Monitor för virtuella datorer för en virtuell dator](../../azure-monitor/insights/media/vminsights-enable-single-vm/enable-vminsights-vm-portal-01.png)

4. På den **Azure Monitor insikter Onboarding** om du har en befintlig Log Analytics-arbetsyta i samma prenumeration, markerar du den i den nedrullningsbara listan.  

    I listan förväljs standard arbets ytan och den plats där den virtuella datorn distribueras i prenumerationen. 

    >[!NOTE]
    >Om du vill skapa en ny Log Analytics-arbetsyta för att lagra övervaknings data från den virtuella datorn, se [skapa en Log Analytics arbets yta](../../azure-monitor/learn/quick-create-workspace.md). Din Log Analytics-arbetsyta måste tillhöra en av de [regioner som stöds](../../azure-monitor/insights/vminsights-enable-overview.md#log-analytics).

När du har aktiverat övervakning kan du behöva vänta flera minuter innan du kan visa prestanda måtten för den virtuella datorn.

![Aktivera Azure Monitor för virtuella datorer övervakning distributionsbearbetning](../../azure-monitor/insights/media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="view-vm-performance-metrics"></a>Visa prestanda mått för virtuella datorer

Azure Monitor for VMs innehåller en uppsättning prestanda diagram som riktar sig mot flera nyckeltal (KPI: er) för att hjälpa dig att avgöra hur väl en virtuell dator fungerar. Utför följande steg för att komma åt från den virtuella datorn.

1. I Azure Portal: Klicka på **Resource Groups**, välj **myResourceGroupMonitor** och välj sedan **myVM** i resurslistan.

2. På sidan virtuell dator i den **övervakning** väljer **Insights (förhandsversion)** .

3. Välj fliken **prestanda** .

På den här sidan ingår inte bara prestanda användnings diagram, utan även en tabell som visar för varje logisk disk som identifieras, dess kapacitet, användning och total genomsnitt per mått.

## <a name="create-alerts"></a>Skapa aviseringar

Du kan skapa aviseringar baserat på specifika prestandamått. Aviseringar kan till exempel användas för att informera dig om när den genomsnittliga CPU-användning överskrider ett visst tröskelvärde eller när det lediga diskutrymmet understiger ett visst värde. Aviseringar kan visas i Azure Portal eller skickas via e-post. Du kan också utlösa Azure Automation-runbooks eller Azure Logic Apps när aviseringar skapas.

I följande exempel skapas en avisering för genomsnittlig CPU-användning.

1. I Azure Portal: Klicka på **Resource Groups**, välj **myResourceGroupMonitor** och välj sedan **myVM** i resurslistan.

2. Klicka på **Aviseringsregler** på bladet för den virtuella datorn och klicka sedan på **Lägg till metrisk varning** längst upp på aviseringsbladet.

3. Ange ett **namn** för aviseringen, till exempel *myAlertRule*

4. Om du vill utlösa en avisering när CPU-procenten har varit högre än 1,0 i fem minuter lämnar du alla andra standardinställningar valda.

5. Du kan också markera kryssrutan för *E-postägare, deltagare och läsare* om du vill skicka ett e-postmeddelande. Standardåtgärden är att visa en avisering i portalen.

6. Klicka på **OK**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du konfigurerat och visat prestanda för den virtuella datorn. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en resursgrupp och virtuell dator
> * Aktivera startdiagnostik på den virtuella datorn
> * Visa startdiagnostik
> * Visa värdmått
> * Aktivera Azure Monitor for VMs
> * Visa VM-mått
> * Skapa en avisering

Gå vidare till nästa kurs om du vill veta mer om Azure Security Center.

> [!div class="nextstepaction"]
> [Hantera säkerheten för virtuella datorer](../../security/fundamentals/overview.md)
