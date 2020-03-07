---
title: Kommando rads gränssnitt – Tjänst för kunskapsutveckling-API
titlesuffix: Azure Cognitive Services
description: Använd kommando rads gränssnittet för att bygga index-och grammatikalternativ från strukturerade data och distribuera dem som webb tjänster.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/24/2016
ms.author: paulhsu
ms.openlocfilehash: 018552982a8ece3bbbaea2d60e2a6e64f681f822
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78385634"
---
# <a name="command-line-interface"></a>Kommando rads gränssnitt

Med kommando rads gränssnittet Tjänst för kunskapsutveckling (KES) kan du bygga index-och grammatikalternativ från strukturerade data och distribuera dem som webb tjänster.  Den allmänna syntaxen används: `kes.exe <command> <required_args> [<optional_args>]`.  Du kan köra `kes.exe` utan argument för att visa en lista över kommandon eller `kes.exe <command>` för att visa en lista över argument som är tillgängliga för det angivna kommandot.  Nedan visas en lista över tillgängliga kommandon:

* build_index
* build_grammar
* host_service
* deploy_service
* describe_index
* describe_grammar

<a name="build_index-command"></a>

## <a name="build_index-command"></a>build_index kommando

Kommandot **build_index** skapar en binär indexfil från en schema definitions fil och en data fil med objekt som ska indexeras.  Den resulterande index filen kan användas för att utvärdera strukturerade frågeuttryck eller för att generera tolkningar av frågor i naturliga språk tillsammans med en kompilerad grammatikkontroll.

`kes.exe build_index <schemaFile> <dataFile> <indexFile> [options]`

| Parameter      | Beskrivning               |
|----------------|---------------------------|
| `<schemaFile>` | Sökväg för inmatat schema |
| `<dataFile>`   | Sökväg för indata   |
| `<indexFile>`  | Sökväg för utgående index |
| `--description <description>` | Beskrivnings sträng |
| `--remote <vmSize>`           | Storlek på virtuell dator för fjärran sluten build |

De här filerna kan anges av lokala fil Sök vägar eller URL-sökvägar till Azure-blobbar.  Schema filen beskriver strukturen för de objekt som indexeras samt vilka åtgärder som stöds (se [schema format](SchemaFormat.md)).  Data filen räknar upp de objekt-och attributvärden som ska indexeras (se [data format](DataFormat.md)).  När bygget lyckas innehåller index filen för utdata en komprimerad representation av de indata som stöder önskade åtgärder.  

En beskrivnings sträng kan eventuellt anges för att senare identifiera ett binärt index med kommandot **describe_index** .  

Som standard skapas indexet på den lokala datorn.  Utanför Azure-miljön är lokala versioner begränsade till datafiler som innehåller upp till 10 000 objekt.  När flaggan--Remote anges, skapas indexet på en tillfälligt skapad virtuell Azure-dator med den angivna storleken.  Detta gör att stora index kan skapas effektivt med virtuella Azure-datorer med mer minne.  För att undvika växling som saktar ned Bygg processen, rekommenderar vi att du använder en virtuell dator med 3 gånger mängden RAM-minne som indatafilens storlek.  En lista över tillgängliga storlekar på virtuella datorer finns i [storlekar på virtuella datorer](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

> [!TIP] 
> För snabbare versioner kan du försortera objekten i data filen genom att minska sannolikheten.

<a name="build_grammar-command"></a>

## <a name="build_grammar-command"></a>build_grammar kommando

Kommandot **build_grammar** kompilerar en grammatik som anges i XML-format till en binär grammatikkontroll.  Den resulterande grammatikkontrollen kan användas tillsammans med en index fil för att generera tolkningar av frågor med naturligt språk.

`kes.exe build_grammar <xmlFile> <grammarFile>`

| Parameter       | Beskrivning               |
|-----------------|---------------------------|
| `<xmlFile>`     | Sökväg till kod för indata-grammatik |
| `<grammarFile>` | Autoutdata-kompilerad grammatisk sökväg         |

De här filerna kan anges av lokala fil Sök vägar eller URL-sökvägar till Azure-blobbar.  I grammatikkontrollen beskrivs uppsättningen med vägda naturliga språk uttryck och deras semantiska tolkningar (se [Grammar format](GrammarFormat.md)).  När bygget lyckas innehåller autoutdata-filen en binär representation av grammatikkontrollen för att aktivera snabb avkodning.

<a name="host_service-command"/>

## <a name="host_service-command"></a>host_service kommando

**Host_service** kommandot är värd för en instans av KES-tjänsten på den lokala datorn.

`kes.exe host_service <grammarFile> <indexFile> [options]`

| Parameter       | Beskrivning                |
|-----------------|----------------------------|
| `<grammarFile>` | Sökväg för binär grammatik         |
| `<indexFile>`   | Sökväg för binär index för indatamängd           |
| `--port <port>` | Lokalt port nummer.  Standard: 8000 |

De här filerna kan anges av lokala fil Sök vägar eller URL-sökvägar till Azure-blobbar.  En webb tjänst kommer att finnas på http://localhost:&lt;p ort&gt;/.  En lista över åtgärder som stöds finns i [webb-API: er](WebAPI.md) .

Utanför Azure-miljön är lokalt värdbaserade tjänster begränsade till att indexera filer på upp till 1 MB i storlek, 10 begär Anden per sekund och 1000 totala anrop.  Du kan lösa dessa begränsningar genom att köra **host_service** i en virtuell Azure-dator eller distribuera till en Azure-molnbaserad tjänst med **deploy_service**.

<a name="deploy_service-command"/>

## <a name="deploy_service-command"></a>deploy_service kommando

Kommandot **deploy_service** distribuerar en instans av KES-tjänsten till en Azure-molnbaserad tjänst.

`kes.exe deploy_service <grammarFile> <indexFile> <serviceName> <vmSize>[options]`

| Parameter       | Beskrivning                  |
|-----------------|------------------------------|
| `<grammarFile>` | Sökväg för binär grammatik           |
| `<indexFile>`   | Sökväg för binär index för indatamängd             |
| `<serviceName>` | Namn på mål moln tjänst |
| `<vmSize>`      | Storlek på virtuell dator i moln tjänsten     |
| `--slot <slot>` | Moln tjänst fack: "mellanlagring" (standard), "produktion" |

De här filerna kan anges av lokala fil Sök vägar eller URL-sökvägar till Azure-blobbar.  Tjänst namn anger en förkonfigurerad Azure-molnbaserad tjänst (se [skapa och distribuera en moln tjänst](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md)).  Kommandot distribuerar automatiskt KES-tjänsten till den angivna Azure-moln tjänsten med virtuella datorer med den angivna storleken.  För att undvika växling som avsevärt minskar prestandan rekommenderar vi att du använder en virtuell dator med 1 GB mer RAM-minne än storleken på index filen för indata.  En lista över tillgängliga VM-storlekar finns i [storlekar för Cloud Services](../../../articles/cloud-services/cloud-services-sizes-specs.md).

Som standard distribueras tjänsten till mellanlagrings miljön, alternativt åsidosätts via parametern--socket.  En lista över åtgärder som stöds finns i [webb-API: er](WebAPI.md) .

<a name="describe_index-command"/>

## <a name="describe_index-command"></a>describe_index kommando

Kommandot **describe_index** matar ut information om en index fil, inklusive schema och beskrivning.

`kes.exe describe_index <indexFile>`

| Parameter     | Beskrivning      |
|---------------|------------------|
| `<indexFile>` | Sökväg för indatamängds index |

Den här filen kan anges av en lokal fil Sök väg eller en URL-sökväg till en Azure-blob.  Den utgående beskrivnings strängen kan anges med parametern--Description i kommandot **build_index** .

<a name="describe_grammar-command"/>

## <a name="describe_grammar-command"></a>describe_grammar kommando

Kommandot **describe_grammar** matar ut den ursprungliga grammatik specifikationen som används för att bygga den binära grammatiken.

`kes.exe describe_grammar <grammarFile>`

| Parameter       | Beskrivning      |
|-----------------|------------------|
| `<grammarFile>` | Sökväg för inmatad grammatik |

Den här filen kan anges av en lokal fil Sök väg eller en URL-sökväg till en Azure-blob.

