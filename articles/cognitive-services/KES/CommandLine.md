---
title: Knowledge utforskning Service-kommandoradsgränssnittet | Microsoft Docs
description: Använd KES kommandoradsgränssnitt för att skapa index och grammatik filer från strukturerade data och distribuera dem som web services kognitiva Microsoft-tjänster.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/24/2016
ms.author: paulhsu
ms.openlocfilehash: ffa42ac73b42a8271004d2d45d7a80f3307ef059
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351801"
---
# <a name="command-line-interface"></a>Kommandoradsgränssnitt
KES kommandoradsgränssnittet ger möjlighet att skapa index och grammatik filer från strukturerade data och distribuera dem som webbtjänster.  Den använder den allmänna syntaxen: `kes.exe <command> <required_args> [<optional_args>]`.  Du kan köra `kes.exe` utan argument för att visa en lista med kommandon, eller `kes.exe <command>` att visa en lista över de argument som är tillgängliga för det angivna kommandot.  Nedan visas en lista över tillgängliga kommandon:
* build_index
* build_grammar
* host_service
* deploy_service
* describe_index
* describe_grammar

<a name="build_index-command"></a>
## <a name="buildindex-command"></a>build_index kommando
Den **build_index** kommando skapar en binär indexfil från en definition av schemafilen och en datafil för objekt som indexeras.  Resulterande indexfilen kan användas för att utvärdera strukturerade frågeuttryck eller generera tolkning av naturligt språk tillsammans med en kompilerad grammatikfil.

`kes.exe build_index <schemaFile> <dataFile> <indexFile> [options]`

| Parameter      | Beskrivning               |
|----------------|---------------------------|
| `<schemaFile>` | Ingående schema sökväg |
| `<dataFile>`   | Sökväg för indata   |
| `<indexFile>`  | Utdatasökvägen index |
| `--description <description>` | Beskrivningssträngen |
| `--remote <vmSize>`           | Storlek för den virtuella datorn för fjärr-version |

Dessa filer kan anges med lokala sökvägar eller URL-sökvägar till Azure BLOB.  Schemafilen beskriver strukturen för de objekt som indexeras samt åtgärderna som stöds (se [schemaformat](SchemaFormat.md)).  Datafilen räknar upp de objekt och attributvärden med att indexera (se [dataformat](DataFormat.md)).  När den har byggts innehåller index utdatafilen en komprimerad representation av indata som har stöd för de önskade åtgärderna.  

Beskrivningssträng eventuellt kan anges för att senare identifiera en binär index med hjälp av den **describe_index** kommando.  

Som standard har skapat indexet på den lokala datorn.  Lokala versioner är begränsade till datafiler med upp till 10 000 objekt utanför Azure-miljön.  När--fjärr-flaggan har angetts, indexet skapas på ett tillfälligt skapade virtuella Azure-datorn med angiven storlek.  Detta gör att stora index skapas ett effektivt sätt med hjälp av Azure virtuella datorer med mer minne.  Om du vill undvika sidindelning som långsammare skapar rekommenderar vi använder en virtuell dator med 3 gånger mängden RAM-minne som filstorlek indata.  En lista över tillgängliga storlekar på VM, se [storlekar för virtuella datorer](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

> [!TIP] 
> Presort objekten i filen genom att minska sannolikheten för snabbare versioner.

<a name="build_grammar-command"></a>
## <a name="buildgrammar-command"></a>build_grammar kommando
Den **build_grammar** kommandon kompilerar en grammatik som angetts i XML-filen till en binär grammatik-fil.  Den resulterande grammatikfilen kan användas tillsammans med en indexfil för att generera tolkning av naturligt språk.

`kes.exe build_grammar <xmlFile> <grammarFile>`

| Parameter       | Beskrivning               |
|-----------------|---------------------------|
| `<xmlFile>`     | Inkommande XML-grammatik specifikation sökväg |
| `<grammarFile>` | Utdatasökvägen kompilerade grammatik         |

Dessa filer kan anges med lokala sökvägar eller URL-sökvägar till Azure BLOB.  Specifikationen grammatik beskriver uppsättningen viktat naturligt språk uttryck och deras semantiska tolkningar (se [Grammatikformatet](GrammarFormat.md)).  När den har byggts innehåller grammatik utdatafilen en a binär representation av grammatik-specifikationen för att aktivera snabb avkodning.

<a name="host_service-command"/>
## <a name="hostservice-command"></a>host_service kommando
Den **host_service** kommandot värd för en instans av tjänsten KES på den lokala datorn.

`kes.exe host_service <grammarFile> <indexFile> [options]`

| Parameter       | Beskrivning                |
|-----------------|----------------------------|
| `<grammarFile>` | Inkommande binär grammatik sökväg         |
| `<indexFile>`   | Inkommande binär index sökväg           |
| `--port <port>` | Lokala portnumret.  Standard: 8000 |

Dessa filer kan anges med lokala sökvägar eller URL-sökvägar till Azure BLOB.  En webbtjänst kommer att finnas på http://localhost:&lt; porten&gt;/.  Se [webb-API: er](WebAPI.md) för en lista över de åtgärder som stöds.

Miljö, lokalt värdtjänster är begränsade till index-filer utanför Azure upp till 1 MB i storlek, 10 begäranden per sekund och 1000 Totalt antal anrop.  Om du vill lösa dessa begränsningar, kör **host_service** inuti en virtuell dator i Azure, eller distribuera till en Azure cloud service med **deploy_service**.

<a name="deploy_service-command"/>
## <a name="deployservice-command"></a>deploy_service kommando
Den **deploy_service** kommandot distribuerar en instans av tjänsten KES till en Azure-molntjänst.

`kes.exe deploy_service <grammarFile> <indexFile> <serviceName> <vmSize>[options]`

| Parameter       | Beskrivning                  |
|-----------------|------------------------------|
| `<grammarFile>` | Inkommande binär grammatik sökväg           |
| `<indexFile>`   | Inkommande binär index sökväg             |
| `<serviceName>` | Namnet på målet Molntjänsten |
| `<vmSize>`      | Storleken på molntjänst för virtuell dator     |
| `--slot <slot>` | Cloud service fack: ”mellanlagring” (standard), ”produktion” |

Dessa filer kan anges med lokala sökvägar eller URL-sökvägar till Azure BLOB.  Tjänstens namn anger förkonfigurerade Azure-molntjänst (se [hur du skapar och distribuerar en tjänst i molnet](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md)).  Kommandot Distribuera automatiskt tjänsten KES till angivna Azure-Molntjänsten, med hjälp av virtuella datorer i den angivna storleken.  För att undvika sidindelning som avsevärt minskar prestanda bör du använda en virtuell dator med 1 GB mer RAM-minne än filstorleken inkommande index.  En lista över tillgängliga storlekar på VM, se [storlekar för molntjänster](../../../articles/cloud-services/cloud-services-sizes-specs.md).

Som standard tjänsten har distribuerats till mellanlagringsmiljön, eventuellt åsidosatt via parametern--plats.  Se [webb-API: er](WebAPI.md) för en lista över de åtgärder som stöds.

<a name="describe_index-command"/>
## <a name="describeindex-command"></a>describe_index kommando
Den **describe_index** kommandot matar ut information om ett indexfil, inklusive ett schema och en beskrivning.

`kes.exe describe_index <indexFile>`

| Parameter     | Beskrivning      |
|---------------|------------------|
| `<indexFile>` | Inkommande index sökväg |

Den här filen kan anges med en lokal sökväg eller URL-sökvägen till en Azure-blob.  Beskrivningssträngen utdata kan anges med hjälp av--beskrivning parametern till den **build_index** kommando.

<a name="describe_grammar-command"/>
## <a name="describegrammar-command"></a>describe_grammar kommando
Den **describe_grammar** kommando den ursprungliga grammatik-specifikationen som används för att skapa binär grammatik.

`kes.exe describe_grammar <grammarFile>`

| Parameter       | Beskrivning      |
|-----------------|------------------|
| `<grammarFile>` | Inkommande grammatik sökväg |

Den här filen kan anges med en lokal sökväg eller URL-sökvägen till en Azure-blob.

