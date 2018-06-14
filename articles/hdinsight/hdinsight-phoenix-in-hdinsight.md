---
title: Apache Phoenix i HDInsight - Azure HDInsight | Microsoft Docs
description: ''
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: ashishth
ms.openlocfilehash: 5d96b5656881815a82c89e0d159ba2bf556946b9
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31407739"
---
# <a name="apache-phoenix-in-hdinsight"></a>Apache Phoenix i HDInsight

[Apache Phoenix](http://phoenix.apache.org/) är en öppen källkod, massivt parallell relationsdatabaslager bygger på [HBase](hbase/apache-hbase-overview.md). Phoenix kan du använda SQL-liknande frågor över HBase. Phoenix använder JDBC-drivrutiner under så att användarna kan skapa, ta bort, ändra SQL-tabeller, index, vyer och sekvenser och upsert rader och separat gruppvis. Phoenix används noSQL interna kompilering i stället för att använda MapReduce för att kompilera frågor att aktivera skapandet av låg latens program ovanpå HBase. Phoenix lägger till coprocessors för att kunna köra klienten har angett koden i adressutrymmet för servern, köra koden samordnade med data. Detta minimerar klient/server dataöverföring.

Apache Phoenix öppnas stordata frågor till icke-utvecklare som kan använda en SQL-liknande syntax i stället för programmering. Phoenix är optimerad för HBase, till skillnad från andra verktyg som [Hive](hadoop/hdinsight-use-hive.md) och Spark SQL. Fördelen att utvecklare skriva hög performant frågor med mycket mindre kod.
<!-- [Spark SQL](spark/apache-spark-sql-with-hdinsight.md)  -->

När du skickar en SQL-fråga Phoenix kompilerar frågan till interna HBase-anrop och kör genomsökningen (eller planerar) parallellt för optimering. Det här lagret Abstraktionslager Frigör utvecklare från att skriva MapReduce-jobb i stället fokusera på affärslogiken och arbetsflöde för sina program runt Phoenixs stort datalagring.

## <a name="query-performance-optimization-and-other-features"></a>Fråga prestandaoptimering och andra funktioner

Apache Phoenix lägger till flera förbättringar av prestanda och funktioner till HBase-frågor.

### <a name="secondary-indexes"></a>Sekundärindex

HBase har ett index som lexicographically sorteras på primära Radnyckeln. Dessa poster kan endast nås via Radnyckeln. Hämtar poster via någon kolumn än Radnyckeln kräver genomsöka alla data när nödvändiga filtret. I ett sekundärt index kolumner eller uttryck som är indexerad form en alternativ radnyckel sökningar och intervallet tillåts igenom index.

Skapa ett sekundärt index med det `CREATE INDEX` kommando:

```sql
CREATE INDEX ix_purchasetype on SALTEDWEBLOGS (purchasetype, transactiondate) INCLUDE (bookname, quantity);
```

Den här metoden kan ge en avsevärd prestandaökning över köra single-indexerat frågor. Den här typen av sekundärt index är en **täcker index**, som innehåller alla kolumner som ingår i frågan. Därför krävs inte tabellen sökning och indexet uppfyller hela frågan.

### <a name="views"></a>Vyer

Phoenix vyer ger ett sätt att lösa ett HBase-begränsning, var prestanda börjar försämras när du skapar fler än cirka 100 fysiska tabeller. Phoenix vyer kan flera *virtuella tabeller* att dela en underliggande fysiska HBase-tabellen.

Att skapa vyn Phoenix är ungefär som att använda standard syntaxen för SQL-vyn. En skillnaden är att du kan definiera kolumner för din vy, förutom de kolumner som ärvts från bastabellen. Du kan också lägga till nya `KeyValue` kolumner.

Här är till exempel en fysisk tabell med namnet `product_metrics` med definitionen av följande:

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

Hoppa över sökning använder en eller flera kolumner i en sammansatt index för att hitta distinkta värden. Till skillnad från en genomsökning för intervallet, hoppa över sökning implementerar intra-raden genomsökning, vilket ger [bättre prestanda](http://phoenix.apache.org/performance.html#Skip-Scan). Under genomsökningen ignoreras det första matchande värdet tillsammans med index tills nästa värde hittas.

En hoppa över sökning använder den `SEEK_NEXT_USING_HINT` uppräkning av filtret HBase. Med hjälp av `SEEK_NEXT_USING_HINT`, hoppa över sökning håller reda på vilken uppsättning nycklar eller intervall med nycklar, söks för varje kolumn. Skip skanna och sedan tar en nyckel som skickades till den under utvärdering av filter och bestämmer om det är en av kombinationerna. Om inte, hoppa över sökning utvärderar att gå till nästa högsta nyckeln.

### <a name="transactions"></a>Transaktioner

Medan HBase innehåller radnivå transaktioner, Phoenix integreras med [Tephra](http://tephra.io/) att lägga till stöd för cross-rad och korstabell transaktioner med fullständig [av](https://en.wikipedia.org/wiki/ACID) semantik.

Som med traditionella SQL transaktioner kan transaktioner som tillhandahålls via Phoenix transaktionshanteraren du kontrollera en atomisk dataenhet har upserted, återställer transaktionen om upsert åtgärden misslyckas på någon transaktion-aktiverade tabell.

Om du vill aktivera Phoenix transaktioner, finns det [Apache Phoenix transaktion dokumentationen](http://phoenix.apache.org/transactions.html).

För att skapa en ny tabell med transaktioner som är aktiverade, ange den `TRANSACTIONAL` egenskapen `true` i en `CREATE` instruktionen:

```sql
CREATE TABLE my_table (k BIGINT PRIMARY KEY, v VARCHAR) TRANSACTIONAL=true;
```

Om du vill ändra en befintlig tabell för att vara en transaktionskö, använder du samma egenskapen i ett `ALTER` instruktionen:

```sql
ALTER TABLE my_other_table SET TRANSACTIONAL=true;
```

> [!NOTE]
> Du kan inte växla en transaktionell tabell tillbaka till som icke-transaktionell.

### <a name="salted-tables"></a>Saltat tabeller

*Region server hotspotting* kan uppstå när du skriver poster med sekventiella nycklar till HBase. Om du har flera region servrar i klustret, uppstår din skrivningar alla på bara en. Denna koncentration skapar hotspotting problemet i stället för att skriva över belastningen distribueras över alla tillgängliga region-servrar, en där, hantera belastningen. Eftersom varje region har en fördefinierad maximal storlek när en region når den storleksgränsen, är den uppdelad i två small regioner. När det händer tar något av dessa nya regioner alla nya poster, blir den nya hotspot.

För att minimera det här problemet och få bättre prestanda, före dela tabellerna så att alla servrar som region används lika. Phoenix ger *saltade tabeller*, transparent lägger till dessa byte i raden nyckel för en viss tabell. Tabellen är före dela på salt byte-gränser så lika belastningsfördelningen mellan region servrar under den inledande fasen av tabellen. Den här metoden distribuerar arbetsbelastningen skriva över alla tillgängliga region servrar, förbättra skrivning och Läs prestanda. Om du vill salt en tabell, ange den `SALT_BUCKETS` tabellen egenskapen när tabellen har skapats:

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

## <a name="enable-and-tune-phoenix-with-ambari"></a>Aktivera och finjustera Phoenix med Ambari

Ett HDInsight-HBase-kluster innehåller den [Ambari UI](hdinsight-hadoop-manage-ambari.md) för att göra konfigurationsändringar.

1. Aktivera eller inaktivera Phoenix och för att kontrollera Phoenixs frågan timeout-inställningar, logga in på Ambari-Webbgränssnittet (`https://YOUR_CLUSTER_NAME.azurehdinsight.net`) med dina användaruppgifter för Hadoop.

2. Välj **HBase** från listan över tjänster i den vänstra menyn, Välj den **konfigurationerna** fliken.

    ![Ambari HBase config](./media/hdinsight-phoenix-in-hdinsight/ambari-hbase-config.png)

3. Hitta de **Phoenix SQL** konfigurationsavsnitt som ska aktivera eller inaktivera phoenix och ange timeout-värde.

    ![Ambari Phoenix SQL konfigurationsavsnitt](./media/hdinsight-phoenix-in-hdinsight/ambari-phoenix.png)

## <a name="see-also"></a>Se också

* [Använda Apache Phoenix med Linux-baserade HBase-kluster i HDInsight](hbase/apache-hbase-phoenix-squirrel-linux.md)
