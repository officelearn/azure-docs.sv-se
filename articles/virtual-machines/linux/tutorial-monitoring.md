---
title: "Övervaka och uppdatera virtuella Linux-datorer i Azure | Microsoft Docs"
description: "Lär dig hur du övervakar startdiagnostik och prestandastatistik och hanterar paketuppdateringar på en virtuell Linux-dator i Azure"
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
ms.openlocfilehash: 230ce6a6b33e63bcced5f520b57b63ef4ed05448
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="how-to-monitor-and-update-a-linux-virtual-machine-in-azure"></a>Övervaka och uppdatera en virtuell Linux-dator i Azure

För att säkerställa att dina virtuella datorer körs på rätt sätt i Azure kan du granska startdiagnostik, prestandastatistik och hantera paketuppdateringar. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Aktivera startdiagnostik på den virtuella datorn
> * Visa startdiagnostik
> * Visa värdmått
> * Aktivera diagnostiktillägget på den virtuella datorn
> * Visa VM-mått
> * Skapa aviseringar baserat på diagnostikmått
> * Hantera paketuppdateringar
> * Konfigurera avancerad övervakning


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver de här självstudierna att du kör Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-vm"></a>Skapa en virtuell dator

Du behöver en virtuell dator för att kunna se diagnostik och mått i praktiken. Skapa först en resursgrupp med [az group create](/cli/azure/group#az_group_create). I följande exempel skapas en resursgrupp med namnet *myResourceGroupMonitor* på platsen *eastus* (Östra USA).

```azurecli-interactive 
az group create --name myResourceGroupMonitor --location eastus
```

Skapa nu en virtuell dator med [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create). I följande exempel skapas en virtuell dator med namnet *myVM*:

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

Innan du aktiverar startdiagnostik måste du skapa ett lagringskonto för att lagra startloggarna. Lagringskonton måste ha ett globalt unikt namn. Namnet ska vara mellan 3 och 24 tecken och får bara innehålla siffror och gemener. Skapa ett lagringskonto med kommandot [az storage account create](/cli/azure/storage/account#az_storage_account_create). I det här exemplet används en slumpmässig sträng för att skapa ett unikt namn på lagringskontot. 

```azurecli-interactive 
storageacct=mydiagdata$RANDOM

az storage account create \
  --resource-group myResourceGroupMonitor \
  --name $storageacct \
  --sku Standard_LRS \
  --location eastus
```

När du aktiverar startdiagnostik behöver du URI:en till Blob Storage-behållaren. Följande kommando skickar en fråga till lagringskontot för att returnera denna URI. URI-värdet lagras i variabeln *bloburi* som används i nästa steg.

```azurecli-interactive 
bloburi=$(az storage account show --resource-group myResourceGroupMonitor --name $storageacct --query 'primaryEndpoints.blob' -o tsv)
```

Nu kan du aktivera startdiagnostik med [az vm boot-diagnostics enable](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_enable). `--storage`-värdet är den blob-URI som samlades in i föregående steg.

```azurecli-interactive 
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```


## <a name="view-boot-diagnostics"></a>Visa startdiagnostik

När startdiagnostik är aktiverat skrivs information om startprocessen till en loggfil varje gång du stoppar och startar den virtuella datorn. I det här exemplet frigör du först den virtuella datorn med kommandot [az vm deallocate](/cli/azure/vm#az_vm_deallocate):

```azurecli-interactive 
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

Sedan startar du den virtuella datorn med kommandot [az vm start]( /cli/azure/vm#az_vm_stop):

```azurecli-interactive 
az vm start --resource-group myResourceGroupMonitor --name myVM
```

Du kan hämta startdiagnostikdata för *myVM* med kommandot [az vm boot-diagnostics get-boot-log](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_get_boot_log):

```azurecli-interactive 
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```


## <a name="view-host-metrics"></a>Visa värdmått

En virtuell Linux-dator har en dedikerad värd i Azure som den interagerar med. Mått samlas in automatiskt för värden och kan visas i Azure Portal:

1. I Azure Portal: Klicka på **Resource Groups**, välj **myResourceGroupMonitor** och välj sedan **myVM** i resurslistan.
1. Om du vill se status för den virtuella värddatorn klickar du på **Mått** på bladet för den virtuella datorn och väljer något av *[Värd]*-måtten under **Tillgängliga mått**.

    ![Visa värdmått](./media/tutorial-monitoring/monitor-host-metrics.png)


## <a name="install-diagnostics-extension"></a>Installera diagnostiktillägget

> [!IMPORTANT]
> I det här dokumentet beskrivs version 2.3 av Linux-diagnostiktillägget, som är inaktuellt. Version 2.3 stöds till och med 30 juni 2018.
>
> Du kan aktivera version 3.0 av Linux-diagnostiktillägget i stället. Mer information finns i [dokumentationen](./diagnostic-extension.md).

Grundläggande värdmått är tillgängliga, men om du vill se mer detaljerade och VM-specifika mått måste du installera Azure-diagnostiktillägget på den virtuella datorn. Med Azure-diagnostiktillägget kan du få ut mer övervaknings- och diagnostikdata från den virtuella datorn. Du kan visa dessa prestandamått och skapa aviseringar baserat på hur det går för den virtuella datorn. Diagnostiktillägget installeras via Azure Portal på följande sätt:

1. I Azure Portal: Klicka på **Resursgrupper**, välj **myResourceGroup** och välj sedan **myVM** i resurslistan.
1. Klicka på **Diagnosinställningar**. I listan kan du se att *Startdiagnostik* redan har aktiverats i föregående avsnitt. Markera kryssrutan för *Basmått*.
1. I avsnittet *Lagringskonto* bläddrar du till och väljer *mydiagdata[1234]*-kontot som skapades i föregående avsnitt.
1. Klicka på knappen **Spara**.

    ![Visa diagnostikmått](./media/tutorial-monitoring/enable-diagnostics-extension.png)


## <a name="view-vm-metrics"></a>Visa VM-mått

Du kan visa VM-mått på samma sätt som du visade VM-värdmått:

1. I Azure Portal: Klicka på **Resursgrupper**, välj **myResourceGroup** och välj sedan **myVM** i resurslistan.
1. Om du vill se hur det går för den virtuella datorn klickar du på **Mått** på bladet för den virtuella datorn och sedan på något av diagnostikmåtten under **Tillgängliga mått**.

    ![Visa VM-mått](./media/tutorial-monitoring/monitor-vm-metrics.png)


## <a name="create-alerts"></a>Skapa aviseringar

Du kan skapa aviseringar baserat på specifika prestandamått. Aviseringar kan till exempel användas för att informera dig om när den genomsnittliga CPU-användning överskrider ett visst tröskelvärde eller när det lediga diskutrymmet understiger ett visst värde. Aviseringar kan visas i Azure Portal eller skickas via e-post. Du kan också utlösa Azure Automation-runbooks eller Azure Logic Apps när aviseringar skapas.

I följande exempel skapas en avisering för genomsnittlig CPU-användning.

1. I Azure Portal: Klicka på **Resursgrupper**, välj **myResourceGroup** och välj sedan **myVM** i resurslistan.
2. Klicka på **Aviseringsregler** på bladet för den virtuella datorn och klicka sedan på **Lägg till metrisk varning** längst upp på aviseringsbladet.
4. Ange ett **namn** för aviseringen, till exempel *myAlertRule*
5. Om du vill utlösa en avisering när CPU-procenten har varit högre än 1,0 i fem minuter lämnar du alla andra standardinställningar valda.
6. Du kan också markera kryssrutan för *E-postägare, deltagare och läsare* om du vill skicka ett e-postmeddelande. Standardåtgärden är att visa en avisering i portalen.
7. Klicka på **OK**.

## <a name="manage-package-updates"></a>Hantera paketuppdateringar

Med uppdateringshantering kan du hantera paketuppdateringar och korrigeringar för dina virtuella Linux-datorer i Azure. Du kan snabbt se status för tillgängliga uppdateringar, schemalägga installation av nödvändiga uppdateringar och granska distributionsresultat för att verifiera att uppdateringarna har tillämpats på den virtuella datorn, direkt från den virtuella datorn.

Prisinformation finns i [Automation-priser för uppdateringshantering](https://azure.microsoft.com/pricing/details/automation/)

### <a name="enable-update-management-preview"></a>Aktivera uppdateringshantering (förhandsversion)

Aktivera uppdateringshantering för din virtuella dator

1. Välj **Virtuella datorer** till vänster på skärmen.
1. Välj en virtuell dator i listan.
1. Klicka på **Hantering av uppdateringar** i avsnittet **Åtgärder** på VM-skärmen. Skärmen **Aktivera hantering av uppdateringar** visas.

Verifieringen utförs för att fastställa om uppdateringshantering är aktiverat för den här virtuella datorn. Verifieringen söker efter en Log Analytics-arbetsyta och ett länkat Automation-konto, och om lösningen är i arbetsytan.

En Log Analytics-arbetsyta används för att samla in data som genereras av funktioner och tjänster som uppdateringshantering. Arbetsytan tillhandahåller en enda plats för att granska och analysera data från flera källor. Om du vill utföra ytterligare åtgärder på virtuella datorer som kräver uppdateringar kan Azure Automation köra skript mot virtuella datorer, till exempel för att ladda ned och installera uppdateringar.

Verifieringsprocessen kontrollerar också om den virtuella datorn har etablerats med MMA och Hybrid Worker. Den här agenten används för att kommunicera med den virtuella datorn och hämta information om uppdateringsstatus. 

Om förutsättningarna inte uppfylls visas en banderoll som ger dig möjlighet att aktivera lösningen.

![Publicering av konfigurationsbanderoll för Uppdateringshantering](./media/tutorial-monitoring/manage-updates-onboard-solution-banner.png)

Klicka på banderollen för att aktivera lösningen. Om några av följande krav saknades efter verifieringen läggs de till automatiskt:

* [Log Analytics](../../log-analytics/log-analytics-overview.md)-arbetsyta
* [Automation](../../automation/automation-offering-get-started.md)
* En [Hybrid runbook worker](../../automation/automation-hybrid-runbook-worker.md) aktiveras på den virtuella datorn

Skärmen **Aktivera hantering av uppdateringar** visas. Konfigurera inställningarna och klicka på **Aktivera**.

![Aktivera lösningen för hantering av uppdateringar](./media/tutorial-monitoring/manage-updates-update-enable.png)

Det kan ta upp till 15 minuter att aktivera lösningen och under den tiden bör du inte stänga webbläsarfönstret. När lösningen har aktiverats flödar information om saknade uppdateringar från pakethanteraren på den virtuella datorn till Log Analytics.
Det kan ta mellan 30 minuter och 6 timmar innan data blir tillgängliga för analys.

### <a name="view-update-assessment"></a>Visa kontroll av uppdateringar

När **lösningen för hantering av uppdateringar** är aktiverad visas skärmen **Hantering av uppdateringar**. Du kan se en lista med uppdateringar som saknas på fliken  **Uppdateringar som saknas**.

![Visa uppdateringsstatus](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>Schemalägga en uppdateringsdistribution

För att installera uppdateringar schemalägger du en distribution som passar ditt schema och underhållsfönster.

Schemalägg en ny uppdateringsdistribution för den virtuella datorn genom att klicka på **Distribution av schemauppdatering** längst upp på skärmen **Hantering av uppdateringar**. På skärmen **Ny uppdateringsdistribution** anger du följande information:

* **Namn** – Ett unikt namn som identifierar uppdateringsdistributionen.
* **Uppdateringar som ska uteslutas** – Välj detta för att ange namnen på de paket som ska undantas från uppdateringen.
* **Schemainställningar** – Du kan antingen godkänna standarddatumet och -tiden, d.v.s. 30 minuter efter aktuell tid, eller så kan du ange en annan tid. Du kan också ange om distributionen ska ske en gång eller ange ett schema med återkommande tider. Klicka på alternativet Återkommande under Återkommande för att ange ett återkommande schema.

  ![Inställningsskärmen för uppdateringsschema](./media/tutorial-monitoring/manage-updates-schedule-linux.png)

* **Underhållsperiod (minuter)** – Ange den tidsperiod som uppdateringsdistributionen ska utföras inom.  På så sätt försäkrar du dig om att ändringarna utförs inom ditt definierade underhållsfönster. 

När du har konfigurerat schemat klickar du på **Skapa**. Därmed återgår du till statusinstrumentpanelen.
Observera att tabellen **Schemalagt** visar det distributionsschema som du skapade.

> [!WARNING]
> Den virtuella datorn startas om automatiskt efter att uppdateringarna har installerats, om det finns tillräckligt med tid i underhållsfönstret.

I uppdateringshanteringen används den befintliga pakethanteraren på din virtuella dator för att installera paket.

### <a name="view-results-of-an-update-deployment"></a>Visa resultat för en uppdateringsdistribution

När den schemalagda distributionen har påbörjats kan du se status för distributionen på fliken **Uppdateringsdistributioner** på skärmen **Hantering av uppdateringar**.
Om distributionen körs visas status **Pågår**. När distributionen har slutförts ändras status till **Lyckades**.
Om det uppstod något fel med en eller flera uppdateringar i distributionen visas status **Misslyckades**.
Klicka på den slutförda uppdateringsdistributionen för att visa instrumentpanelen för distributionen.

![Statusinstrumentpanel för Uppdatera distribution för specifik distribution](./media/tutorial-monitoring/manage-updates-view-results.png)

På panelen för **uppdateringsresultat** visas en sammanfattning av det totala antalet uppdateringar och distributionsresultat för den virtuella datorn.
I tabellen till höger visas detaljer för varje uppdatering och installationsresultaten, som kan ha ett av följande värden:

* **Inget försök har gjorts** – Uppdateringen installerades inte eftersom det inte fanns tillräckligt med tid utifrån det underhållsfönster som definierats.
* **Lyckades** – Uppdateringen har hämtats och installerats på den virtuella datorn
* **Misslyckades** – Det gick inte att hämta eller installera uppdateringen på den virtuella datorn.

Klicka på **Alla loggar** om du vill se alla loggposter som har skapats för distributionen.

Klicka på panelen **Utdata** om du vill se jobbströmmen för den runbook som ansvarar för att hantera uppdateringsdistributionen på den virtuella måldatorn.

Klicka på **Fel** om du vill se detaljerad information om fel som uppstått vid distributionen.

## <a name="advanced-monitoring"></a>Avancerad övervakning 

Du kan göra mer en avancerad övervakning av den virtuella datorn med hjälp av [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview). Registrera dig för en [kostnadsfri utvärderingsversion](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-trial) av Operations Management Suite, om du inte redan gjort det.

När du har åtkomst till OMS-portalen hittar du arbetsytenyckeln och arbetsyteidentifieraren på bladet Inställningar. Ersätt <workspace-key> och <workspace-id> med värdena från din OMS-arbetsyta. Sedan kan du använda **az vm extension set** för att lägga till OMS-tillägget på den virtuella datorn:

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

På bladet Loggsökning på OMS-portalen visas nu *myVM* som på följande bild:

![OMS-bladet](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen har du konfigurerat, granskat och hanterat uppdateringar för en virtuell dator. Du har lärt dig att:

> [!div class="checklist"]
> * Aktivera startdiagnostik på den virtuella datorn
> * Visa startdiagnostik
> * Visa värdmått
> * Aktivera diagnostiktillägget på den virtuella datorn
> * Visa VM-mått
> * Skapa aviseringar baserat på diagnostikmått
> * Hantera paketuppdateringar
> * Konfigurera avancerad övervakning

Gå vidare till nästa kurs om du vill veta mer om Azure Security Center.

> [!div class="nextstepaction"]
> [Hantera säkerheten för virtuella datorer](./tutorial-azure-security.md)
