---
title: Kommandoradsgränssnittet - Knowledge API för tjänst för Kunskapsutveckling
titlesuffix: Azure Cognitive Services
description: Använda kommandoradsgränssnittet för att skapa indexet och grammatik filer från strukturerade data och sedan distribuera dem som webbtjänster.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/24/2016
ms.author: paulhsu
ms.openlocfilehash: 018552982a8ece3bbbaea2d60e2a6e64f681f822
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60815135"
---
# <a name="command-line-interface"></a>Kommandoradsgränssnitt

Kommandoradsgränssnitt för Knowledge utforskning Service (KES) ger möjlighet att skapa indexet och grammatik filer från strukturerade data och distribuera dem som webbtjänster.  Den använder den allmänna syntaxen: `kes.exe <command> <required_args> [<optional_args>]`.  Du kan köra `kes.exe` utan argument för att visa en lista över kommandon, eller `kes.exe <command>` att visa en lista över argument som är tillgängliga för det angivna kommandot.  Nedan visas en lista över tillgängliga kommandon:

* build_index
* build_grammar
* host_service
* deploy_service
* describe_index
* describe_grammar

<a name="build_index-command"></a>

## <a name="buildindex-command"></a>build_index Command

Den **build_index** kommandot skapas en binär indexeringsfil från en definitionsfil för schema och en datafil med objekt som ska indexeras.  Den resulterande indexfilen kan användas för att utvärdera strukturerade frågeuttryck eller att generera tolkningar av frågor med naturligt språk i samband med en kompilerad grammatikfil.

`kes.exe build_index <schemaFile> <dataFile> <indexFile> [options]`

| Parameter      | Beskrivning               |
|----------------|---------------------------|
| `<schemaFile>` | Inmatningsschemat sökväg |
| `<dataFile>`   | Indata-sökväg   |
| `<indexFile>`  | Sökväg för utdata-index |
| `--description <description>` | Beskrivningssträngen |
| `--remote <vmSize>`           | Storleken för virtuell dator för fjärranslutna version |

Dessa filer kan anges med lokala sökvägar eller URL-sökvägar till Azure BLOB.  Schemafilen beskriver strukturen för de objekt som indexeras samt åtgärderna som stöds (se [schemaformat](SchemaFormat.md)).  Datafilen räknar upp de objekt och attributvärden som ska indexeras (se [dataformat](DataFormat.md)).  När den har byggts innehåller index utdatafilen en komprimerad representation av de indata som stöder de önskade åtgärderna.  

Beskrivningssträng kan anges om du vill att senare identifiera en binär index med hjälp av den **describe_index** kommando.  

Som standard har skapat indexet på den lokala datorn.  Lokala versioner är begränsade till filer som innehåller upp till 10 000 objekt utanför Azure-miljön.  När--remote flagga har angetts, indexet skapas på ett tillfälligt skapade Azure-VM för den angivna storleken.  På så sätt kan stora index skapas effektivt med Azure-datorer med mer minne.  För att undvika sidindelning som saktar ned skapandeprocessen, bör du använda en virtuell dator med 3 gånger mängden RAM-minne som filstorlek indata.  En lista över tillgängliga storlekar på virtuella datorer finns i [storlekar på virtuella datorer](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

> [!TIP] 
> Presort objekten i datafilen genom att minska sannolikheten för snabbare versioner.

<a name="build_grammar-command"></a>

## <a name="buildgrammar-command"></a>build_grammar kommando

Den **build_grammar** kommandot kompilerar en grammatik som angetts i XML-filen till en binär grammatikfil.  Den resulterande grammatik-filen kan användas tillsammans med en indexfil för att generera tolkningar av frågor med naturligt språk.

`kes.exe build_grammar <xmlFile> <grammarFile>`

| Parameter       | Beskrivning               |
|-----------------|---------------------------|
| `<xmlFile>`     | XML-grammatik-specifikationen indatasökvägen |
| `<grammarFile>` | Utgående kompilerade grammatik sökväg         |

Dessa filer kan anges med lokala sökvägar eller URL-sökvägar till Azure BLOB.  Grammatik-specifikationen beskriver uppsättningen viktad naturligt språk uttryck och deras semantiska tolkningar (se [Grammatikformat](GrammarFormat.md)).  När den har byggts innehåller grammatik utdatafilen en binär representation av grammatik-specifikationen för att aktivera snabb avkodning.

<a name="host_service-command"/>

## <a name="hostservice-command"></a>host_service Command

Den **host_service** kommando har en instans av tjänsten KES på den lokala datorn.

`kes.exe host_service <grammarFile> <indexFile> [options]`

| Parameter       | Beskrivning                |
|-----------------|----------------------------|
| `<grammarFile>` | Inkommande binära grammatik sökväg         |
| `<indexFile>`   | Inkommande binära index sökväg           |
| `--port <port>` | Lokala portnummer.  Standard: 8000 |

Dessa filer kan anges med lokala sökvägar eller URL-sökvägar till Azure BLOB.  En webbtjänst som kommer att finnas på http://localhost:&lt; porten&gt;/.  Se [Web API: erna](WebAPI.md) för en lista med åtgärder som stöds.

Utanför Azure-miljö, lokalt värdbaserade tjänster är begränsade till index filer upp till 1 MB i storlek, 10 förfrågningar per sekund och 1000 totala anrop.  Om du vill lösa dessa begränsningar, kör **host_service** inuti en Azure-dator, eller distribuera till en Azure-molnet tjänsten med hjälp av **deploy_service**.

<a name="deploy_service-command"/>

## <a name="deployservice-command"></a>deploy_service Command

Den **deploy_service** kommandot distribuerar en instans av tjänsten KES till en Azure-molntjänst.

`kes.exe deploy_service <grammarFile> <indexFile> <serviceName> <vmSize>[options]`

| Parameter       | Beskrivning                  |
|-----------------|------------------------------|
| `<grammarFile>` | Inkommande binära grammatik sökväg           |
| `<indexFile>`   | Inkommande binära index sökväg             |
| `<serviceName>` | Namnet på målmolntjänst |
| `<vmSize>`      | Storleken på molntjänst för virtuell dator     |
| `--slot <slot>` | Molntjänstplats: ”mellanlagring” (standard), ”produktion” |

Dessa filer kan anges med lokala sökvägar eller URL-sökvägar till Azure BLOB.  Tjänstnamnet anger förkonfigurerade Azure-molntjänst (se [hur du skapar och distribuerar en tjänst i molnet](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md)).  Kommandot distribuerar automatiskt KES tjänsten till den angivna Azure-molntjänst, med hjälp av virtuella datorer i den angivna storleken.  Om du vill undvika sidindelning som avsevärt minskar prestanda, bör du använda en virtuell dator med 1 GB mer RAM-minne än den inkommande indexstorleken.  En lista över tillgängliga VM-storlekar finns i [storlekar för Cloud Services](../../../articles/cloud-services/cloud-services-sizes-specs.md).

Som standard tjänsten har distribuerats till mellanlagringsmiljö, du kan också åsidosatt via parametern--fack.  Se [Web API: erna](WebAPI.md) för en lista med åtgärder som stöds.

<a name="describe_index-command"/>

## <a name="describeindex-command"></a>describe_index kommando

Den **describe_index** kommando visar information om ett indexfil, inklusive ett schema och en beskrivning.

`kes.exe describe_index <indexFile>`

| Parameter     | Beskrivning      |
|---------------|------------------|
| `<indexFile>` | Inkommande index sökväg |

Den här filen kan anges med en lokal filsökväg eller URL-sökvägen till en Azure-blob.  Beskrivningssträngen utdata kan anges med hjälp av--beskrivningsparameter av den **build_index** kommando.

<a name="describe_grammar-command"/>

## <a name="describegrammar-command"></a>describe_grammar kommando

Den **describe_grammar** kommando visar den ursprungliga grammatik-specifikationen som används för att skapa den binära grammatiken.

`kes.exe describe_grammar <grammarFile>`

| Parameter       | Beskrivning      |
|-----------------|------------------|
| `<grammarFile>` | Inkommande grammatik sökväg |

Den här filen kan anges med en lokal filsökväg eller URL-sökvägen till en Azure-blob.

