---
title: "Övervaka och uppdatera virtuella Linux-datorer i Azure | Microsoft Docs"
description: "Lär dig att övervaka Start diagnostik- och prestandamått och hantera uppdateringar av paketet på en virtuell Linux-dator i Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/08/2017
ms.author: davidmu
ms.custom: mvc
ms.openlocfilehash: 70c17d9a8f7bf6d9106efcb56eee7cd996460c18
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-monitor-and-update-a-linux-virtual-machine-in-azure"></a>Hur du övervakar och uppdatera en virtuell Linux-dator i Azure

För att säkerställa att dina virtuella datorer (VM) i Azure körs på rätt sätt, kan du granska startdiagnostikinställningar för prestandavärden och hantera uppdateringar av paketet. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Aktivera startdiagnostikinställningar på den virtuella datorn
> * Visa startdiagnostik
> * Visa värden mått
> * Aktivera diagnostik tillägget på den virtuella datorn
> * Visa VM mått
> * Skapa aviseringar baserat på diagnostiska mått
> * Hantera uppdateringar av paketet
> * Konfigurera avancerad övervakning


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kursen krävs att du använder Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-vm"></a>Skapa en virtuell dator

Om du vill visa diagnostik- och mått i praktiken, behöver du en virtuell dator. Börja med att skapa en resursgrupp med [az gruppen skapa](/cli/azure/group#create). I följande exempel skapas en resursgrupp med namnet *myResourceGroupMonitor* i den *eastus* plats.

```azurecli-interactive 
az group create --name myResourceGroupMonitor --location eastus
```

Nu skapa en virtuell dator med [az vm skapa](https://docs.microsoft.com/cli/azure/vm#az_vm_create). I följande exempel skapas en virtuell dator med namnet *myVM*:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="enable-boot-diagnostics"></a>Aktivera startdiagnostikinställningar

Eftersom virtuella Linux-datorer startar Start diagnostiska tillägget avbildas Start utdata och lagrar den i Azure-lagring. Dessa data kan användas för att felsöka problem med VM-start. Startdiagnostikinställningar aktiveras inte automatiskt när du skapar en Linux VM som använder Azure CLI.

Innan du aktiverar startdiagnostikinställningar behöver ett lagringskonto skapas för att lagra start loggar. Storage-konton måste ha ett globalt unikt namn vara mellan 3 och 24 tecken och får enbart innehålla siffror och gemener. Skapa ett lagringskonto med den [az storage-konto skapar](/cli/azure/storage/account#create) kommando. I det här exemplet används en slumpmässig sträng för att skapa en unik lagringskontonamnet. 

```azurecli-interactive 
storageacct=mydiagdata$RANDOM

az storage account create \
  --resource-group myResourceGroupMonitor \
  --name $storageacct \
  --sku Standard_LRS \
  --location eastus
```

När du aktiverar startdiagnostikinställningar krävs URI till blob storage-behållare. Följande kommando frågar storage-konto för att returnera URI: N. URI-värdet lagras i ett variabelnamn *bloburi*, som används i nästa steg.

```azurecli-interactive 
bloburi=$(az storage account show --resource-group myResourceGroupMonitor --name $storageacct --query 'primaryEndpoints.blob' -o tsv)
```

Nu aktivera startdiagnostikinställningar med [az vm-startdiagnostikinställningar aktivera](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_enable). Den `--storage` värdet är blob-URI som samlas in i föregående steg.

```azurecli-interactive 
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```


## <a name="view-boot-diagnostics"></a>Visa startdiagnostik

När startdiagnostikinställningar aktiveras skrivs varje gång du stoppa och starta den virtuella datorn, information om startprocessen till en loggfil. I det här exemplet först frigöra den virtuella datorn med den [az vm frigöra](/cli/azure/vm#deallocate) kommandot på följande sätt:

```azurecli-interactive 
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

Nu starta den virtuella datorn med den [az vm start]( /cli/azure/vm#stop) kommandot på följande sätt:

```azurecli-interactive 
az vm start --resource-group myResourceGroupMonitor --name myVM
```

Du kan hämta Start diagnostikdata för *myVM* med den [az vm-startdiagnostikinställningar get-Start-loggen](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_get_boot_log) kommandot på följande sätt:

```azurecli-interactive 
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```


## <a name="view-host-metrics"></a>Visa värden mått

En Linux-VM med en dedikerad värd i Azure som den interagerar med. Mått är automatiskt samlas in för värden och kan visas i Azure-portalen på följande sätt:

1. I Azure-portalen klickar du på **resursgrupper**väljer **myResourceGroupMonitor**, och välj sedan **myVM** i resurslistan.
1. Klicka för att se hur den Virtuella värddatorn utförs **mått** på VM-bladet välj sedan någon av de *[värd]* mått under **tillgängliga mått**.

    ![Visa värden mått](./media/tutorial-monitoring/monitor-host-metrics.png)


## <a name="install-diagnostics-extension"></a>Installera tillägg för diagnostik

> [!IMPORTANT]
> Det här dokumentet beskriver version 2.3 av Linux diagnostiska tillägget, som har tagits bort. Version 2.3 kommer att stödjas tills 30 juni 2018.
>
> Version 3.0 av Linux diagnostiska tillägget kan aktiveras i stället. Mer information finns i [dokumentationen](./diagnostic-extension.md).

De grundläggande värden är tillgänglig, men att se mer detaljerad och VM-specifika statistik, och du behöver installera Azure-diagnostik-tillägget på den virtuella datorn. Azure diagnostics-tillägget kan ytterligare övervakning och diagnostikdata som ska hämtas från den virtuella datorn. Du kan visa dessa prestandamått och skapa varningar baserat på hur den virtuella datorn utför. Diagnostiska tillägget installeras via Azure portal på följande sätt:

1. I Azure-portalen klickar du på **resursgrupper**väljer **myResourceGroup**, och välj sedan **myVM** i resurslistan.
1. Klicka på **diagnos inställningar**. I listan visas som *starta diagnostik* redan aktiverat från föregående avsnitt. Klicka på kryssrutan för *grundläggande mått*.
1. I den *lagringskonto* , bläddra till och välj den *mydiagdata [1234]* konto som har skapats i föregående avsnitt.
1. Klicka på knappen **Spara**.

    ![Visa diagnostik mått](./media/tutorial-monitoring/enable-diagnostics-extension.png)


## <a name="view-vm-metrics"></a>Visa VM mått

Du kan visa VM mått på samma sätt som du granskat värden VM mått:

1. I Azure-portalen klickar du på **resursgrupper**väljer **myResourceGroup**, och välj sedan **myVM** i resurslistan.
1. Klicka för att se hur den virtuella datorn utför **mått** på VM-bladet och välj sedan någon av diagnostik mått under **tillgängliga mått**.

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

## <a name="manage-package-updates"></a>Hantera uppdateringar av paketet

Genom att använda hantering av uppdateringar kan hantera du paketet uppdateringar och korrigeringsfiler för din Azure Linux virtuella datorer. Direkt från din virtuella dator du kan snabbt bedöma statusen för uppdateringar, schemalägga installationen av obligatoriska uppdateringar och granska resultatet av distributionen för att kontrollera uppdateringar har använts på den virtuella datorn.

Information om priser finns [Automation priser för uppdateringshantering](https://azure.microsoft.com/pricing/details/automation/)

### <a name="enable-update-management-preview"></a>Aktivera uppdateringshantering (förhandsgranskning)

Aktivera hantering av uppdatering för den virtuella datorn

1. På vänster sida av skärmen väljer **virtuella datorer**.
1. Välj en virtuell dator i listan.
1. På skärmen VM i den **Operations** klickar du på **uppdateringshantering**. Den **aktivera uppdateringshantering** skärmen öppnas.

Verifieringen utförs för att fastställa om uppdateringen management har aktiverats för den här virtuella datorn. Verifieringen inkluderar söker efter en logganalys-arbetsytan och länkade Automation-konto, och om lösningen i arbetsytan.

Logganalys-arbetsytan används för att samla in data som genereras av funktioner och tjänster, till exempel hantering av uppdateringar. Arbetsytan innehåller en enda plats för att granska och analysera data från flera källor. Om du vill utföra ytterligare åtgärder på virtuella datorer som kräver uppdateringar kan Azure Automation du köra skript mot virtuella datorer, såsom att hämta och installera uppdateringar.

Valideringen kontrollerar även om den virtuella datorn har etablerats med Microsoft Monitoring Agent (MMA) och worker-hybrid. Den här agenten används för att kommunicera med den virtuella datorn och få information om uppdateringsstatus. 

Om förutsättningarna inte uppfylls visas en banderoll som ger dig möjlighet att aktivera lösningen.

![Uppdatera Management publicera configuration banderoll](./media/tutorial-monitoring/manage-updates-onboard-solution-banner.png)

Klicka på banderollen för att aktivera lösningen. Om någon av följande förutsättningar hittades saknas efter valideringen läggs de automatiskt:

* [Logga Analytics](../../log-analytics/log-analytics-overview.md) arbetsytan
* [Automation](../../automation/automation-offering-get-started.md)
* En [runbook worker-Hybrid](../../automation/automation-hybrid-runbook-worker.md) är aktiverat på den virtuella datorn

Den **aktivera uppdateringshantering** skärmen öppnas. Konfigurera inställningarna och klicka på **aktivera**.

![Aktivera uppdateringshantering](./media/tutorial-monitoring/manage-updates-update-enable.png)

Aktivera lösningen kan ta upp till 15 minuter och under tiden ska du inte stänga webbläsarfönstret. När lösningen har aktiverats, flödar information om uppdateringar som saknas från package manager på den virtuella datorn till logganalys.
Det kan ta mellan 30 minuter och 6 timmar för att data ska vara tillgängliga för analys.

### <a name="view-update-assessment"></a>Visa kontroll av uppdateringar

Efter den **uppdateringshantering** lösningen aktiveras den **uppdateringshantering** visas. Du kan se en lista med uppdateringar som saknas på fliken  **Uppdateringar som saknas**.

![Visa status för uppdateringen](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>Schemalägga en uppdateringsdistribution

Schemalägga en distribution som följer viktig schema och underhåll fönstret för att installera uppdateringarna.

Schemalägga en ny distribution av uppdatering för den virtuella datorn genom att klicka på **schema uppdateringsdistribution** överst i den **uppdateringshantering** skärmen. I den **ny uppdatera distribution** skärmen, anger du följande information:

* **Namn** – Ett unikt namn som identifierar uppdateringsdistributionen.
* **Uppdateringar för att utesluta** -Välj detta för att ange namnen på de paket som ska undantas från uppdateringen.
* **Schemainställningar** – Du kan antingen godkänna standarddatumet och -tiden, d.v.s. 30 minuter efter aktuell tid, eller så kan du ange en annan tid. Du kan också ange om distributionen ska ske en gång eller ange ett schema med återkommande tider. Klicka på alternativet Återkommande under Återkommande för att ange ett återkommande schema.

  ![Inställningsskärmen för uppdateringsschema](./media/tutorial-monitoring/manage-updates-schedule-linux.png)

* **Underhållsperiod (minuter)** – Ange den tidsperiod som uppdateringsdistributionen ska utföras inom.  På så sätt försäkrar du dig ändringar utförs inom de definierade underhållsfönstren. 

När du har konfigurerat schemat klickar du på **Skapa**. Därmed återgår du till statusinstrumentpanelen.
Observera att den **schemalagda** tabellen visar distributionsschemat du skapat.

> [!WARNING]
> Den virtuella datorn kommer att startas om automatiskt efter uppdateringar har installerats om det finns tillräckligt med tid i underhållsfönstret.

Uppdateringshantering använder befintliga package manager på den virtuella datorn för att installera paket.

### <a name="view-results-of-an-update-deployment"></a>Visa resultat för en uppdateringsdistribution

När den schemalagda distributionen har påbörjats kan du se status för distributionen på fliken **Uppdateringsdistributioner** på skärmen **Hantering av uppdateringar**.
Om distributionen körs visas status **Pågår**. När distributionen har slutförts ändras status till **Lyckades**.
Om det har uppstått ett fel med en eller flera uppdateringar i distributionen är status **misslyckades**.
Klicka på den slutförda uppdateringsdistributionen för att visa instrumentpanelen för distributionen.

![Uppdatera distributionen instrumentpanelen distribution](./media/tutorial-monitoring/manage-updates-view-results.png)

I **Uppdatera resultat** panelen visas en sammanfattning av det totala antalet uppdateringar och distributionsresultat på den virtuella datorn.
I tabellen till höger visas detaljer för varje uppdatering och installationsresultaten, som kan ha ett av följande värden:

* **Inget försök** -uppdateringen installerades inte eftersom det inte har tillräckligt med tid tillgänglig baserat på Underhåll fönstervaraktigheten som definierats.
* **Lyckades** -uppdateringen har hämtat och installerat på den virtuella datorn
* **Det gick inte** -uppdateringen kunde inte hämtas eller installeras på den virtuella datorn.

Klicka på **Alla loggar** om du vill se alla loggposter som har skapats för distributionen.

Klicka på den **utdata** ruta visas jobbström runbook ansvarar för att hantera distributionen av uppdateringen på målet VM.

Klicka på **Fel** om du vill se detaljerad information om fel som uppstått vid distributionen.

## <a name="advanced-monitoring"></a>Avancerad övervakning 

Du kan göra mer avancerad övervakning av den virtuella datorn med hjälp av [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview). Om du inte redan gjort det. Du kan registrera dig för en [kostnadsfri utvärderingsversion](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-trial) av Operations Management Suite.

När du har åtkomst till OMS-portalen hittar du den arbetsyta och arbetsytan identifierare på bladet inställningar. Ersätt < arbetsytenyckel > och < arbetsytan-id > med värden från din OMS för arbetsytan och du sedan kan använda **az vm-tillägget set** lägga till OMS-tillägget till den virtuella datorn:

```azurecli-interactive 
az vm extension set \
  --resource-group myResourceGroupMonitor \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.3 \
  --protected-settings '{"workspaceKey": "<workspace-key>"}' \
  --settings '{"workspaceId": "<workspace-id>"}'
```

I bladet loggen Sök i OMS-portalen kan du se *myVM* , till exempel vad som ska visas i följande bild:

![OMS-bladet](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen konfigurerad, granskas och hanterad uppdateringar för en virtuell dator. Du har lärt dig att:

> [!div class="checklist"]
> * Aktivera startdiagnostikinställningar på den virtuella datorn
> * Visa startdiagnostik
> * Visa värden mått
> * Aktivera diagnostik tillägget på den virtuella datorn
> * Visa VM mått
> * Skapa aviseringar baserat på diagnostiska mått
> * Hantera uppdateringar av paketet
> * Konfigurera avancerad övervakning

Gå vidare till nästa kurs vill veta mer om Azure Security Center.

> [!div class="nextstepaction"]
> [Hantera säkerheten för virtuella datorer](./tutorial-azure-security.md)
