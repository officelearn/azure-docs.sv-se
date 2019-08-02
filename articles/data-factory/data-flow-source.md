---
title: Konfigurera en käll omvandling i funktionen för att mappa data flöde i Azure Data Factory
description: Lär dig hur du konfigurerar en käll omvandling i mappnings data flödet.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 974ece9cd035ae29ada38f34b7933d86f682194f
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68696223"
---
# <a name="source-transformation-for-mapping-data-flow"></a>Käll omvandling för att mappa data flöde 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

En käll omvandling konfigurerar data källan för data flödet. Ett data flöde kan innehålla mer än en käll omvandling. När du skapar data flöden börjar du alltid med en käll omvandling.

Varje data flöde kräver minst en käll omvandling. Lägg till så många källor som behövs för att slutföra dina data transformationer. Du kan ansluta dessa källor tillsammans med en JOIN-omvandling eller en union-omvandling.

> [!NOTE]
> När du felsöker data flödet läses data från källan genom att använda samplings inställningen eller käll gränserna för fel sökningen. Om du vill skriva data till en mottagare måste du köra ditt data flöde från en data flödes aktivitet för pipeline. 

![Alternativ för omvandling av källa på fliken käll inställningar](media/data-flow/source.png "källa")

Koppla din transformering av data flödes källan med exakt en Data Factory data uppsättning. Data uppsättningen definierar formen och platsen för de data som du vill skriva till eller läsa från. Du kan använda jokertecken och fil listor i din källa för att arbeta med fler än en fil i taget.

Genom att använda ett **mönster** alternativ för jokertecken instruerar ADF att gå igenom varje matchande mapp och fil i en enda käll omvandling. Detta är ett mycket effektivt sätt att bearbeta flera filer i ett enda flöde. Om du vill spåra fil namnet som bearbetas för närvarande anger du ett fält namn för fältet "kolumn att lagra fil namn" i käll alternativ.

> [!NOTE]
> Ange flera matchnings mönster för jokertecken med plus tecknet bredvid ditt befintliga mönster för jokertecken för att lägga till fler regler för jokertecken.

## <a name="data-flow-staging-areas"></a>Mellanlagrings områden för data flöde
Data flödet fungerar med *mellanlagring* av data uppsättningar som är alla i Azure. Använd de här data uppsättningarna för mellanlagring när du ska transformera dina data. 

Data Factory har åtkomst till nästan 80 inbyggda anslutningar. Om du vill ta med data från de andra källorna i ditt data flöde använder du verktyget kopiera aktivitet för att mellanlagra data i någon av data flödets data flödes mellanlagringsplats.

## <a name="options"></a>Alternativ

Välj schema-och samplings alternativ för dina data.

### <a name="schema-drift"></a>Schemadrift
[Schema](concepts-data-flow-schema-drift.md) drift är ADF möjlighet att hantera flexibla scheman i dina data flöden utan att uttryckligen definiera kolumn ändringar.

* Välj **Tillåt schema avvikelse** om käll kolumnerna kommer att ändras ofta. Med den här inställningen kan alla inkommande källfält flöda genom omvandlingarna till mottagaren.

* Om du väljer **härledda kolumn typer** kommer ADF att instruera ADF att definiera data typer för varje ny kolumn som identifieras. När den här funktionen är inaktive rad kommer ADF att anta en sträng.

### <a name="validate-schema"></a>Verifiera schema

Om den inkommande versionen av käll data inte matchar det definierade schemat kan data flödet inte köras.

![Offentliga käll inställningar, som visar alternativen för att verifiera schema, tillåta schema avvikelse och sampling](media/data-flow/source1.png "offentlig källa 1")

### <a name="sample-the-data"></a>Sampla data
Aktivera **sampling** för att begränsa antalet rader från källan. Använd den här inställningen när du testar eller samplar data från källan för fel sökning.

## <a name="define-schema"></a>Definiera schema

När källfilerna inte är starkt skrivna (till exempel flata filer i stället för Parquet-filer) definierar du data typerna för varje fält i käll omvandlingen.  

![Käll omvandlings inställningar på fliken definiera schema](media/data-flow/source2.png "källa 2")

Du kan senare ändra kolumn namnen i en SELECT-omvandling. Använd en omvandling med härledd kolumn för att ändra data typerna. För starkt skrivna källor kan du ändra data typerna i en senare Välj omvandling. 

![Data typer i en SELECT-omvandling](media/data-flow/source003.png "data typer")

### <a name="optimize-the-source-transformation"></a>Optimera käll omvandlingen

På fliken **optimera** för käll omvandling kan du se en typ av **Källtyp** . Det här alternativet är bara tillgängligt när din källa är Azure SQL Database. Detta beror på att Data Factory försöker göra anslutningar parallella för att köra stora frågor mot din SQL Database källa.

![Käll partitions inställningar](media/data-flow/sourcepart3.png "partitionering")

Du behöver inte partitionera data på din SQL Database källa, men partitioner är användbara för stora frågor. Du kan basera partitionen på en kolumn eller en fråga.

### <a name="use-a-column-to-partition-data"></a>Använda en kolumn för att partitionera data

Välj en kolumn att partitionera på i käll tabellen. Ange också antalet partitioner.

### <a name="use-a-query-to-partition-data"></a>Använd en fråga för att partitionera data

Du kan välja att partitionera anslutningarna baserat på en fråga. Ange bara innehållet i ett WHERE-predikat. Ange till exempel Year > 1980.

## <a name="source-file-management"></a>Käll fils hantering

Välj inställningar för att hantera filer i din källa. 

![Nya käll inställningar](media/data-flow/source2.png "Nya inställningar")

* **Sökväg till jokertecken**: Från din käll behållare väljer du en serie filer som matchar ett mönster. Den här inställningen åsidosätter en fil i din data uppsättnings definition.

Jokertecken exempel:

* ```*```Representerar en uppsättning tecken
* ```**```Representerar rekursiv katalog kapsling
* ```?```Ersätter ett Character
* ```[]```Matchar ett eller flera tecken inom hakparenteserna

* ```/data/sales/**/*.csv```Hämtar alla CSV-filer under/data/Sales
* ```/data/sales/20??/**```Hämtar alla filer i 20-talet
* ```/data/sales/2004/*/12/[XY]1?.csv```Hämtar alla CSV-filer i 2004 i december från och med X eller Y som föregås av ett tvåsiffrigt tal

Container måste anges i data uppsättningen. Sökvägen till jokertecken måste därför även innehålla sökvägen till din mapp från rotmappen.

* **Partitionens rot Sök väg**: Om du har partitionerade mappar i fil källan för ett ```key=value``` format (t. ex. Year = 2019) kan du be ADF att tilldela den översta nivån i det partitionens mappträd till ett kolumn namn i data flödets data ström.

Ange först ett jokertecken för att inkludera alla sökvägar som är de partitionerade mapparna plus de löv-filer som du vill läsa.

![Inställningar för partitionens källfil](media/data-flow/partfile2.png "Fil inställning för partition")

Använd nu inställningen för partitionens rot Sök väg för att meddela ADF vad är den översta nivån i mappstrukturen. När du nu visar innehållet i dina data kommer du att se att ADF lägger till de matchade partitioner som finns i alla mappar.

![Partitionens rot Sök väg] För (media/data-flow/partfile1.png "hands version av partitionens rot Sök väg")

* **Lista över filer**: Detta är en fil uppsättning. Skapa en textfil som innehåller en lista över relativa Sök vägs filer som ska bearbetas. Peka på den här text filen.
* **Kolumn att lagra fil namn på**: Lagra namnet på käll filen i en kolumn i dina data. Ange ett nytt namn här för att lagra fil namn strängen.
* **Efter slut för ande**: Välj att inte göra något med käll filen när data flödet körts, ta bort käll filen eller flytta käll filen. Sök vägarna för flytten är relativa.

Om du vill flytta källfilerna till en annan plats efter bearbetning väljer du först flytta för fil åtgärd. Ange sedan "från"-katalogen. Om du inte använder jokertecken för sökvägen, kommer inställningen från att vara samma mapp som källmappen.

Om du har en käll Sök väg med jokertecken så ser din syntax ut så här:

```/data/sales/20??/**/*.csv```

Du kan ange "från" som

```/data/sales```

Och "till" som

```/backup/priorSales```

I det här fallet flyttas alla filer som skrevs under/data/Sales till/backup/priorSales.

### <a name="sql-datasets"></a>SQL-datauppsättningar

Om din källa är i SQL Database eller SQL Data Warehouse har du ytterligare alternativ för hantering av käll filer.

* **Fråga**: Ange en SQL-fråga för källan. Den här inställningen åsidosätter alla tabeller som du har valt i data uppsättningen. Observera att **order by** -satser inte stöds här, men du kan ange en fullständig Select from-instruktion. Du kan också använda användardefinierade tabell funktioner. **Select * from udfGetData ()** är en UDF i SQL som returnerar en tabell. Med den här frågan skapas en käll tabell som du kan använda i ditt data flöde.
* **Batchstorlek**: Ange en batchstorlek för att segmentera stora data till läsningar.
* **Isolerings nivå**: Standard för SQL-källor i data flöden för ADF-mappning är skrivskyddad. Du kan ändra isolerings nivån här till något av följande värden:
* Läs bekräftad
* Läs ej allokerad
* Upprepnings bar läsning
* Serialisera
* Ingen (ignorera isolerings nivå)

![Isolerings nivå](media/data-flow/isolationlevel.png "Isolerings nivå")

> [!NOTE]
> Fil åtgärder körs bara när du startar data flödet från en pipeline-körning (en pipeline för pipeline-fel eller körning) som använder aktiviteten kör data flöde i en pipeline. Fil åtgärder körs *inte* i fel söknings läge för data flöde.

### <a name="projection"></a>Projektion

Som scheman i data uppsättningar definierar projektionen i en källa data kolumnerna, typerna och formaten från käll data. 

![Inställningar på fliken projektion](media/data-flow/source3.png "Projektion")

Om text filen inte har något definierat schema väljer du **identifiera data typ** så att Data Factory kan sampla och härleda data typerna. Välj **definiera** standardformat för att automatiskt identifiera standard data formaten. 

Du kan ändra kolumn data typerna i en senare omvandling med härledd kolumn. Använd en SELECT-omvandling för att ändra kolumn namnen.

![Inställningar för standard data format](media/data-flow/source2.png "") Standardformat

### <a name="add-dynamic-content"></a>Lägg till dynamiskt innehåll
När du klickar i fält i inställnings panelen visas en hyperlänk för "Lägg till dynamiskt innehåll". När du väljer att starta uttrycks verktyget kan du ange värden dynamiskt med uttryck, statiska literala värden eller parametrar.

![Parametrar](media/data-flow/params6.png "Parametrar")

## <a name="next-steps"></a>Nästa steg

Börja skapa en [omvandling med härledd kolumn](data-flow-derived-column.md) och en [Välj omvandling](data-flow-select.md).
