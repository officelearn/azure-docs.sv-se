---
title: Självstudiekurs - Övervaka virtuella Linux-datorer i Azure
description: I den här självstudien får du lära dig hur du övervakar prestanda och upptäckte programkomponenter som körs på dina virtuella Linux-datorer.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: mgoedtel
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/30/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: b06342d5034b820be4e6fd49436546a5aa7b7e02
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75749794"
---
# <a name="tutorial-monitor-a-linux-virtual-machine-in-azure"></a>Självstudiekurs: Övervaka en virtuell Linux-dator i Azure

Azure-övervakning använder agenter för att samla in start- och prestandadata från virtuella Azure-datorer, lagra dessa data i Azure-lagring och göra dem tillgängliga via portalen, Azure PowerShell-modulen och Azure CLI. Avancerad övervakning levereras med Azure Monitor för virtuella datorer genom att samla in prestandamått, identifiera programkomponenter installerade på den virtuella datorn och inkluderar prestandadiagram och beroendemappning.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Aktivera startdiagnostik på en virtuell dator
> * Visa startdiagnostik
> * Visa statistik för en virtuell värddator
> * Aktivera Azure Monitor för virtuella datorer
> * Visa bakgrundsmått för vm-prestanda
> * Skapa en avisering

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell i [https://shell.azure.com/powershell](https://shell.azure.com/powershell)en separat webbläsarflik genom att gå till . Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

Om du väljer att installera och använda CLI lokalt krävs Azure CLI version 2.0.30 eller senare för att du ska kunna genomföra den här självstudiekursen. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-vm"></a>Skapa en virtuell dator

Du behöver en virtuell dator för att kunna se diagnostik och mått i praktiken. Skapa först en resursgrupp med [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create). I följande exempel skapas en resursgrupp med namnet *myResourceGroupMonitor* på platsen *eastus* (Östra USA).

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

Skapa nu en virtuell dator med [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). Följande exempel skapar en virtuell dator som heter *myVM*, och SSH-nycklar skapas om de inte redan finns på *~/.ssh/*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="enable-boot-diagnostics"></a>Aktivera startdiagnostik

När de virtuella Linux-datorerna startas samlar startdiagnostiktillägget in startutdata och lagrar dem i Azure Storage. Dessa data kan användas för att felsöka startproblem med de virtuella datorerna. Startdiagnostik aktiveras inte automatiskt när du skapar en virtuell Linux-dator med Azure CLI.

Innan du aktiverar startdiagnostik måste du skapa ett lagringskonto för att lagra startloggarna. Lagringskonton måste ha ett globalt unikt namn. Namnet ska vara mellan 3 och 24 tecken och får bara innehålla siffror och gemener. Skapa ett lagringskonto med kommandot [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create). I det här exemplet används en slumpmässig sträng för att skapa ett unikt namn på lagringskontot.

```azurecli-interactive
storageacct=mydiagdata$RANDOM

az storage account create \
  --resource-group myResourceGroupMonitor \
  --name $storageacct \
  --sku Standard_LRS \
  --location eastus
```

När du aktiverar startdiagnostik behöver du URI:en till Blob Storage-containern. Följande kommando skickar en fråga till lagringskontot för att returnera denna URI. URI-värdet lagras i variabeln *bloburi* som används i nästa steg.

```azurecli-interactive
bloburi=$(az storage account show --resource-group myResourceGroupMonitor --name $storageacct --query 'primaryEndpoints.blob' -o tsv)
```

Nu kan du aktivera startdiagnostik med [az vm boot-diagnostics enable](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-enable). `--storage`-värdet är den blob-URI som samlades in i föregående steg.

```azurecli-interactive
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```

## <a name="view-boot-diagnostics"></a>Visa startdiagnostik

När startdiagnostik är aktiverat skrivs information om startprocessen till en loggfil varje gång du stoppar och startar den virtuella datorn. I det här exemplet frigör du först den virtuella datorn med kommandot [az vm deallocate](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-deallocate):

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

Sedan startar du den virtuella datorn med kommandot [az vm start](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-start):

```azurecli-interactive
az vm start --resource-group myResourceGroupMonitor --name myVM
```

Du kan hämta startdiagnostikdata för *myVM* med kommandot [az vm boot-diagnostics get-boot-log](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-get-boot-log):

```azurecli-interactive
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```

## <a name="view-host-metrics"></a>Visa värdmått

En virtuell Linux-dator har en dedikerad värd i Azure som den interagerar med. Mått samlas in automatiskt för värden och kan visas i Azure Portal:

1. I Azure Portal, klicka på **Resource Groups**, välj **myResourceGroupMonitor** och välj sedan **myVM** i resurslistan.
1. Om du vill se status för den virtuella värddatorn väljer du **Mått** på fönstret för den virtuella datorn och väljer något av *[Värd]*-måtten under **Tillgängliga mått**.

    ![Visa värdmått](./media/tutorial-monitoring/monitor-host-metrics.png)

## <a name="enable-advanced-monitoring"></a>Aktivera avancerad övervakning

Så här aktiverar du övervakning av din virtuella Azure-dator med Azure Monitor för virtuella datorer:

1. I Azure Portal: Klicka på **Resource Groups**, välj **myResourceGroupMonitor** och välj sedan **myVM** i resurslistan.

2. Välj **Insikter (förhandsgranskning)** i avsnittet **Övervakning** på sidan Virtuell dator .

3. På sidan **Insikter (förhandsgranskning)** väljer du **Prova nu**.

    ![Aktivera Azure Monitor för virtuella datorer för en virtuell dator](../../azure-monitor/insights/media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

4. Om du har en befintlig Log Analytics-arbetsyta i samma prenumeration på sidan **Azure Monitor Insights Onboarding** väljer du den i listrutan.  

    Listan förväljs till standardarbetsyta och plats där den virtuella datorn distribueras i prenumerationen. 

    >[!NOTE]
    >Information om hur du skapar en ny Log Analytics-arbetsyta för att lagra övervakningsdata från den virtuella datorn finns i [Skapa en Log Analytics-arbetsyta](../../azure-monitor/learn/quick-create-workspace.md). Log Analytics-arbetsytan måste tillhöra en av de [regioner som stöds.](../../azure-monitor/insights/vminsights-enable-overview.md#log-analytics)

När du har aktiverat övervakning kan du behöva vänta flera minuter innan du kan visa prestandamåtten för den virtuella datorn.

![Aktivera Azure Monitor för virtuella datorer som övervakar distributionsbearbetning](../../azure-monitor/insights/media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="view-vm-performance-metrics"></a>Visa bakgrundsmått för vm-prestanda

Azure Monitor för virtuella datorer innehåller en uppsättning prestandadiagram som riktar sig till flera kpi:er (Key Performance Indicators) som hjälper dig att avgöra hur bra en virtuell dator presterar. Gör följande för att komma åt från den virtuella datorn.

1. I Azure Portal: Klicka på **Resource Groups**, välj **myResourceGroupMonitor** och välj sedan **myVM** i resurslistan.

2. Välj **Insikter (förhandsgranskning)** i avsnittet **Övervakning** på sidan Virtuell dator .

3. Välj fliken **Prestanda.**

Den här sidan innehåller inte bara prestandautnyttjandediagram, utan även en tabell som visar för varje logisk disk som upptäckts, dess kapacitet, användning och totalt genomsnitt för varje mått.

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
> * Aktivera Azure Monitor för virtuella datorer
> * Visa VM-mått
> * Skapa en avisering

Gå vidare till nästa kurs om du vill veta mer om Azure Security Center.

> [!div class="nextstepaction"]
> [Hantera säkerheten för virtuella datorer](../../security/fundamentals/overview.md)
