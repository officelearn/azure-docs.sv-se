---
title: "Komma igång med Azure Batch CLI | Microsoft Docs"
description: "Få en snabb introduktion till Batch-kommandon i Azure CLI för att hantera Azure Batch-tjänstens resurser"
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: fcd76587-1827-4bc8-a84d-bba1cd980d85
ms.service: batch
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 09/30/2016
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: dfcf1e1d54a0c04cacffb50eca4afd39c6f6a1b1
ms.openlocfilehash: cb1f049597d53263f0f92fe1192b285c09d78eeb


---
# <a name="get-started-with-azure-batch-cli"></a>Komma igång med Azure Batch CLI
Med det plattformsoberoende Azure-kommandoradsgränssnittet (Azure CLI) kan du hantera Batch-konton och resurser, till exempel pooler, jobb och aktiviteter i Linux-, Mac- och Windows-kommandogränssnitt. Med Azure CLI kan du genomföra och skriva många av de uppgifter som du utför med Batch-API:erna, Azure-portalen och Batch PowerShell-cmdletarna.

Den här artikeln är baserad på Azure CLI version 0.10.5.

## <a name="prerequisites"></a>Krav
* [Installera Azure CLI](../xplat-cli-install.md)
* [Ansluta Azure CLI till din Azure-prenumeration](../xplat-cli-connect.md)
* Växla till **Resource Manager-läge**:`azure config mode arm`

> [!TIP]
> Vi rekommenderar att du uppdaterar Azure CLI-installationen ofta för att dra nytta av tjänstuppdateringar och förbättringar.
> 
> 

## <a name="command-help"></a>Kommandohjälp
Du kan visa hjälptext för alla kommandon i Azure CLI genom att lägga till `-h` som det enda alternativet efter kommandot. Exempel:

* Om du vill få hjälp med `azure`-kommandot anger du: `azure -h`
* Om du vill hämta en lista över alla Batch-kommandon i CLI använder du: `azure batch -h`
* Om du vill ha hjälp med att skapa ett Batch-konto anger du: `azure batch account create -h`

Om du är tveksam ska du använda kommandoradsalternativet `-h` för att få hjälp med samtliga Azure CLI-kommandon.

## <a name="create-a-batch-account"></a>Skapa ett Batch-konto
Användning:

    azure batch account create [options] <name>

Exempel:

    azure batch account create --location "West US"  --resource-group "resgroup001" "batchaccount001"

Skapar ett nytt Batch-konto med de angivna parametrarna. Du måste ange minst en plats, en resursgrupp och ett kontonamn. Om du inte redan har en resursgrupp skapar du en genom att köra `azure group create` och ange en av Azure-regionerna (till exempel ”USA, västra”) för alternativet `--location`. Några exempel:

    azure group create --name "resgroup001" --location "West US"

> [!NOTE]
> Batch-kontonamnet måste vara unikt i den Azure-region som kontot skapas i. Det får bara innehålla gemena alfanumeriska tecken och måste vara mellan 3 och 24 tecken långt. Du kan inte använda specialtecken som `-` eller `_` i Batch-kontonamn.
> 
> 

### <a name="linked-storage-account-autostorage"></a>Länkat lagringskonto (autostorage)
Du kan (om du vill) länka ett **allmänt** lagringskonto till ditt Batch-konto när du skapar det. [Programpaketfunktionen](batch-application-packages.md) i Batch använder blobblagring i ett länkat allmänt Storage-konto, precis som [.NET-biblioteket för filkonventioner i Batch](batch-task-output.md). Med dessa valfria funktioner kan du distribuera de program som körs av Batch-aktiviteterna och spara de data som de genererar.

För att länka ett befintligt Azure Storage-konto till ett nytt Batch-konto när du skapar det anger du `--autostorage-account-id`-alternativet. Det här alternativet kräver det fullständigt kvalificerade resurs-ID:t för lagringskontot.

Först ska du visa dina lagringskontouppgifter:

    azure storage account show --resource-group "resgroup001" "storageaccount001"

Använd sedan **Url**-värdet för `--autostorage-account-id`alternativet. Url-värdet börjar med "/subscriptions/" och innehåller ditt prenumerations-ID och resurssökvägen till lagringskontot:

    azure batch account create --location "West US"  --resource-group "resgroup001" --autostorage-account-id "/subscriptions/8ffffff8-4444-4444-bfbf-8ffffff84444/resourceGroups/resgroup001/providers/Microsoft.Storage/storageAccounts/storageaccount001" "batchaccount001"

## <a name="delete-a-batch-account"></a>Ta bort ett Batch-konto
Användning:

    azure batch account delete [options] <name>

Exempel:

    azure batch account delete --resource-group "resgroup001" "batchaccount001"

Tar bort det angivna Batch-kontot. När du uppmanas till det, bekräftar du att du vill ta bort kontot (det kan ta lite tid att ta bort kontot).

## <a name="manage-account-access-keys"></a>Hantera kontots åtkomstnycklar
Du behöver en åtkomstnyckel för att [skapa och ändra resurser](#create-and-modify-batch-resources) i ditt Batch-konto.

### <a name="list-access-keys"></a>Lista åtkomstnycklar
Användning:

    azure batch account keys list [options] <name>

Exempel:

    azure batch account keys list --resource-group "resgroup001" "batchaccount001"

Listar kontonycklarna för det givna Batch-kontot.

### <a name="generate-a-new-access-key"></a>Generera en ny åtkomstnyckel
Användning:

    azure batch account keys renew [options] --<primary|secondary> <name>

Exempel:

    azure batch account keys renew --resource-group "resgroup001" --primary "batchaccount001"

Återskapar den angivna kontonyckeln för det angivna Batch-kontot.

## <a name="create-and-modify-batch-resources"></a>Skapa och ändra Batch-resurser
Du kan använda Azure CLI för att skapa, läsa, uppdatera och ta bort (CRUD) Batch-resurser som pooler, beräkningsnoder, jobb och uppgifter. Dessa CRUD-åtgärder kräver Batch-kontonamn, åtkomstnyckel och slutpunkt. Du kan ange dem med alternativen `-a`, `-k` och `-u` eller ange [miljövariabler](#credential-environment-variables) som CLI använder automatiskt (om de fyllts i).

### <a name="credential-environment-variables"></a>Miljövariabler för autentiseringsuppgifter
Du kan ställa in miljövariablerna `AZURE_BATCH_ACCOUNT`, `AZURE_BATCH_ACCESS_KEY` och `AZURE_BATCH_ENDPOINT` i stället för att ange alternativen `-a`, `-k` och `-u` på kommandoraden för alla kommandon som du kör. Batch CLI använder dessa variabler (om de har angetts) så att du kan hoppa över alternativen `-a`, `-k`, och `-u`. Resten av den här artikeln förutsätter att du använder dig av dessa miljövariabler.

> [!TIP]
> Visa en lista med dina nycklar med `azure batch account keys list` och visa kontots slutpunkt med `azure batch account show`.
> 
> 

### <a name="json-files"></a>JSON-filer
När du skapar Batch-resurser som pooler och jobb kan ange du en JSON-fil som innehåller den nya resursens konfiguration i stället för att ange dess parametrar som kommandoradsalternativ. Några exempel:

`azure batch pool create my_batch_pool.json`

Du kan utföra många resursskapande åtgärder med endast kommandoradsalternativ, men vissa funktioner kräver en JSON-formaterad fil som innehåller information om resursen. Du måste till exempel använda en JSON-fil om du vill ange resursfiler för en startuppgift.

Om du vill hitta den JSON som krävs för att skapa en resurs bör du läsa dokumentationen [Referens för Batch REST API][rest_api] på MSDN. Varje avsnitt med ”Lägg till *resurstyp*” innehåller exempel på JSON-filer för att skapa en resurs, som du kan använda som en mall för JSON-filer. Det finns t.ex. en JSON-fil för att skapa en pool i [Lägga till en pool till ett konto][rest_add_pool].

> [!NOTE]
> Om du anger en JSON-fil när du skapar en resurs ignoreras alla andra parametrar som du anger på kommandoraden för den resursen.
> 
> 

## <a name="create-a-pool"></a>Skapa en pool
Användning:

    azure batch pool create [options] [json-file]

Exempel (konfiguration av virtuell dator):

    azure batch pool create --id "pool001" --target-dedicated 1 --vm-size "STANDARD_A1" --image-publisher "Canonical" --image-offer "UbuntuServer" --image-sku "14.04.2-LTS" --node-agent-id "batch.node.ubuntu 14.04"

Exempel (Cloud Services-konfiguration):

    azure batch pool create --id "pool002" --target-dedicated 1 --vm-size "small" --os-family "4"

Skapar en pool av beräkningsnoder i Batch-tjänsten.

Som vi nämnde i [Funktionsöversikt över Batch](batch-api-basics.md#pool) har du två alternativ när du väljer operativsystem för noderna i en pool: **konfiguration av virtuell dator** (VM-konfiguration) och **Cloud Services-konfiguration**. Använd `--image-*`-alternativen för att skapa virtuella datorkonfigurationspooler och `--os-family` för att skapa Cloud Services-konfigurationspooler. Du kan inte ange både `--os-family`- och `--image-*`-alternativ.

Du kan ange [poolprogrampaket](batch-application-packages.md) och kommandoraden för en [startuppgift](batch-api-basics.md#start-task). Om du vill ange resursfiler för startuppgiften måste du i stället använda en [JSON-fil](#json-files).

Ta bort en pool med:

    azure batch pool delete [pool-id]

> [!TIP]
> Kontrollera [listan med virtuella datoravbildningar](batch-linux-nodes.md#list-of-virtual-machine-images) för värden som är lämpliga för `--image-*`-alternativen.
> 
> 

## <a name="create-a-job"></a>Skapa ett jobb
Användning:

    azure batch job create [options] [json-file]

Exempel:

    azure batch job create --id "job001" --pool-id "pool001"

Lägger till ett jobb till Batch-kontot och anger den pool där uppgifterna utförs.

Ta bort ett jobb med:

    azure batch job delete [job-id]

## <a name="list-pools-jobs-tasks-and-other-resources"></a>Lista pooler, jobb, uppgifter och andra resurser
Varje Batch-resurstyp stöder ett `list`-kommando som frågar Batch-kontot och visar en lista över resurser av den typen. Du kan till exempel ange poolerna i ditt konto och aktiviteterna i ett jobb:

    azure batch pool list
    azure batch task list --job-id "job001"

### <a name="listing-resources-efficiently"></a>Visa en lista över resurser på ett effektivt sätt
För snabbare frågor kan du ange satsalternativen **välj**, **filtrera** och **expandera** för `list`-åtgärder. Med dessa alternativ kan du begränsa mängden data som returneras av Batch-tjänsten. Eftersom all filtrering utförs på serversidan överförs endast de data som du är intresserad av. Använd dessa satser för att spara bandbredd (och därmed tid) när du utför liståtgärder.

Exempelvis returnerar detta endast pooler vars ID-nummer som börjar med ”renderTask”:

    azure batch task list --job-id "job001" --filter-clause "startswith(id, 'renderTask')"

Batch-CLI stöder alla tre satser som stöds av Batch-tjänsten:

* `--select-clause [select-clause]` Returnera en delmängd av egenskaperna för varje entitet
* `--filter-clause [filter-clause]` Returnera endast de enheter som matchar det angivna OData-uttrycket
* `--expand-clause [expand-clause]` Hämta entitetsinformationen i ett enda underliggande REST-anrop. Expandera-satsen stöder endast `stats`-egenskapen för tillfället.

Mer information om de tre satserna och hur man utför listfrågor med dem finns i [Skicka effektiva frågor till Azure Batch-tjänsten](batch-efficient-list-queries.md).

## <a name="application-package-management"></a>Hantera programpaket
Programpaket är ett förenklat sätt att distribuera program till beräkningsnoder i dina pooler. Med Azure-CLI kan du överföra programpaket, hantera paketversioner och ta bort paket.

Så här skapar du ett nytt program och lägger till en paketversion:

**Skapa** ett program:

    azure batch application create "resgroup001" "batchaccount001" "MyTaskApplication"

**Lägg till** ett programpaket:

    azure batch application package create "resgroup001" "batchaccount001" "MyTaskApplication" "1.10-beta3" package001.zip

**Aktivera** paketet:

    azure batch application package activate "resgroup001" "batchaccount001" "MyTaskApplication" "1.10-beta3" zip

Ange **standardversionen** för programmet:

    azure batch application set "resgroup001" "batchaccount001" "MyTaskApplication" --default-version "1.10-beta3"

### <a name="deploy-an-application-package"></a>Distribuera ett programpaket
Du kan ange ett eller flera programpaket för distribution när du skapar en ny pool. När du anger ett paket när poolen skapas distribueras den till varje nod när noden ansluter till poolen. Paket distribueras också när en nod startas om eller när en avbildning återställs.

Ange alternativet `--app-package-ref` när du skapar en pool för att distribuera ett programpaket till poolen noder efterhand som de ansluts till poolen. Alternativet `--app-package-ref` accepterar en semikolonavgränsad lista med program-ID för distribution till datornoder.

    azure batch pool create --pool-id "pool001" --target-dedicated 1 --vm-size "small" --os-family "4" --app-package-ref "MyTaskApplication"

När du skapar en pool med hjälp av kommandoradsalternativen kan du för närvarande inte ange *vilken* version av programpaketet som ska distribueras till datornoder, till exempel ”1.10-beta3”. Därför måste du ange en standardversion för program med `azure batch application set [options] --default-version <version-id>` innan du skapar poolen (se ovan). Du kan dock ange versioner för poolen om du använder en [JSON-fil](#json-files) i stället för kommandoradsalternativ när du skapar poolen.

Du hittar mer information om programpaket i [programdistribution med Azure Batch-programpaket](batch-application-packages.md).

> [!IMPORTANT]
> Du måste [koppla ett Azure Storage-konto](#linked-storage-account-autostorage) till Batch-kontot för att använda programpaket.
> 
> 

### <a name="update-a-pools-application-packages"></a>Uppdatera programpaket för en pool
Om du vill uppdatera program som är tilldelade till en befintlig pool använder du kommandot `azure batch pool set` med alternativet `--app-package-ref`:

    azure batch pool set --pool-id "pool001" --app-package-ref "MyTaskApplication2"

Du måste starta om eller återavbilda noderna för att distribuera nya programpaket för datornoder som redan befinner sig i en befintlig pool:

    azure batch node reboot --pool-id "pool001" --node-id "tvm-3105992504_1-20160930t164509z"

> [!TIP]
> Du kan hämta en lista över noder i poolen tillsammans med deras nod-ID med `azure batch node list`.
> 
> 

Tänk på att du måste ha konfigurerat programmet med en standardversion innan den distribueras (`azure batch application set [options] --default-version <version-id>`).

## <a name="troubleshooting-tips"></a>Felsökningstips
I det här avsnittet finns resurser som du kan använda när du felsöker problem med Azure CLI. Det kanske inte finns lösningar på alla problem, men du kan få hjälp med att begränsa orsaken och tips om hjälpresurser.

* Använd `-h` för att hämta **hjälptext** för alla CLI-kommandon
* Använd `-v` och `-vv` för att visa **utförliga** kommandoutdata; `-vv` är ”extra” utförliga och visar faktiska REST-begäranden och -svar. Växlarna är användbara för att visa fullständiga utdata vid fel.
* Du kan visa **-kommandoutdata som JSON-** med `--json`-alternativet. Till exempel visar `azure batch pool show "pool001" --json` pool001:s egenskaper i JSON-format. Du kan sedan kopiera och ändra dessa utdata och använda dem i en `--json-file` (se [JSON-filer](#json-files) tidigare i den här artikeln).
* [Batch-forumet på MSDN][batch_forum] är en bra hjälpresurs och följs nära av Batch-gruppmedlemmar. Skicka dina frågor dit om du får problem eller behöver hjälp med en viss åtgärd.
* Azure CLI stöder för närvarande inte alla Batch-resursåtgärder. För tillfället kan du till exempel inte ange ett programpakets *version* för en pool, bara paketets ID. I sådana fall kanske du måste ange en `--json-file` för ett kommando i stället för att använda kommandoradsalternativ. Se till att hålla dig uppdaterad med den senaste CLI-versionen för framtida förbättringar.

## <a name="next-steps"></a>Nästa steg
* Se [Programdistribution med Azure Batch-programpaket](batch-application-packages.md) för att lära dig hur du använder den här funktionen för att hantera och distribuera de program som du kör på Batch-beräkningsnoder.
* Mer information om hur du minskar antalet objekt och vilken typ av information som returneras för frågor till Batch finns i [Skicka effektiva frågor till Batch-tjänsten](batch-efficient-list-queries.md).

[batch_forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[github_readme]: https://github.com/Azure/azure-xplat-cli/blob/dev/README.md
[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx



<!--HONumber=Dec16_HO2-->


