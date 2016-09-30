<properties
   pageTitle="Komma igång med Azure Batch CLI | Microsoft Azure"
   description="Få en snabb introduktion till Batch-kommandon i Azure CLI för att hantera Azure Batch-tjänstens resurser"
   services="batch"
   documentationCenter=""
   authors="mmacy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="big-compute"
   ms.date="09/06/2016"
   ms.author="marsma"/>


# Komma igång med Azure Batch CLI

Med det plattformsoberoende Azure-kommandoradsgränssnittet (Azure CLI) kan du hantera Batch-konton och resurser, till exempel pooler, jobb och aktiviteter i Linux-, Mac- och Windows-kommandogränssnitt. Med Azure CLI kan du genomföra och skriva många av de uppgifter som du utför med Batch-API:erna, Azure-portalen och Batch PowerShell-cmdletarna.

Den här artikeln är baserad på Azure CLI version 0.10.3.

## Krav

* [Installera Azure CLI](../xplat-cli-install.md)

* [Ansluta Azure CLI till din Azure-prenumeration](../xplat-cli-connect.md)

* Växla till **Resource Manager-läge**: `azure config mode arm`

>[AZURE.TIP] Vi rekommenderar att du uppdaterar Azure CLI-installationen ofta för att dra nytta av tjänstuppdateringar och förbättringar.

## Kommandohjälp

Du kan visa hjälptext för alla kommandon i Azure CLI genom att lägga till `-h` som det enda alternativet efter kommandot. Några exempel:

* Om du vill få hjälp med `azure`-kommandot anger du: `azure -h`
* Om du vill hämta en lista över alla Batch-kommandon i CLI använder du: `azure batch -h`
* Om du vill ha hjälp med att skapa ett Batch-konto anger du: `azure batch account create -h`

Om du är tveksam ska du använda kommandoradsalternativet `-h` för att få hjälp med samtliga Azure CLI-kommandon.

## Skapa ett Batch-konto

Användning:

    azure batch account create [options] <name>

Exempel:

    azure batch account create --location "West US"  --resource-group "resgroup001" "batchaccount001"

Skapar ett nytt Batch-konto med de angivna parametrarna. Du måste ange minst en plats, en resursgrupp och ett kontonamn. Om du inte redan har en resursgrupp skapar du en genom att köra `azure group create` och ange en av Azure-regionerna (till exempel ”USA, västra”) för alternativet `--location`. Några exempel:

    azure group create --name "resgroup001" --location "West US"

> [AZURE.NOTE] Batch-kontonamnet måste vara unikt i den Azure-region som kontot skapas i. Det får bara innehålla gemena alfanumeriska tecken och måste vara mellan 3 och 24 tecken långt. Du kan inte använda specialtecken som `-` eller `_` i Batch-kontonamn.

### Länkat lagringskonto (autostorage)

Du kan (om du vill) länka ett **allmänt** lagringskonto till ditt Batch-konto när du skapar det. [Programpaketfunktionen](batch-application-packages.md) i Batch använder blobblagring i ett länkat allmänt Storage-konto, precis som [.NET-biblioteket för filkonventioner i Batch](batch-task-output.md). Med dessa valfria funktioner kan du distribuera de program som körs av Batch-aktiviteterna och spara de data som de genererar.

För att länka ett befintligt Azure Storage-konto till ett nytt Batch-konto när du skapar det anger du `--autostorage-account-id`-alternativet. Det här alternativet kräver det fullständigt kvalificerade resurs-ID:t för lagringskontot.

Först ska du visa dina lagringskontouppgifter:

    azure storage account show --resource-group "resgroup001" "storageaccount001"

Använd sedan **Url**-värdet för `--autostorage-account-id`alternativet. Url-värdet börjar med "/subscriptions/" och innehåller ditt prenumerations-ID och resurssökvägen till lagringskontot:

    azure batch account create --location "West US"  --resource-group "resgroup001" --autostorage-account-id "/subscriptions/8ffffff8-4444-4444-bfbf-8ffffff84444/resourceGroups/resgroup001/providers/Microsoft.Storage/storageAccounts/storageaccount001" "batchaccount001"

## Ta bort ett Batch-konto

Användning:

    azure batch account delete [options] <name>

Exempel:

    azure batch account delete --resource-group "resgroup001" "batchaccount001"

Tar bort det angivna Batch-kontot. När du uppmanas till det, bekräftar du att du vill ta bort kontot (det kan ta lite tid att ta bort kontot).

## Hantera kontots åtkomstnycklar

Du behöver en åtkomstnyckel för att [skapa och ändra resurser](#create-and-modify-batch-resources) i ditt Batch-konto.

### Lista åtkomstnycklar

Användning:

    azure batch account keys list [options] <name>

Exempel:

    azure batch account keys list --resource-group "resgroup001" "batchaccount001"

Listar kontonycklarna för det givna Batch-kontot.

### Generera en ny åtkomstnyckel

Användning:

    azure batch account keys renew [options] --<primary|secondary> <name>

Exempel:

    azure batch account keys renew --resource-group "resgroup001" --primary "batchaccount001"

Återskapar den angivna kontonyckeln för det angivna Batch-kontot.

## Skapa och ändra Batch-resurser

Du kan använda Azure CLI för att skapa, läsa, uppdatera och ta bort (CRUD) Batch-resurser som pooler, beräkningsnoder, jobb och uppgifter. Dessa CRUD-åtgärder kräver Batch-kontonamn, åtkomstnyckel och slutpunkt. Du kan ange dem med alternativen `-a`, `-k` och `-u` eller ange [miljövariabler](#credential-environment-variables) som CLI använder automatiskt (om de fyllts i).

### Miljövariabler för autentiseringsuppgifter

Du kan ställa in miljövariablerna `AZURE_BATCH_ACCOUNT`, `AZURE_BATCH_ACCESS_KEY` och `AZURE_BATCH_ENDPOINT` i stället för att ange alternativen `-a`, `-k` och `-u` på kommandoraden för alla kommandon som du kör. Batch CLI använder dessa variabler (om de har angetts) så att du kan hoppa över alternativen `-a`, `-k`, och `-u`. Resten av den här artikeln förutsätter att du använder dig av dessa miljövariabler.

>[AZURE.TIP] Visa en lista med dina nycklar med `azure batch account keys list` och visa kontots slutpunkt med `azure batch account show`.

### JSON-filer

När du skapar Batch-resurser som pooler och jobb kan ange du en JSON-fil som innehåller den nya resursens konfiguration i stället för att ange dess parametrar som kommandoradsalternativ. Några exempel:

`azure batch pool create my_batch_pool.json`

Du kan utföra många resursskapande åtgärder med endast kommandoradsalternativ, men vissa funktioner kräver en JSON-formaterad fil som innehåller information om resursen. Du måste till exempel använda en JSON-fil om du vill ange resursfiler för en startuppgift.

För att hitta den JSON-fil som krävs för att skapa en resurs ska du gå till dokumentationen [Referens för Batch REST API ][rest_api] på MSDN. Varje avsnitt med ”Lägg till *resurstyp*” innehåller exempel på JSON-filer för att skapa en resurs, som du kan använda som en mall för JSON-filer. Till exempel finns en JSON-fil för att skapa en pool i [Lägga till en pool till ett konto][rest_add_pool].

>[AZURE.NOTE] Om du anger en JSON-fil när du skapar en resurs ignoreras alla andra parametrar som du anger på kommandoraden för den resursen.

## Skapa en pool

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

>[AZURE.TIP] Kontrollera [listan med virtuella datoravbildningar](batch-linux-nodes.md#list-of-virtual-machine-images) för värden som är lämpliga för `--image-*`-alternativen.

## Skapa ett jobb

Användning:

    azure batch job create [options] [json-file]

Exempel:

    azure batch job create --id "job001" --pool-id "pool001"

Lägger till ett jobb till Batch-kontot och anger den pool där uppgifterna utförs.

Ta bort ett jobb med:

    azure batch job delete [job-id]

## Lista pooler, jobb, uppgifter och andra resurser

Varje Batch-resurstyp stöder ett `list`-kommando som frågar Batch-kontot och visar en lista över resurser av den typen. Du kan till exempel ange poolerna i ditt konto och aktiviteterna i ett jobb:

    azure batch pool list
    azure batch task list --job-id "job001"

### Visa en lista över resurser på ett effektivt sätt

För snabbare frågor kan du ange satsalternativen **välj**, **filtrera** och **expandera** för `list`-åtgärder. Med dessa alternativ kan du begränsa mängden data som returneras av Batch-tjänsten. Eftersom all filtrering utförs på serversidan överförs endast de data som du är intresserad av. Använd dessa satser för att spara bandbredd (och därmed tid) när du utför liståtgärder.

Exempelvis returnerar detta endast pooler vars ID-nummer som börjar med ”renderTask”:

    azure batch task list --job-id "job001" --filter-clause "startswith(id, 'renderTask')"

Batch-CLI stöder alla tre satser som stöds av Batch-tjänsten:

* `--select-clause [select-clause]`  Returnera en delmängd av egenskaperna för varje entitet
* `--filter-clause [filter-clause]`  Returnera endast de enheter som matchar det angivna OData-uttrycket
* `--expand-clause [expand-clause]`  Hämta entitetsinformationen i ett enda underliggande REST-anrop. Expandera-satsen stöder endast `stats`-egenskapen för tillfället.

Mer information om de tre satserna och hur man utför listfrågor med dem finns i [Skicka effektiva frågor till Azure Batch-tjänsten](batch-efficient-list-queries.md).

## Hantera programpaket

Programpaket är ett förenklat sätt att distribuera program till beräkningsnoder i dina pooler. Med Azure-CLI kan du överföra programpaket, hantera paketversioner och ta bort paket.

Så här skapar du ett nytt program och lägger till en paketversion:

**Skapa** ett program:

    azure batch application create "resgroup001" "batchaccount001" "MyTaskApplication"

**Lägg till** ett programpaket:

    azure batch application package create "resgroup001" "batchaccount001" "MyTaskApplication" "1.10-beta3" package001.zip

**Aktivera** paketet:

    azure batch application package activate "resgroup002" "azbatch002" "MyTaskApplication" "1.10-beta3" zip

### Distribuera ett programpaket

Du kan ange ett eller flera programpaket för distribution när du skapar en ny pool. När du anger ett paket när poolen skapas distribueras den till varje nod när noden ansluter till poolen. Paket distribueras också när en nod startas om eller när en avbildning återställs.

Detta kommando anger ett paket när en pool skapas och distribueras när varje nod ansluter till den nya poolen:

    azure batch pool create --id "pool001" --target-dedicated 1 --vm-size "small" --os-family "4" --app-package-ref "MyTaskApplication"

För tillfället kan du inte ange vilken paketversion som ska distribueras med hjälp av kommandoradsalternativen. Du måste först ange en standardversion för programmet på Azure-portalen innan du kan tilldela den till en pool. Mer information om att ange en standardversion finns i [Programdistribution med Azure Batch-programpaket](batch-application-packages.md). Du kan dock ange en standardversion om du använder en [JSON-fil](#json-files) i stället för kommandoradsalternativ när du skapar en pool.

>[AZURE.IMPORTANT] Du måste [koppla ett Azure Storage-konto](#linked-storage-account-autostorage) till Batch-kontot för att använda programpaket.

## Felsökningstips

I det här avsnittet finns resurser som du kan använda när du felsöker problem med Azure CLI. Det kanske inte finns lösningar på alla problem, men du kan få hjälp med att begränsa orsaken och tips om hjälpresurser.

* Använd `-h` för att hämta **hjälptext** för alla CLI-kommandon

* Använd `-v` och `-vv` för att visa **utförliga** kommandoutdata; `-vv` är ”extra” utförliga och visar faktiska REST-begäranden och -svar. Växlarna är användbara för att visa fullständiga utdata vid fel.

* Du kan visa **-kommandoutdata som JSON-** med `--json`-alternativet. Till exempel visar `azure batch pool show "pool001" --json` pool001:s egenskaper i JSON-format. Du kan sedan kopiera och ändra dessa utdata och använda dem i en `--json-file` (se [JSON-filer](#json-files) tidigare i den här artikeln).

* [Batch-forumet på MSDN][batch_forum] är en bra hjälpresurs och följs nära av Batch-gruppmedlemmar. Skicka dina frågor dit om du får problem eller behöver hjälp med en viss åtgärd.

* Azure CLI stöder för närvarande inte alla Batch-resursåtgärder. För tillfället kan du till exempel inte ange ett programpakets *version* för en pool, bara paketets ID. I sådana fall kanske du måste ange en `--json-file` för ett kommando i stället för att använda kommandoradsalternativ. Se till att hålla dig uppdaterad med den senaste CLI-versionen för framtida förbättringar.

## Nästa steg

*  Se [Programdistribution med Azure Batch-programpaket](batch-application-packages.md) för att lära dig hur du använder den här funktionen för att hantera och distribuera de program som du kör på Batch-beräkningsnoder.

* Mer information om hur du minskar antalet objekt och vilken typ av information som returneras för frågor till Batch finns i [Skicka effektiva frågor till Batch-tjänsten](batch-efficient-list-queries.md).

[batch_forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[github_readme]: https://github.com/Azure/azure-xplat-cli/blob/dev/README.md
[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx


<!--HONumber=Sep16_HO3-->


