---
title: Apache Phoenix i HDInsight - Azure HDInsight
description: ''
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: ashishth
ms.openlocfilehash: 833f240572b10e9d07da0ded27f5848822a70f46
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53744351"
---
# <a name="apache-phoenix-in-hdinsight"></a>Apache Phoenix i HDInsight

[Apache Phoenix](https://phoenix.apache.org/) är en öppen källkod, massivt parallella relationsdatabaslager bygger på [Apache HBase](hbase/apache-hbase-overview.md). Phoenix kan du använda SQL-liknande frågor över HBase. Phoenix använder JDBC-drivrutiner under så att användarna kan skapa, ta bort, ändra SQL-tabeller, index, vyer och sekvenser och upsert rader individuellt och gruppvis. Phoenix används noSQL interna kompilering i stället för att använda MapReduce för att kompilera frågor att aktivera skapandet av låg latens program ovanpå HBase. Phoenix lägger till coprocessors om du vill kan du köra klienten anger kod i adressutrymmet för servern, köra koden samordnat med data. Denna metod minimerar klient/server-dataöverföring.

Apache Phoenix öppnas stordatafrågor för icke-utvecklare som kan använda en SQL-liknande syntax i stället för programmering. Phoenix är optimerade för HBase, till skillnad från andra verktyg som [Apache Hive](hadoop/hdinsight-use-hive.md) och Apache Spark SQL. Fördelen för utvecklare skriver med hög prestanda frågor med mycket mindre kod.
<!-- [Spark SQL](spark/apache-spark-sql-with-hdinsight.md)  -->

När du skickar in en SQL-fråga, Phoenix kompilerar frågan till HBase interna anrop och kör genomsökningen (eller planerar) parallellt för optimering. Det här lagret Abstraktionslager Frigör utvecklare från att skriva MapReduce-jobb, i stället fokusera på affärslogiken och arbetsflöde för sina program runt Phoenix's big datalagring.

## <a name="query-performance-optimization-and-other-features"></a>Fråga prestandaoptimering och andra funktioner

Apache Phoenix lägger till flera prestandaförbättringar av och funktioner på HBase-frågor.

### <a name="secondary-indexes"></a>Sekundära index

HBase har ett enda index som lexicographically sorteras baserat på den primära Radnyckeln. Dessa poster kan endast nås via Radnyckeln. Få åtkomst till poster via valfri kolumn än Radnyckeln kräver genomsöker alla data när nödvändiga filtret. I ett sekundärt index kolumner eller uttryck som är indexerad form en alternativa radnyckel, vilket gör att sökningar och intervallet söka igenom detta index.

Skapa ett sekundärt index med det `CREATE INDEX` kommando:

```sql
CREATE INDEX ix_purchasetype on SALTEDWEBLOGS (purchasetype, transactiondate) INCLUDE (bookname, quantity);
```

Den här metoden kan ge en avsevärd prestandaökning över köra enskild indexeras frågor. Den här typen av sekundärt index är en **som täcker index**, som innehåller alla kolumner som ingår i frågan. Därför tabell lookup är inte obligatoriskt och indexet uppfyller villkoren i hela frågan.

### <a name="views"></a>Vyer

Phoenix vyer är ett sätt att lösa ett HBase-begränsning, där prestanda börjar försämras när du skapar fler än cirka 100 fysiska tabeller. Phoenix vyer kan flera *virtuella tabeller* att dela en underliggande fysiska HBase-tabellen.

Skapa en vy för Phoenix liknar med standard SQL-vyn-syntax. En skillnad är att du kan definiera kolumner för vyn, förutom de kolumner som har ärvts från dess bastabellen. Du kan också lägga till nya `KeyValue` kolumner.

Här är till exempel en fysisk tabell med namnet `product_metrics` med följande definition:

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

Om du vill lägga till fler kolumner senare använda den `ALTER VIEW` instruktionen.

### <a name="skip-scan"></a>Hoppa över sökning

Hoppa över sökning använder en eller flera kolumner i ett sammansatt index för att hitta distinkta värden. Till skillnad från en genomsökning för intervallet, hoppa över sökning implementerar intra-raden genomsökning, väjande [förbättrad prestanda](https://phoenix.apache.org/performance.html#Skip-Scan). Vid sökning hoppas det första matchade värdet tillsammans med indexet tills nästa värde hittas.

En hoppa över sökning använder den `SEEK_NEXT_USING_HINT` uppräkning av HBase-filtret. Med hjälp av `SEEK_NEXT_USING_HINT`, hoppa över sökning håller reda på vilken uppsättning nycklar eller intervall av nycklar, genomsöks efter i varje kolumn. Alternativet Hoppa över skanna och sedan tar en nyckel som skickades till den under filter utvärdering och bestämmer om det är en av kombinationerna. Annars kan du hoppa över sökning utvärderar till går du vidare till nästa högsta nyckeln.

### <a name="transactions"></a>Transaktioner

Medan HBase ger på radnivå transaktioner, Phoenix kan integreras med [Tephra](https://tephra.io/) att lägga till stöd för flera rader och korstabell transaktioner med fullständig [ACID](https://en.wikipedia.org/wiki/ACID) semantik.

Som med traditionella SQL-transaktioner kan transaktioner som tillhandahålls via transaktionshanteraren Phoenix du se till att en atomisk enhet med data är har upserted, återställer transaktionen om upsert-åtgärden misslyckas i en transaktion-aktiverade tabell.

För att aktivera Phoenix transaktioner, se den [Apache Phoenix transaktion dokumentation](https://phoenix.apache.org/transactions.html).

Om du vill skapa en ny tabell med transaktioner som har aktiverats, ange den `TRANSACTIONAL` egenskap `true` i en `CREATE` instruktionen:

```sql
CREATE TABLE my_table (k BIGINT PRIMARY KEY, v VARCHAR) TRANSACTIONAL=true;
```

Om du vill ändra en befintlig tabell för att vara en transaktionskö, använder du samma egenskap i en `ALTER` instruktionen:

```sql
ALTER TABLE my_other_table SET TRANSACTIONAL=true;
```

> [!NOTE]  
> Du kan inte byta en transaktionell tabell tillbaka för att vara icke-transaktionell.

### <a name="salted-tables"></a>Saltat tabeller

*Region server hotspotting* kan inträffa när du skriver poster med sekventiella nycklar till HBase. Om du har flera regionservrar i klustret, sker din skrivningar alla på bara en. Den här koncentration skapar hotspotting problemet i stället för att skriva arbetsbelastningen distribueras på alla tillgängliga regionen-servrar bara en där, hantera belastningen. Eftersom varje region har en fördefinierad maximal storlek när en region når den storleksgränsen, delas det upp i två små områden. När det sker tar en av dessa nya områden alla nya poster, blir en ny hotspot.

För att minimera det här problemet och få bättre prestanda, förväg dela tabeller så att alla regionservrar som används lika. Phoenix ger *saltade tabeller*, transparent lägga till dessa byte i radnyckel för en viss tabell. Tabellen är förväg dela upp på salt byte-gränserna för att säkerställa lika med belastningsutjämning bland regionservrar under det första steget i tabellen. Den här metoden distribuerar arbetsbelastningen skriva över alla tillgängliga regionen-servrar och förbättra skrivningen och läsa prestanda. Om du vill salt en tabell, ange den `SALT_BUCKETS` tabellen egenskapen när tabellen har skapats:

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

## <a name="enable-and-tune-phoenix-with-apache-ambari"></a>Aktivera och finjustera Phoenix med Apache Ambari

En HDInsight HBase-kluster innehåller den [Ambari UI](hdinsight-hadoop-manage-ambari.md) för att göra ändringar i konfigurationen.

1. Aktivera eller inaktivera Phoenix och för att kontrollera inställningarna för timeout för Phoenixs fråga, logga in på Ambari-Webbgränssnittet (`https://YOUR_CLUSTER_NAME.azurehdinsight.net`) med hjälp av dina Hadoop-autentiseringsuppgifter.

2. Välj **HBase** från listan över tjänster i den vänstra menyn, Välj den **Peeringkonfigurationer** fliken.

    ![Ambari HBase-config](./media/hdinsight-phoenix-in-hdinsight/ambari-hbase-config.png)

3. Hitta den **Phoenix SQL** konfigurationsavsnittet för att aktivera eller inaktivera phoenix och ange Frågetimeout.

    ![Konfigurationsavsnittet för Ambari Phoenix SQL](./media/hdinsight-phoenix-in-hdinsight/ambari-phoenix.png)

## <a name="see-also"></a>Se också

* [Använda Apache Phoenix med Linux-baserade HBase-kluster i HDInsight](hbase/apache-hbase-phoenix-squirrel-linux.md)
