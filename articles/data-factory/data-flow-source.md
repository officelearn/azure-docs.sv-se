---
title: Käll omvandling i data flöde för mappning – Azure Data Factory | Microsoft Docs
description: Lär dig hur du konfigurerar en käll omvandling i mappnings data flödet.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: fb11b785cecbd021c0b894754e31d226edfe72f2
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73519312"
---
# <a name="source-transformation-for-mapping-data-flow"></a>Käll omvandling för att mappa data flöde 

En käll omvandling konfigurerar data källan för data flödet. När du skapar data flöden kommer ditt första steg alltid att konfigurera en käll omvandling. Om du vill lägga till en källa klickar du på rutan **Lägg till källa** i arbets ytan data flöde.

Varje data flöde kräver minst en käll omvandling, men du kan lägga till så många källor som behövs för att slutföra dina data transformationer. Du kan koppla ihop dessa källor med en JOIN-, lookup-eller union-omvandling.

Varje käll omvandling är associerad med exakt en Data Factory data uppsättning. Data uppsättningen definierar formen och platsen för de data som du vill skriva till eller läsa från. Om du använder en filbaserad data uppsättning kan du använda jokertecken och fil listor i din källa för att arbeta med fler än en fil i taget.

## <a name="supported-connectors-in-mapping-data-flow"></a>Anslutningar som stöds i data flöde för mappning

Genom att mappa data flödet följer du metoden extrahera, läsa in, transformera (ELT) och arbetar med *mellanlagring* av data uppsättningar som är alla i Azure. För närvarande kan följande data uppsättningar användas i en käll omvandling:
    
* Azure Blob Storage (JSON, Avro, text, Parquet)
* Azure Data Lake Storage Gen1 (JSON, Avro, text, Parquet)
* Azure Data Lake Storage Gen2 (JSON, Avro, text, Parquet)
* Azure SQL Data Warehouse
* Azure SQL Database
* Azure CosmosDB

Azure Data Factory har åtkomst till över 80 inbyggda anslutningar. Om du vill ta med data från de andra källorna i ditt data flöde använder du kopierings aktiviteten för att läsa in dessa data till något av de mellanliggande mellanlagrings områdena.

## <a name="source-settings"></a>Källinställningar

När du har lagt till en källa konfigurerar du via fliken **käll inställningar** . Här kan du välja eller skapa den data uppsättning som din käll pekar på. Du kan också välja schema-och samplings alternativ för dina data.

![Fliken käll inställningar](media/data-flow/source1.png "Fliken käll inställningar")

**Schema avvikelse:** [schema avvikelse](concepts-data-flow-schema-drift.md) är data fabriks möjlighet att hantera flexibla scheman i dina data flöden utan att uttryckligen definiera kolumn ändringar.

* Markera kryss rutan **Tillåt schema avvikelse** om käll kolumnerna ska ändras ofta. Med den här inställningen kan alla inkommande källfält flöda genom omvandlingarna till mottagaren.

* Om du väljer **härledda kolumn typer** instrueras Data Factory att identifiera och definiera data typer för varje ny kolumn som identifieras. När den här funktionen är inaktive rad blir alla nedsatta kolumner av typen sträng.

**Verifiera schema:** Om verifiera schema har valts kan data flödet inte köras om inkommande källdata inte matchar det definierade schemat för data uppsättningen.

**Hoppa över linje antal:** Fältet slopa rad antal anger hur många rader som ska ignoreras i början av data uppsättningen.

**Sampling:** Aktivera sampling för att begränsa antalet rader från källan. Använd den här inställningen när du testar eller samplar data från källan för fel sökning.

**Flera rader:** Välj flera rader om käll text filen innehåller sträng värden som sträcker sig över flera rader, d.v.s. newlines inuti ett värde.

Verifiera att källan är korrekt konfigurerad genom att aktivera fel söknings läge och hämta en data förhands granskning. Mer information finns i [fel söknings läge](concepts-data-flow-debug-mode.md).

> [!NOTE]
> När fel söknings läget är aktiverat skriver rad begränsnings konfigurationen i fel söknings inställningarna över insamlings inställningen i källan under för hands versionen av data.

## <a name="file-based-source-options"></a>Filbaserade käll alternativ

Om du använder en filbaserad data uppsättning, till exempel Azure Blob Storage eller Azure Data Lake Storage, kan du med fliken **käll alternativ** hantera hur källan läser filer.

![Käll alternativ](media/data-flow/sourceOPtions1.png "Käll alternativ")

**Sökväg till jokertecken:** Genom att använda jokertecken instrueras ADF att loopa igenom varje matchande mapp och fil i en enda käll omvandling. Detta är ett effektivt sätt att bearbeta flera filer i ett enda flöde. Lägg till flera matchnings mönster för jokertecken med +-tecknet som visas när du hovrar över ditt befintliga mönster i jokertecken.

Från din käll behållare väljer du en serie filer som matchar ett mönster. Det går endast att ange container i data uppsättningen. Sökvägen till jokertecken måste därför även innehålla sökvägen till din mapp från rotmappen.

Jokertecken exempel:

* ```*``` representerar en uppsättning tecken
* ```**``` representerar rekursiv katalog kapsling
* ```?``` ersätter ett Character
* ```[]``` matchar ett tecken i hakparenteserna

* ```/data/sales/**/*.csv``` hämtar alla CSV-filer under/data/Sales
* ```/data/sales/20??/**``` hämtar alla filer i 20-talet
* ```/data/sales/2004/*/12/[XY]1?.csv``` får alla CSV-filer i 2004 i december som börjar med X eller Y som föregås av ett tvåsiffrigt tal

**Partitionens rot Sök väg:** Om du har partitionerade mappar i fil källan med ett ```key=value``` format (t. ex. Year = 2019) kan du tilldela den översta nivån i det partitionens mappträd till ett kolumn namn i data flödes data strömmen.

Ange först ett jokertecken för att inkludera alla sökvägar som är de partitionerade mapparna plus de löv-filer som du vill läsa.

![Inställningar för partitionens källfil](media/data-flow/partfile2.png "Fil inställning för partition")

Använd inställningen för partitionens rot Sök väg för att definiera vad mappstrukturen är. När du visar innehållet i dina data via en data förhands granskning kommer du att se att ADF lägger till de matchade partitioner som finns i alla mappar.

![Partitionens rot Sök väg](media/data-flow/partfile1.png "För hands version av partitionens rot Sök väg")

**Lista över filer:** Detta är en fil uppsättning. Skapa en textfil som innehåller en lista över relativa Sök vägs filer som ska bearbetas. Peka på den här text filen.

**Kolumn att lagra fil namn på:** Lagra namnet på käll filen i en kolumn i dina data. Ange ett nytt kolumn namn här för att lagra fil namn strängen.

**Efter slut för ande:** Välj att inte göra något med käll filen när data flödet körts, ta bort käll filen eller flytta käll filen. Sök vägarna för flytten är relativa.

Om du vill flytta källfilerna till en annan plats efter bearbetning väljer du först flytta för fil åtgärd. Ange sedan "från"-katalogen. Om du inte använder jokertecken för sökvägen, kommer inställningen från att vara samma mapp som källmappen.

Om du har en käll Sök väg med jokertecken så ser din syntax ut så här:

```/data/sales/20??/**/*.csv```

Du kan ange "från" som

```/data/sales```

Och "till" som

```/backup/priorSales```

I det här fallet flyttas alla filer som har ursprung under/data/Sales till/backup/priorSales.

> [!NOTE]
> Fil åtgärder körs bara när du startar data flödet från en pipeline-körning (en pipeline för pipeline-fel eller körning) som använder aktiviteten kör data flöde i en pipeline. Fil åtgärder körs *inte* i fel söknings läge för data flöde.

**Filtrera efter senast ändrad:** Du kan filtrera vilka filer du bearbetar genom att ange ett datum intervall när de senast ändrades. Alla datum-gånger är i UTC-tid. 

### <a name="add-dynamic-content"></a>Lägg till dynamiskt innehåll

Alla käll inställningar kan anges som uttryck med hjälp av [omvandlings uttryckets språk för mappnings data flödet](data-flow-expression-functions.md). Om du vill lägga till dynamiskt innehåll klickar eller hovrar du i fälten i panelen Inställningar. Klicka på hyperlänken för **Lägg till dynamiskt innehåll**. Detta startar uttrycks verktyget där du kan ange värden dynamiskt med uttryck, statiska literala värden eller parametrar.

![Parametrar](media/data-flow/params6.png "Parametrar")

## <a name="sql-source-options"></a>Alternativ för SQL-källa

Om din källa är i SQL Database eller SQL Data Warehouse är ytterligare SQL-/regionsspecifika inställningar tillgängliga på fliken **käll alternativ** . 

**Inmatade:** Välj om du vill peka din källa i en tabell (motsvarande ```Select * from <table-name>```) eller ange en anpassad SQL-fråga.

**Fråga**: om du väljer fråga i fältet inmatat anger du en SQL-fråga för källan. Den här inställningen åsidosätter alla tabeller som du har valt i data uppsättningen. **Order by** -satser stöds inte här, men du kan ange en fullständig Select from-instruktion. Du kan också använda användardefinierade tabell funktioner. **Select * from udfGetData ()** är en UDF i SQL som returnerar en tabell. Med den här frågan skapas en käll tabell som du kan använda i ditt data flöde. Att använda frågor är också ett bra sätt att minska antalet rader för testning eller sökning. Exempel: ```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Batchstorlek**: Ange en batchstorlek för att segmentera stora data till läsningar.

**Isolerings nivå**: standardvärdet för SQL-källor i mappnings data flödet är Read uncommitted. Du kan ändra isolerings nivån här till något av följande värden:
* Läs bekräftad
* Läs ej allokerad
* Upprepnings bar läsning
* Serialisera
* Ingen (ignorera isolerings nivå)

![Isolerings nivå](media/data-flow/isolationlevel.png "Isolerings nivå")

## <a name="projection"></a>Projektion

Som scheman i data uppsättningar definierar projektionen i en källa data kolumnerna, typerna och formaten från käll data. För de flesta typer av data uppsättningar, till exempel SQL och Parquet, åtgärdas projektionen i en källa för att återspegla det schema som definierats i en data uppsättning. När källfilerna inte är starkt skrivna (till exempel enkla CSV-filer i stället för Parquet-filer) kan du definiera data typerna för varje fält i käll omvandlingen.

![Inställningar på fliken projektion](media/data-flow/source3.png "Projektion")

Om text filen inte har något definierat schema väljer du **identifiera data typ** så att Data Factory kan sampla och härleda data typerna. Välj **definiera standardformat** för att automatiskt identifiera standard data formaten. 

Du kan ändra kolumn data typerna i en nedströms härledd kolumn-omvandling. Använd en SELECT-omvandling för att ändra kolumn namnen.

### <a name="import-schema"></a>Importera schema

Data uppsättningar som Avro och CosmosDB som stöder komplexa data strukturer kräver inte att schema definitioner finns i data uppsättningen. Därför kan du klicka på knappen "Importera schema" på fliken projektion för dessa typer av källor.

## <a name="cosmosdb-specific-settings"></a>CosmosDB-inställningar

När du använder CosmosDB som ursprungs typ finns det några alternativ att tänka på:

* Inkludera system kolumner: om du markerar det här kommer ```id```, ```_ts```och andra system kolumner att inkluderas i metadata för data flödet från CosmosDB. När du uppdaterar samlingar är det viktigt att ta med detta så att du kan ta tag i det befintliga rad-ID: t.
* Sid storlek: antalet dokument per sida i frågeresultatet. Standardvärdet är "-1" som använder den dynamiska sidan för tjänsten upp till 1000.
* Data flöde: Ange ett valfritt värde för det antal ru: er som du vill använda för din CosmosDB-samling för varje körning av det här data flödet under Läs åtgärden. Minimum är 400.
* Prioriterade regioner: du kan välja önskade Läs regioner för den här processen.

## <a name="optimize-the-source-transformation"></a>Optimera käll omvandlingen

På fliken **optimera** för käll omvandling kan du se en typ av **Källtyp** . Det här alternativet är bara tillgängligt när din källa är Azure SQL Database. Detta beror på att Data Factory försöker göra anslutningar parallella för att köra stora frågor mot din SQL Database källa.

![Käll partitions inställningar](media/data-flow/sourcepart3.png "Partitionering")

Du behöver inte partitionera data på din SQL Database källa, men partitioner är användbara för stora frågor. Du kan basera partitionen på en kolumn eller en fråga.

### <a name="use-a-column-to-partition-data"></a>Använda en kolumn för att partitionera data

Välj en kolumn att partitionera på i käll tabellen. Ange också antalet partitioner.

### <a name="use-a-query-to-partition-data"></a>Använd en fråga för att partitionera data

Du kan välja att partitionera anslutningarna baserat på en fråga. Ange innehållet i ett WHERE-predikat. Ange till exempel Year > 1980.

Mer information om optimering i data flödet för mappning finns på [fliken optimera](concepts-data-flow-overview.md#optimize).

## <a name="next-steps"></a>Nästa steg

Börja skapa en [omvandling med härledd kolumn](data-flow-derived-column.md) och en [Välj omvandling](data-flow-select.md).
