---
title: Apache Phoenix i HDInsight - Azure HDInsight
description: Översikt över Apache Phoenix
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: b1d81296c996ab09cb6482cb970496779ccf8bd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435499"
---
# <a name="apache-phoenix-in-azure-hdinsight"></a>Apache Phoenix i Azure HDInsight

[Apache Phoenix](https://phoenix.apache.org/) är en öppen källkod, massivt parallella relationella databaslager byggt på [Apache HBase](hbase/apache-hbase-overview.md). Phoenix kan du använda SQL-liknande frågor över HBase. Phoenix använder JDBC-drivrutiner under för att göra det möjligt för användare att skapa, ta bort, ändra SQL-tabeller, index, vyer och sekvenser och uppåtrader individuellt och i grupp. Phoenix använder noSQL native kompilering i stället för att använda MapReduce för att kompilera frågor, vilket gör det möjligt att skapa program med låg latens ovanpå HBase. Phoenix lägger till coprocessorer för att stödja körning av klientkod i serverns adressutrymme och kör koden som är samlokaliserad med data. Den här metoden minimerar överföring av klient-serverdata.

Apache Phoenix öppnar stora datafrågor för icke-utvecklare som kan använda en SQL-liknande syntax i stället för programmering. Phoenix är mycket optimerad för HBase, till skillnad från andra verktyg som [Apache Hive](hadoop/hdinsight-use-hive.md) och Apache Spark SQL. Fördelen för utvecklare är att skriva högpresterande frågor med mycket mindre kod.

När du skickar en SQL-fråga sammanställer Phoenix frågan till HBase-inbyggda anrop och kör genomsökningen (eller planen) parallellt för optimering. Detta lager av abstraktion befriar utvecklaren från att skriva MapReduce jobb, att fokusera istället på affärslogik och arbetsflödet för deras program runt Phoenix stora datalagring.

## <a name="query-performance-optimization-and-other-features"></a>Frågeprestandaoptimering och andra funktioner

Apache Phoenix lägger till flera prestandaförbättringar och funktioner i HBase-frågor.

### <a name="secondary-indexes"></a>Sekundära index

HBase har ett enda index som är lexikografiskt sorterat på den primära radnyckeln. Dessa poster kan endast nås via radnyckeln. Åtkomst till poster via någon annan kolumn än radnyckeln kräver att du skannar alla data när du använder det filter som krävs. I ett sekundärt index bildar de kolumner eller uttryck som indexeras en alternativ radnyckel, vilket gör att sökningar och intervall genomsökningar på indexet.

Skapa ett sekundärt `CREATE INDEX` index med kommandot:

```sql
CREATE INDEX ix_purchasetype on SALTEDWEBLOGS (purchasetype, transactiondate) INCLUDE (bookname, quantity);
```

Den här metoden kan ge en betydande prestandaökning jämfört med att köra enindexerade frågor. Den här typen av sekundärt index är ett **täckindex**som innehåller alla kolumner som ingår i frågan. Därför krävs inte tabellsökningen och indexet uppfyller hela frågan.

### <a name="views"></a>Vyer

Phoenix-vyer är ett sätt att övervinna en HBase-begränsning, där prestanda börjar försämras när du skapar mer än cirka 100 fysiska tabeller. Phoenix-vyer gör det möjligt för flera *virtuella tabeller* att dela en underliggande fysisk HBase-tabell.

Att skapa en Phoenix-vy liknar standardsyntax för SQL-vy. En skillnad är att du kan definiera kolumner för vyn, förutom de kolumner som ärvs från bastabellen. Du kan också `KeyValue` lägga till nya kolumner.

Här är till exempel en `product_metrics` fysisk tabell med namnet med följande definition:

```sql
CREATE  TABLE product_metrics (
    metric_type CHAR(1),
    created_by VARCHAR,
    created_date DATE,
    metric_id INTEGER
    CONSTRAINT pk PRIMARY KEY (metric_type, created_by, created_date, metric_id));
```

Definiera en vy över den här tabellen med ytterligare kolumner:

```sql
CREATE VIEW mobile_product_metrics (carrier VARCHAR, dropped_calls BIGINT) AS
SELECT * FROM product_metrics
WHERE metric_type = 'm';
```

Om du vill lägga `ALTER VIEW` till fler kolumner senare använder du uttrycket.

### <a name="skip-scan"></a>Hoppa över skanningen

Hoppa över genomsökningen använder en eller flera kolumner i ett sammansatt index för att hitta distinkta värden. Till skillnad från en områdessökning implementerar genomsökningen inom rad, vilket ger [bättre prestanda](https://phoenix.apache.org/performance.html#Skip-Scan). Vid skanning hoppas det första matchade värdet över tillsammans med indexet tills nästa värde hittas.

En hoppa genomsökning använder `SEEK_NEXT_USING_HINT` uppräkningen av HBase-filtret. Med `SEEK_NEXT_USING_HINT`hjälp håller hoppa igenom reda på vilka nycklar eller tangentintervall som söks i varje kolumn. Hoppa över-genomsökningen tar sedan en nyckel som skickades till den under filterutvärderingen och avgör om det är en av kombinationerna. Om inte, utvärderar hoppa igenom den näst högsta nyckeln att hoppa till.

### <a name="transactions"></a>Transaktioner

Medan HBase tillhandahåller transaktioner på radnivå integreras Phoenix med [Tephra](https://tephra.io/) för att lägga till cross-row- och cross-table-transaktionsstöd med full ACID-semantik. [ACID](https://en.wikipedia.org/wiki/ACID)

Precis som med traditionella SQL-transaktioner kan du med transaktioner som tillhandahålls via Phoenix-transaktionshanteraren se till att en atomenhet med data har aktiverats, vilket återställer transaktionen om åtgärden för att öppna den misslyckas i en transaktionsaktiverad tabell.

Om du vill aktivera Phoenix-transaktioner finns i [dokumentationen till Apache Phoenix-transaktionen](https://phoenix.apache.org/transactions.html).

Om du vill skapa en ny `TRANSACTIONAL` tabell `true` med `CREATE` aktiverade transaktioner ställer du in egenskapen på i ett utdrag:

```sql
CREATE TABLE my_table (k BIGINT PRIMARY KEY, v VARCHAR) TRANSACTIONAL=true;
```

Om du vill ändra en befintlig tabell så `ALTER` att den är transaktionell använder du samma egenskap i ett utdrag:

```sql
ALTER TABLE my_other_table SET TRANSACTIONAL=true;
```

> [!NOTE]  
> Du kan inte växla tillbaka en transaktionstabell till att inte vara transaktionell.

### <a name="salted-tables"></a>Saltade bord

*Aktivering av regionserver* kan uppstå när poster skrivs med sekventiella nycklar till HBase. Även om du kan ha flera regionservrar i klustret sker alla dina skrivningar på bara en. Den här koncentrationen skapar aktiveringsproblemet där bara en hanterar belastningen i stället för att skrivarbetsbelastningen distribueras över alla tillgängliga regionservrar. Eftersom varje region har en fördefinierad maximal storlek, när en region når den storleksgränsen, delas den upp i två små regioner. När det händer tar en av dessa nya regioner alla nya poster och blir den nya hotspoten.

För att minska problemet och uppnå bättre prestanda, fördelade tabeller så att alla regionservrar används lika. Phoenix ger *saltade tabeller,* transparent lägga saltning byte till radnyckeln för en viss tabell. Tabellen är försplittrad på saltbytegränserna för att säkerställa lika belastningsfördelning mellan regionservrar under tabellens inledande fas. Den här metoden distribuerar skrivarbetsbelastningen över alla tillgängliga regionservrar, vilket förbättrar skriv- och läsprestanda. Om du vill salta en tabell anger du `SALT_BUCKETS` tabellegenskapen när tabellen skapas:

```sql
CREATE TABLE Saltedweblogs (
    transactionid varchar(500) Primary Key,
    transactiondate Date NULL,
    customerid varchar(50) NULL,
    bookid varchar(50) NULL,
    purchasetype varchar(50) NULL,
    orderid varchar(50) NULL,
    bookname varchar(50) NULL,
    categoryname varchar(50) NULL,
    invoicenumber varchar(50) NULL,
    invoicestatus varchar(50) NULL,
    city varchar(50) NULL,
    state varchar(50) NULL,
    paymentamount DOUBLE NULL,
    quantity INTEGER NULL,
    shippingamount DOUBLE NULL) SALT_BUCKETS=4;
```

## <a name="enable-and-tune-phoenix-with-apache-ambari"></a>Aktivera och stämma Phoenix med Apache Ambari

Ett HDInsight HBase-kluster innehåller [Ambari-användargränssnittet](hdinsight-hadoop-manage-ambari.md) för att göra konfigurationsändringar.

1. Om du vill aktivera eller inaktivera Phoenix och styra Phoenix-inställningar för frågetidsgränsen`https://YOUR_CLUSTER_NAME.azurehdinsight.net`loggar du in på Ambari Web UI ( ) med hjälp av dina Hadoop-användaruppgifter.

2. Välj **HBase** i listan över tjänster på menyn till vänster och välj sedan fliken **Configs.**

    ![Apache Ambari HBase-konfigurationer](./media/hdinsight-phoenix-in-hdinsight/ambari-hbase-config1.png)

3. Leta reda på **phoenix SQL-konfigurationsavsnittet** för att aktivera eller inaktivera Phoenix och ange tidsgränsen för frågan.

    ![Avsnittet Ambari Phoenix SQL-konfiguration](./media/hdinsight-phoenix-in-hdinsight/apache-ambari-phoenix.png)

## <a name="see-also"></a>Se även

* [Använd Apache Phoenix med Linux-baserade HBase-kluster i HDInsight](hbase/apache-hbase-query-with-phoenix.md)

* [Använd Apache Zeppelin för att köra Apache Phoenix-frågor via Apache HBase i Azure HDInsight](./hbase/apache-hbase-phoenix-zeppelin.md)
