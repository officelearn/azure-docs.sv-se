---
title: Självstudier – Övervaka och uppdatera virtuella Linux-datorer i Azure | Microsoft Docs
description: I den här självstudiekursen lär du dig hur du övervakar startdiagnostik och prestandamått och hur du hanterar paketuppdateringar på en virtuell Linux-dator
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/26/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: fba54fa1d2ca6675b41728b460a07515b05758f8
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57531597"
---
# <a name="tutorial-monitor-and-update-a-linux-virtual-machine-in-azure"></a>Självstudier: Övervaka och uppdatera en virtuell Linux-dator i Azure

För att säkerställa att dina virtuella datorer körs på rätt sätt i Azure kan du granska startdiagnostik, prestandastatistik och hantera paketuppdateringar. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Aktivera startdiagnostik på den virtuella datorn
> * Visa startdiagnostik
> * Visa värdmått
> * Aktivera diagnostiktillägget på den virtuella datorn
> * Visa VM-mått
> * Skapa aviseringar baserat på diagnostikmått
> * Hantera paketuppdateringar
> * Övervaka ändringar och inventering
> * Konfigurera avancerad övervakning

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt krävs Azure CLI version 2.0.30 eller senare för att du ska kunna genomföra den här självstudiekursen. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-vm"></a>Skapa en virtuell dator

Du behöver en virtuell dator för att kunna se diagnostik och mått i praktiken. Skapa först en resursgrupp med [az group create](/cli/azure/group#az-group-create). I följande exempel skapas en resursgrupp med namnet *myResourceGroupMonitor* på platsen *eastus* (Östra USA).

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

Skapa nu en virtuell dator med [az vm create](/cli/azure/vm#az-vm-create). Följande exempel skapar en virtuell dator som heter *myVM*, och SSH-nycklar skapas om de inte redan finns på *~/.ssh/*:

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

Innan du aktiverar startdiagnostik måste du skapa ett lagringskonto för att lagra startloggarna. Lagringskonton måste ha ett globalt unikt namn. Namnet ska vara mellan 3 och 24 tecken och får bara innehålla siffror och gemener. Skapa ett lagringskonto med kommandot [az storage account create](/cli/azure/storage/account#az-storage-account-create). I det här exemplet används en slumpmässig sträng för att skapa ett unikt namn på lagringskontot.

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

När startdiagnostik är aktiverat skrivs information om startprocessen till en loggfil varje gång du stoppar och startar den virtuella datorn. I det här exemplet frigör du först den virtuella datorn med kommandot [az vm deallocate](/cli/azure/vm#az-vm-deallocate):

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

Sedan startar du den virtuella datorn med kommandot [az vm start]( /cli/azure/vm#az-vm-stop):

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

## <a name="install-diagnostics-extension"></a>Installera diagnostiktillägget

Grundläggande värdmått är tillgängliga, men om du vill se mer detaljerade och VM-specifika mått måste du installera Azure-diagnostiktillägget på den virtuella datorn. Med Azure-diagnostiktillägget kan du få ut mer övervaknings- och diagnostikdata från den virtuella datorn. Du kan visa dessa prestandamått och skapa aviseringar baserat på hur det går för den virtuella datorn. Diagnostiktillägget installeras via Azure Portal på följande sätt:

1. I Azure Portal: välj **Resource Groups**, välj **myResourceGroupMonitor** och välj sedan **myVM** i resurslistan.
1. Klicka på **diagnosinställningar**. I listrutan *Välj ett lagringskonto* väljer du kontot *mydiagdata [1234]* som har skapats i föregående avsnitt, om du inte redan har gjort det.
1. Välj **Aktivera övervakning på gästnivå**.

    ![Visa diagnostikmått](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>Visa VM-mått

Du kan visa VM-mått på samma sätt som du visade VM-värdmått:

1. I Azure Portal: välj **Resource Groups**, välj **myResourceGroupMonitor** och välj sedan **myVM** i resurslistan.
1. Om du vill se hur det går för den virtuella datorn väljer du **Mått** på fönstret för den virtuella datorn och sedan något av diagnostikmåtten *[Guest]* under **Tillgängliga mått**.

    ![Visa VM-mått](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>Skapa aviseringar

Du kan skapa aviseringar baserat på specifika prestandamått. Aviseringar kan till exempel användas för att informera dig om när den genomsnittliga CPU-användning överskrider ett visst tröskelvärde eller när det lediga diskutrymmet understiger ett visst värde. Aviseringar kan visas i Azure Portal eller skickas via e-post. Du kan också utlösa Azure Automation-runbooks eller Azure Logic Apps när aviseringar skapas.

I följande exempel skapas en avisering för genomsnittlig CPU-användning.

1. I Azure Portal: välj **Resource Groups**, välj **myResourceGroupMonitor** och välj sedan **myVM** i resurslistan.
2. Välj **Aviseringar (klassisk)**, välj sedan att **Lägg till måttavisering (klassisk)** längst upp i fönstret aviseringar.
3. Ange ett **namn** för aviseringen, till exempel *myAlertRule*
4. Om du vill utlösa en avisering när CPU-procenten har varit högre än 1,0 i fem minuter lämnar du alla andra standardinställningar valda.
5. Du kan också markera kryssrutan för *E-postägare, deltagare och läsare* om du vill skicka ett e-postmeddelande. Standardåtgärden är att visa en avisering i portalen.
6. Välj knappen **OK**.

## <a name="manage-software-updates"></a>Hantera programuppdateringar

Med uppdateringshantering kan du hantera uppdateringar och korrigeringar för dina virtuella Azure Linux-datorer.
Du kan snabbt se status för tillgängliga uppdateringar, schemalägga installation av nödvändiga uppdateringar och granska distributionsresultat för att verifiera att uppdateringarna har tillämpats på den virtuella datorn, direkt från den virtuella datorn.

Prisinformation finns i [Automation-priser för uppdateringshantering](https://azure.microsoft.com/pricing/details/automation/)

### <a name="enable-update-management"></a>Aktivera uppdateringshantering

Så här aktiverar du uppdateringshantering för dina virtuella datorer:

1. Välj **Virtuella datorer** till vänster på skärmen.
2. Välj en virtuell dator i listan.
3. Välj **Hantering av uppdateringar** i avsnittet **Åtgärder** på VM-skärmen. Skärmen **Aktivera hantering av uppdateringar** visas.

Verifieringen utförs för att fastställa om uppdateringshantering är aktiverat för den här virtuella datorn.
Verifieringen söker efter en Log Analytics-arbetsyta och ett länkat Automation-konto, och om lösningen är i arbetsytan.

En [Log Analytics](../../log-analytics/log-analytics-overview.md)-arbetsyta används för att samla in data som genereras av funktioner och tjänster som uppdateringshantering.
Arbetsytan tillhandahåller en enda plats för att granska och analysera data från flera källor.
Om du vill utföra ytterligare åtgärder på virtuella datorer som kräver uppdateringar kan Azure Automation köra runbooks mot virtuella datorer, till exempel ladda ned och installera uppdateringar.

Verifieringsprocessen kontrollerar också om den virtuella datorn har etablerats med Log Analytics-agenten och Automation Hybrid Runbook Worker. Den här agenten används för att kommunicera med den virtuella datorn och hämta information om uppdateringsstatus.

Välj det Log Analytics-arbetsytan och automation-kontot och välj **aktivera** att aktivera lösningen. Det tar upp till 15 minuter att aktivera lösningen.

Om några av följande krav saknades under publiceringen läggs de till automatiskt:

* [Log Analytics](../../log-analytics/log-analytics-overview.md)-arbetsyta
* [Automation-konto](../../automation/automation-offering-get-started.md)
* En [Hybrid runbook worker](../../automation/automation-hybrid-runbook-worker.md) aktiveras på den virtuella datorn

Skärmen **Uppdateringshantering** öppnas. Konfigurera platsen, Log Analytics-arbetsytan och Automation-konto du använder och välj **aktivera**. Om fälten är nedtonade betyder det att någon annan automatiseringslösning är aktiverad för den virtuella datorn, och samma arbetsyta och Automation-konto måste användas.

![Aktivera lösningen för hantering av uppdateringar](./media/tutorial-monitoring/manage-updates-update-enable.png)

Det kan ta upp till 15 minuter att aktivera lösningen. Under tiden ska du inte stänga webbläsaren. När lösningen har aktiverats flödar information om saknade uppdateringar på den virtuella datorn till Azure Monitor-loggar. Det kan ta mellan 30 minuter och 6 timmar innan data blir tillgängliga för analys.

### <a name="view-update-assessment"></a>Visa kontroll av uppdateringar

När **uppdateringshantering** är aktiverat visas skärmen **Hantering av uppdateringar**. När utvärderingen av uppdateringarna är klar ser du en lista med uppdateringar som saknas på fliken  **Uppdateringar som saknas**.

 ![Visa uppdateringsstatus](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>Schemalägga en uppdateringsdistribution

För att installera uppdateringar schemalägger du en distribution som passar ditt schema och servicefönster. Du kan välja vilka uppdateringstyper som ska tas med i distributionen. Du kan till exempel ta med kritiska uppdateringar eller säkerhetsuppdateringar och exkludera samlade uppdateringar.

Schemalägg en ny uppdateringsdistribution för den virtuella datorn genom att välja **Distribution av schemauppdatering** längst upp på skärmen **Hantering av uppdateringar**. På skärmen **Ny uppdateringsdistribution** anger du följande information:

* **Namn** – Ett unikt namn som identifierar uppdateringsdistributionen.
* **Uppdatera klassificering** – Välj vilka typer av programvara som ska tas med i uppdateringsdistributionen. Klassificeringstyper:
  * Kritiska uppdateringar och säkerhetsuppdateringar
  * Övriga uppdateringar
* **Uppdateringar som ska hoppas över** – Du kan ange en lista över paketnamn som ska hoppas över när uppdateringen distribueras. Det går att använda jokertecken i paketnamn (t.ex. \*kernel\*).

  ![Inställningsskärmen för uppdateringsschema](./media/tutorial-monitoring/manage-updates-exclude-linux.png)

* **Schemainställningar** – Du kan antingen godkänna standarddatumet och -tiden, d.v.s. 30 minuter efter aktuell tid, eller så kan du ange en annan tid.
  Du kan också ange om distributionen ska ske en gång eller ange ett schema med återkommande tider. Välj alternativet Återkommande under Återkommande för att ange ett återkommande schema.

  ![Inställningsskärmen för uppdateringsschema](./media/tutorial-monitoring/manage-updates-schedule-linux.png)

* **Underhållsperiod (minuter)** – Ange den tidsperiod som uppdateringsdistributionen ska utföras inom. På så sätt försäkrar du dig om att ändringarna utförs inom ditt definierade servicefönster.

När du har konfigurerat schemat väljer du **Skapa**. Därmed återgår du till statusinstrumentpanelen.
Observera att tabellen **Schemalagt** visar det distributionsschema som du skapade.

> [!WARNING]
> Om en uppdatering kräver omstart startas den virtuella datorn om automatiskt.

### <a name="view-results-of-an-update-deployment"></a>Visa resultat för en uppdateringsdistribution

När den schemalagda distributionen startar kan du se status för distributionen på fliken **Uppdateringsdistributioner** på skärmen **Uppdateringshantering**.
Om distributionen körs visas status **Pågår**. När distributionen har slutförts ändras status till **Lyckades**.
Om det uppstod något fel med en eller flera uppdateringar i distributionen visas status **Misslyckades delvis**.
Välj den slutförda uppdateringsdistributionen för att visa instrumentpanelen för distributionen.

![Statusinstrumentpanel för Uppdatera distribution för specifik distribution](./media/tutorial-monitoring/manage-updates-view-results.png)

På panelen för **uppdateringsresultat** visas en sammanfattning av det totala antalet uppdateringar och distributionsresultat för den virtuella datorn.
I tabellen till höger visas detaljer för varje uppdatering och installationsresultaten, som kan ha ett av följande värden:

* **Inget försök har gjorts** – Uppdateringen installerades inte eftersom det inte fanns tillräckligt med tid utifrån det underhållsfönster som definierats.
* **Lyckades** – Uppdateringen lyckades
* **Misslyckades** – Uppdateringen misslyckades

Välj **Alla loggar** om du vill se alla loggposter som har skapats för distributionen.

Välj panelen **Utdata** om du vill se jobbströmmen för den runbook som ansvarar för att hantera uppdateringsdistributionen på den virtuella måldatorn.

Välj **Fel** om du vill se detaljerad information om fel som uppstått vid distributionen.

## <a name="monitor-changes-and-inventory"></a>Övervaka ändringar och inventering

Du kan samla in och visa inventeringar för program, filer, Linux-daemons, Windows-tjänster och Windows-registernycklar på dina datorer. Om du spårar konfigurationerna för dina datorer kan du identifiera driftproblem i miljön och bättre förstå datorernas tillstånd.

### <a name="enable-change-and-inventory-management"></a>Aktivera ändringsspårning och inventering

Så här aktiverar du ändringsspårning och inventering för din virtuella dator:

1. Välj **Virtuella datorer** till vänster på skärmen.
2. Välj en virtuell dator i listan.
3. Gå till avsnittet **Åtgärder** på VM-skärmen och klicka på **Inventering** eller **Ändringsspårning**. Skärmen **Aktivera ändringsspårning och inventering** öppnas.

Konfigurera platsen, Log Analytics-arbetsytan och Automation-konto du använder och välj **aktivera**. Om fälten är nedtonade betyder det att någon annan automatiseringslösning är aktiverad för den virtuella datorn, och samma arbetsyta och Automation-konto måste användas. Trots att lösningarna är separata på menyn är de samma lösning. När du aktiverar den ena aktiveras båda för den virtuella datorn.

![Aktivera Ändringsspårning och lager](./media/tutorial-monitoring/manage-inventory-enable.png)

När lösningen har aktiverats kan det dröja en stund medan lagret som samlas in på den virtuella datorn innan data visas.

### <a name="track-changes"></a>Spåra ändringar

På din virtuella dator väljer du **Ändringsspårning** under **ÅTGÄRDER**. Välj **Redigera inställningar**. Sidan **Ändringsspårning** visas. Välj typ av inställning som du vill spåra och klicka sedan på **+ Lägg till** för att konfigurera inställningarna. Det tillgängliga Linux-alternativet är **Linux-filer**

Mer information om ändringsspårning finns i [Felsöka ändringar på en virtuell dator](../../automation/automation-tutorial-troubleshoot-changes.md)

### <a name="view-inventory"></a>Visa inventering

Från din virtuella dator väljer du **Inventering** under **ÅTGÄRDER**. På fliken **Programvara** finns det en tabell som anger programvaran som har hittats. Utförlig information för varje programpost visas i tabellen. Uppgifterna omfattar bland annat programvarunamn, version, utgivare och datum för senaste uppdatering.

![Visa inventering](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Övervaka aktivitetsloggar och ändringar

Från sidan **Ändringsspårning** på din virtuella dator väljer du **Hantera aktivitetslogganslutning**. Den här uppgiften öppnar sidan **Azure-aktivitetslogg**. Välj **Anslut** för att ansluta Ändringsspårning till Azure-aktivitetsloggen för din virtuella dator.

När den här inställningen är aktiverad går du till sidan **Översikt** för din virtuella dator och väljer **Stoppa** för att stoppa din virtuella dator. När du uppmanas väljer du **Ja** för att stoppa den virtuella datorn. När den har frigjorts väljer du **Starta** för att starta om din virtuella dator.

När en virtuell dator stoppas och startas loggar den virtuella datorn en händelse i aktivitetsloggen. Gå tillbaka till sidan **Ändringsspårning**. Välj fliken **Händelser** längst ned på sidan. Efter ett tag visas händelserna i diagrammet och tabellen. Du kan välja varje händelse för att visa detaljerad information om händelsen.

![Visa händelser i aktivitetsloggen](./media/tutorial-monitoring/manage-activitylog-view-results.png)

Diagrammet visar ändringar som har skett över tid. När du har lagt till en aktivitetslogganslutning visar linjediagrammet högst upp händelser i Azure-aktivitetsloggen. Varje rad med stapeldiagram representerar en annan spårningsbar ändringstyp. Typerna är Linux-daemons, filer och program. På fliken Ändra visas information för de ändringar som visas i visualiseringen i fallande tidsordning för när ändringen skedde (senaste först).

## <a name="advanced-monitoring"></a>Avancerad övervakning

Du kan utföra mer avancerad övervakning av den virtuella datorn med hjälp av en lösning som [Azure Monitor för virtuella datorer](../../azure-monitor/insights/vminsights-overview.md), som övervakar dina virtuella Azure-datorer (VM) i stor skala genom att analysera prestanda och hälsotillståndet för virtuella Windows- och Linux-datorer, inklusive deras olika processer och inbördes beroenden i förhållande till andra resurser och externa processer. Konfigurationen av dina virtuella datorer i Azure hanteras med hjälp av lösningen Ändringsspårning och inventering i [Azure Automation](../../automation/automation-intro.md), så att du enkelt kan identifiera ändringar i miljön. Uppdateringskompatibiliteten hanteras med hjälp av lösningen Hantering av uppdateringar i Azure Automation.   

Den virtuella datorn ansluts till dig via Log Analytics-arbetsytan och du kan även hämta, konsolidera och analysera insamlade data med [avancerade frågespråk](../../azure-monitor/log-query/log-query-overview.md). 

![Log Analytics-arbetsyta](./media/tutorial-monitoring/tutorial-monitor-oms.png)

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
> * Övervaka ändringar och inventering
> * Konfigurera avancerad övervakning

Gå vidare till nästa kurs om du vill veta mer om Azure Security Center.

> [!div class="nextstepaction"]
> [Hantera säkerheten för virtuella datorer](./tutorial-azure-security.md)
