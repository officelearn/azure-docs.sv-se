---
title: Common Data Model-formatet
description: Transformera data med hjälp av common data Model metadata system
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: daperlov
ms.openlocfilehash: 5e75f2203552a69e50ed16176525429c6c9d8810
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84807807"
---
# <a name="common-data-model-format-in-azure-data-factory"></a>Gemensamt data modell format i Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

COMMON data service-systemet (common data Model) gör det möjligt för data och dess innebörd att enkelt delas mellan program och affärs processer. Mer information finns i Översikt över [common data Model](https://docs.microsoft.com/common-data-model/) .

I Azure Data Factory kan användare omvandla till och från common data service entiteter som lagras i [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) (ADLS Gen2) med hjälp av mappnings data flöden.

> [!NOTE]
> Common data Model (common data service) format Connector för ADF-dataflöden är för närvarande tillgängligt som en offentlig för hands version.

## <a name="mapping-data-flow-properties"></a>Mappa data flödes egenskaper

Den gemensamma data modellen är tillgänglig som en [infogad data uppsättning](data-flow-source.md#inline-datasets) i mappa data flöden som både en källa och en mottagare.

### <a name="source-properties"></a>Käll egenskaper

I tabellen nedan visas de egenskaper som stöds av en common data service-källa. Du kan redigera dessa egenskaper på fliken **käll alternativ** .

| Name | Beskrivning | Obligatorisk | Tillåtna värden | Skript egenskap för data flöde |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Format | Formatet måste vara`cdm` | ja | `cdm` | format |
| Format för metadata | Där enhets referensen till data finns. Om du använder common data service version 1,0 väljer du manifest. Om du använder en common data service-version före 1,0 väljer du model.jspå. | Ja | `'manifest'` eller `'model'` | manifestType |
| Rot plats: behållare | Behållarens namn på mappen common data service | ja | Sträng | Fil Systems |
| Rot plats: mappsökväg | Rotmappens plats för mappen common data service | ja | Sträng | folderPath |
| Manifest fil: enhets Sök väg | Mappsökväg för entiteten i rotmappen | nej | Sträng | entityPath |
| Manifest fil: manifest namn | Manifest filens namn. Standardvärdet är ' default '  | No | Sträng | manifestName |
| Filtrera efter senast ändrad | Välj att filtrera filer baserat på när de senast ändrades | nej | Tidsstämpel | modifiedAfter <br> modifiedBefore | 
| Länkad schema tjänst | Den länkade tjänsten där sökkorpus finns | Ja, om du använder manifest | `'adlsgen2'` eller `'github'` | corpusStore | 
| Enhets referens behållare | Containerns sökkorpus finns i | Ja, om du använder manifest och sökkorpus i ADLS Gen2 | Sträng | adlsgen2_fileSystem |
| Enhets referens databas | GitHub-lagringsplatsnamn | Ja, om du använder manifest och sökkorpus i GitHub | Sträng | github_repository |
| Enhets referens gren | Gren för GitHub-lagringsplats | Ja, om du använder manifest och sökkorpus i GitHub | Sträng |  github_branch |
| Sökkorpus-mapp | sökkorpus rot plats | Ja, om du använder manifest | Sträng | corpusPath |
| Sökkorpus-entitet | Sökväg till entitetsreferens | ja | Sträng | entitetsrelation |
| Det gick inte att hitta några filer | Om värdet är true uppstår ett fel inte om inga filer hittas | nej | `true` eller `false` | ignoreNoFilesFound |

#### <a name="import-schema"></a>Importera schema

COMMON data service är endast tillgänglig som en infogad data uppsättning och har som standard inte ett associerat schema. Hämta kolumnens metadata genom att klicka på knappen **Importera schema** på fliken **projektion** . Detta gör att du kan referera till kolumn namn och data typer som anges av sökkorpus. Om du vill importera schemat måste en [fel söknings session för data flöde](concepts-data-flow-debug-mode.md) vara aktiv.


### <a name="cdm-source-data-flow-script-example"></a>Skript exempel för common data service-käll data flöde

```
source(output(
        ProductSizeId as integer,
        ProductColor as integer,
        CustomerId as string,
        Note as string,
        LastModifiedDate as timestamp
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    manifestType: 'manifest',
    manifestName: 'ProductManifest',
    entityPath: 'Product',
    corpusPath: 'Products',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    folderPath: 'ProductData',
    fileSystem: 'data') ~> CDMSource
```

### <a name="sink-properties"></a>Egenskaper för mottagare

I tabellen nedan visas de egenskaper som stöds av en common data service-mottagare. Du kan redigera dessa egenskaper på fliken **Inställningar** .

| Name | Beskrivning | Obligatorisk | Tillåtna värden | Skript egenskap för data flöde |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Format | Formatet måste vara`cdm` | ja | `cdm` | format |
| Rot plats: behållare | Behållarens namn på mappen common data service | ja | Sträng | Fil Systems |
| Rot plats: mappsökväg | Rotmappens plats för mappen common data service | ja | Sträng | folderPath |
| Manifest fil: enhets Sök väg | Mappsökväg för entiteten i rotmappen | nej | Sträng | entityPath |
| Manifest fil: manifest namn | Manifest filens namn. Standardvärdet är ' default ' | No | Sträng | manifestName |
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

### <a name="cdm-sink-data-flow-script-example"></a>Exempel på data flödes skript för common data service-mottagare

Det associerade data flödes skriptet är:

```
CDMSource sink(allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    entityPath: 'ProductSize',
    manifestName: 'ProductSizeManifest',
    corpusPath: 'Products',
    partitionPath: 'adf',
    folderPath: 'ProductSizeData',
    fileSystem: 'cdm',
    subformat: 'parquet',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    truncate: true,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> CDMSink

```

## <a name="next-steps"></a>Nästa steg

Skapa en [käll omvandling](data-flow-source.md) i mappnings data flödet.
