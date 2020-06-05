---
title: Format för gemensam data modell
description: Transformera data med hjälp av common data Model metadata system
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: daperlov
ms.openlocfilehash: 2faa14ef7724bb9cccfe425569539f5ef0621a28
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2020
ms.locfileid: "84435396"
---
# <a name="common-data-model-format-in-azure-data-factory"></a>Gemensamt data modell format i Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

COMMON data service-systemet (common data Model) gör det möjligt för data och dess innebörd att enkelt delas mellan program och affärs processer. Mer information finns i Översikt över [common data Model](https://docs.microsoft.com/common-data-model/) .

I Azure Data Factory kan användare omvandla till och från common data service entiteter som lagras i [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) (ADLS Gen2) med hjälp av mappnings data flöden.

## <a name="mapping-data-flow-properties"></a>Mappa data flödes egenskaper

Den gemensamma data modellen är tillgänglig som en [infogad data uppsättning](data-flow-source.md#inline-datasets) i mappa data flöden som både en källa och en mottagare.

### <a name="source-properties"></a>Käll egenskaper

I tabellen nedan visas de egenskaper som stöds av en common data service-källa.

| Name | Beskrivning | Obligatorisk | Tillåtna värden | Skript egenskap för data flöde |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Format | Är alltid`cdm` | ja | `cdm` | format |
| Format för metadata | Där enhets referensen till data finns. Om du använder common data service version 1,0 väljer du manifest. Om du använder en common data service-version före 1,0 väljer du Model. JSON. | Ja | `'manifest'` eller `'model'` | manifestType |
| Rot plats: behållare | Behållarens namn på mappen common data service | ja | Sträng | Fil Systems |
| Rot plats: mappsökväg | Rotmappens plats för mappen common data service | ja | Sträng | folderPath |
| Manifest fil: enhets Sök väg | Mappsökväg för entiteten i rotmappen | nej | Sträng | entityPath |
| Manifest fil: manifest namn | Manifest filens namn  | Ja, om du använder manifest | Sträng | manifestName |
| Filtrera efter senast ändrad | Välj att filtrera filer baserat på när de senast ändrades | nej | Tidsstämpel | modifiedAfter <br> modifiedBefore | 
| Länkad schema tjänst | Den länkade tjänsten där sökkorpus finns | Ja, om du använder manifest | `'adlsgen2'` eller `'github'` | corpusStore | 
| Enhets referens behållare | Containerns sökkorpus finns i | Ja, om du använder manifest och sökkorpus i ADLS Gen2 | Sträng | adlsgen2_fileSystem |
| Enhets referens databas | GitHub-lagringsplatsnamn | Ja, om du använder manifest och sökkorpus i GitHub | Sträng | github_repository |
| Enhets referens gren | Gren för GitHub-lagringsplats | Ja, om du använder manifest och sökkorpus i GitHub | Sträng |  github_branch |
| Sökkorpus-mapp | sökkorpus rot plats | Ja, om du använder manifest | Sträng | corpusPath |
| Sökkorpus-entitet | Sökväg till entitetsreferens | ja | Sträng | entitetsrelation |
| Det gick inte att hitta några filer | Om värdet är true uppstår ett fel inte om inga filer hittas | nej | `true` eller `false` | ignoreNoFilesFound |

![COMMON data service-källa](media/format-common-data-model/data-flow-source.png)

### <a name="sink-properties"></a>Egenskaper för mottagare

I tabellen nedan visas de egenskaper som stöds av en common data service-mottagare.

| Name | Beskrivning | Obligatorisk | Tillåtna värden | Skript egenskap för data flöde |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Format | Är alltid`cdm` | ja | `cdm` | format |
| Rot plats: behållare | Behållarens namn på mappen common data service | ja | Sträng | Fil Systems |
| Rot plats: mappsökväg | Rotmappens plats för mappen common data service | ja | Sträng | folderPath |
| Manifest fil: enhets Sök väg | Mappsökväg för entiteten i rotmappen | nej | Sträng | entityPath |
| Manifest fil: manifest namn | Manifest filens namn  | ja | Sträng | manifestName |
| Länkad schema tjänst | Den länkade tjänsten där sökkorpus finns | ja | `'adlsgen2'` eller `'github'` | corpusStore | 
| Enhets referens behållare | Containerns sökkorpus finns i | Ja, om sökkorpus i ADLS Gen2 | Sträng | adlsgen2_fileSystem |
| Enhets referens databas | GitHub-lagringsplatsnamn | Ja, om sökkorpus i GitHub | Sträng | github_repository |
| Enhets referens gren | Gren för GitHub-lagringsplats | Ja, om sökkorpus i GitHub | Sträng |  github_branch |
| Sökkorpus-mapp | sökkorpus rot plats | ja | Sträng | corpusPath |
| Sökkorpus-entitet | Sökväg till entitetsreferens | ja | Sträng | entitetsrelation |
| Sökväg till partition | Plats där partitionen skrivs | nej | Sträng | partitionPath |
| Rensa mappen | Om målmappen rensas innan den skrivs | nej | `true` eller `false` | truncate |
| Format typ | Välj att ange Parquet-format | nej | `parquet`om det anges | under format |
| Kolumn avgränsare | Om du skriver till DelimitedText, så här begränsar du kolumner | Ja, om du skriver till DelimitedText | Sträng | columnDelimiter |
| Första raden som rubrik | Om du använder DelimitedText, om kolumn namnen läggs till som en rubrik | nej | `true` eller `false` | columnNamesAsHeader |

![COMMON data service-källa](media/format-common-data-model/data-flow-sink.png)

## <a name="next-steps"></a>Nästa steg

Skapa en [käll omvandling](data-flow-source.md) i mappnings data flödet.