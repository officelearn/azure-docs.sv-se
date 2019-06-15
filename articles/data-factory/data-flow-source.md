---
title: Konfigurera en källa omvandling i funktionen för mappning av dataflöde i Azure Data Factory
description: Lär dig hur du ställer in en källa omvandling i mappning dataflöde.
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 5b53819c1d30f6cd62c5941d4b44d70a4996daad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67117881"
---
# <a name="source-transformation-for-mapping-data-flow"></a>Transformering av källa för mappning av dataflöde 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

En käll-omvandling konfigurerar din datakälla för dataflödet. Ett dataflöde kan innehålla fler än en transformering av källa. När du utformar data flödar alltid börja med en transformering av källa.

Varje dataflöde kräver minst ett omvandling. Lägg till så många källor som behövs för att slutföra din Datatransformationer. Du kan ansluta till dessa källor tillsammans med en join-transformation eller en union omvandling.

> [!NOTE]
> När du felsöker ditt dataflöde, läses data från källan med hjälp av inställningen sampling eller debug källa gränser. Om du vill skriva data till en mottagare, måste du köra ditt dataflöde från en pipeline Data flödesaktivitet. 

![Käll-omvandling alternativ på fliken Inställningar för datakälla](media/data-flow/source.png "källa")

Koppla din dataflöde källa omvandling till exakt en Data Factory-datauppsättningen. Datauppsättningen definierar formen och platsen för de data du vill skriva till eller läsa från. Du kan använda jokertecken och filen listor i din källan för att arbeta med fler än en fil i taget.

## <a name="data-flow-staging-areas"></a>Data Flow mellanlagringsområden

Dataflödet som fungerar med *mellanlagring* datauppsättningar som finns i Azure. Använd dessa datauppsättningar för att mellanlagra när du omvandlar dina data. 

Data Factory har åtkomst till nästan 80 ursprungliga anslutningsprogram. För att inkludera data från de andra källorna i ditt dataflöde, använder du verktyget Kopieringsaktivitet för att mellanlagra data i någon av mellanlagringsområden för dataflödet datauppsättning.

## <a name="options"></a>Alternativ

Välj schema- och sampling alternativ för dina data.

### <a name="allow-schema-drift"></a>Tillåt schemat drift
Välj **Tillåt schemat drift** om källkolumner ändras ofta. Den här inställningen kan alla inkommande källfält kan passera transformeringar som mottagaren.

### <a name="validate-schema"></a>Validera schemat

Om den inkommande versionen av källdata inte matchar definierat schema, misslyckas dataflödet att köra.

![Inställningar för offentlig datakälla, som visar alternativen för att verifiera schemat och Tillåt schemat drift Sampling](media/data-flow/source1.png "offentliga källa 1")

### <a name="sample-the-data"></a>Exempel på data
Aktivera **Sampling** att begränsa antalet rader från källan. Använd den här inställningen när du testar eller samla in data från källan för felsökning.

## <a name="define-schema"></a>Definiera schema

När källfilerna inte är starkt typbestämd (till exempel, flata filer i stället Parquet-filer), kan du definiera datatyperna för varje fält här omvandling källa.  

![Datakällans omvandling inställningar på fliken Definiera schemat](media/data-flow/source2.png "datakällan 2")

Du kan senare ändra kolumnnamnen i en select transformation. Använd en härledd kolumn transformation för att ändra datatyperna. Du kan ändra datatyper i en senare väljer transformation för starkt typifierad källor. 

![Datatyper i en select transformation](media/data-flow/source003.png "datatyper")

### <a name="optimize-the-source-transformation"></a>Optimera käll-transformering

På den **optimera** fliken för käll-omvandling visas en **källa** partitionstyp. Det här alternativet är tillgängligt endast när källan är Azure SQL Database. Det beror på att Data Factory försöker upprätta anslutningar parallellt för att köra stora frågor mot din SQL Database-källan.

![Datakällan partitionsinställningar](media/data-flow/sourcepart3.png "partitionering")

Partitioner är användbara för stora frågor när du behöver att partitionera data på din SQL Database-källan. Du kan basera din partition på en kolumn eller en fråga.

### <a name="use-a-column-to-partition-data"></a>Använda en kolumn att partitionera data

Välj en kolumn till partition på din källtabellen. Också ange antalet partitioner.

### <a name="use-a-query-to-partition-data"></a>Använd en fråga för att partitionera data

Du kan välja att partitionera anslutningar baserat på en fråga. Ange innehållet i en WHERE-predikat. Till exempel ange år > 1980.

## <a name="source-file-management"></a>Hantering av källa

Välj inställningar för att hantera filer i källan. 

![Nya inställningar för datakälla](media/data-flow/source2.png "nya inställningar")

* **Jokersökväg**: Välj ett antal filer som matchar ett mönster från din källmapp. Den här inställningen åsidosätter alla filer i definitionen av datauppsättningen.

Jokertecken exempel:

* ```*``` Representerar en uppsättning tecken
* ```**``` Representerar rekursiv directory kapsling
* ```?``` Ersätter ett tecken
* ```[]``` Matchar ett av flera tecken i hakparenteserna

* ```/data/sales/**/*.csv``` Hämtar alla csv-filer under /data/sales
* ```/data/sales/20??/**``` Hämtar alla filer på 20 talet
* ```/data/sales/2004/*/12/[XY]1?.csv``` Hämtar alla csv-filer i 2004 i December som börjar med X eller Y prefixet 2 siffror

Behållaren måste anges i datauppsättningen. Din jokersökväg måste därför även innehålla din mappsökväg från rotmappen.

* **Lista över filer**: Det här är en uppsättning. Skapa en textfil som innehåller en lista över filer som relativ sökväg ska bearbetas. Peka på den här filen.
* **Kolumnen för att lagra filnamn**: Store namnet på källfilen i en kolumn i dina data. Ange ett nytt namn för att lagra filen namnsträngen.
* **När du har slutfört**: Välja att göra något med källfilen när data flödeskörningar, ta bort källfilen eller flytta källfilen. Sökvägar för flytten är relativa.

Om du vill flytta källfiler för en annan plats efter bearbetning av du först markera ”flytta” för filen igen. Slutligen ange katalogen ”från”. Om du inte använder några jokertecken för din sökväg kommer ”från”-inställningen är samma mapp som din källmappen.

Om du har en källsökväg jokertecken, t.ex.:

```/data/sales/20??/**/*.csv```

Du kan ange ”från” som

```/data/sales```

Och ”till” som

```/backup/priorSales```

I det här fallet flyttas alla underkataloger på /data/sales som har ursprung i förhållande till /backup/priorSales.

### <a name="sql-datasets"></a>SQL-datauppsättningar

Om källan finns i SQL Database eller SQL Data Warehouse, har du ytterligare alternativ för hantering av källa.

* **Fråga**: Ange en SQL-fråga för källan. Den här inställningen åsidosätter en tabell som du har valt i datauppsättningen. Observera att **Order By** satser stöds inte här, men du kan ange en fullständig SELECT FROM-instruktion. Du kan också använda användardefinierade Tabellfunktioner. **Välj * från udfGetData()** är en UDF i SQL som returnerar en tabell. Den här frågan kommer att generera en källtabellen som du kan använda i ditt dataflöde.
* **Batchstorlek**: Ange en batchstorlek för att dela upp stora mängder data till läsningar.

> [!NOTE]
> Filåtgärder körs bara när du startar dataflödet från en pipeline som körs (en pipeline debug eller körningen kör) och som använder aktiviteten kör dataflödet i en pipeline. Filåtgärder *inte* kör i felsökningsläge dataflöde.

### <a name="projection"></a>Projektion

T.ex. scheman i datauppsättningar definierar projektionen i en källa datakolumner, typer och format från datakällan. 

![Inställningarna på fliken projektion](media/data-flow/source3.png "projektion")

Om din textfil har inget definierat schema väljer **identifiera datatyp** så att Data Factory kommer exempel och härleder datatyperna. Välj **definiera standardformatet** Autodetect standarddata format. 

Du kan ändra datatyperna för kolumnen i en senare härledd kolumn omvandling. Använda en select transformation för att ändra namn på kolumnerna.

![Inställningar för standard-dataformat](media/data-flow/source2.png "standardformat")

## <a name="next-steps"></a>Nästa steg

Börja bygga en [härledd kolumn omvandling](data-flow-derived-column.md) och en [Välj omvandling](data-flow-select.md).
