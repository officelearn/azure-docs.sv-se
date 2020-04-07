---
title: Prestandajustering med materialiserade vyer
description: Rekommendationer och överväganden som du bör känna till när du använder materialiserade vyer för att förbättra frågeprestanda.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 6e942130d9acf803665e52498ef6a4976cc9ade7
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743167"
---
# <a name="performance-tuning-with-materialized-views"></a>Prestandajustering med materialiserade vyer

De materialiserade vyerna i Synapse SQL-poolen ger en metod för lågt underhåll för komplexa analytiska frågor för att få snabba prestanda utan någon frågeändring. I den här artikeln beskrivs den allmänna vägledningen om hur du använder materialiserade vyer.

De materialiserade vyerna i Azure SQL Data Warehouse ger en metod för lågt underhåll för komplexa analytiska frågor för att få snabba prestanda utan någon frågeändring. I den här artikeln beskrivs den allmänna vägledningen om hur du använder materialiserade vyer.

## <a name="materialized-views-vs-standard-views"></a>Materialiserade vyer jämfört med standardvyer

SQL-poolen stöder standard- och materialiserade vyer.  Båda är virtuella tabeller som skapats med SELECT-uttryck och presenteras för frågor som logiska tabeller.  Vyer kapslar in komplexiteten i gemensam databeräkning och lägger till ett abstraktionslager i beräkningsändringar så att du inte behöver skriva om frågor.  

En standardvy beräknar sina data varje gång vyn används.  Det finns inga data lagrade på disken. Personer använder vanligtvis standardvyer som ett verktyg som hjälper till att ordna logiska objekt och frågor i en databas.  Om du vill använda en standardvy måste en fråga referera direkt till den.

En materialiserad vy förberäknar, lagrar och underhåller sina data i SQL-poolen precis som en tabell.  Det behövs ingen omdämning varje gång en materialiserad vy används.  Det är därför frågor som använder alla eller delmängd av data i materialiserade vyer kan få snabbare prestanda.  Ännu bättre, frågor kan använda en materialiserad vy utan att direkt hänvisa till det, så det finns ingen anledning att ändra programkod.  

De flesta kraven i en standardvy gäller fortfarande för en materialiserad vy. Mer information om den materialiserade vysyntaxen och andra krav finns i [SKAPA MATERIALISERAD VY SOM SELECT](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

| Jämförelse                     | Visa                                         | Materialiserad vy
|:-------------------------------|:---------------------------------------------|:--------------------------------------------------------------|
|Visa definition                 | Lagras i SQL-pool.              | Lagras i SQL-pool.
|Visa innehåll                    | Genereras varje gång vyn används.   | Förbehandlade och lagrade i SQL-pool under vyn skapas. Uppdateras när data läggs till i de underliggande tabellerna.
|Datauppdatering                    | Alltid uppdaterad                               | Alltid uppdaterad
|Snabbhet för att hämta vydata från komplexa frågor     | Långsam                                         | Snabb  
|Extra förvaring                   | Inga                                           | Ja
|Syntax                          | SKAPA VY                                  | SKAPA MATERIALISERAD VY SOM VÄLJ

## <a name="benefits-of-using-materialized-views"></a>Fördelar med att använda materialiserade vyer

En korrekt utformad materialiserad vy kan ge följande fördelar:

- Minska körningstiden för komplexa frågor med JOINs och aggregerade funktioner. Ju mer komplex frågan är, desto högre är risken för körningstidssparande. Den största fördelen uppnås när en frågas beräkningskostnad är hög och den resulterande datauppsättningen är liten.  
- Optimeraren i SQL-poolen kan automatiskt använda distribuerade materialiserade vyer för att förbättra frågekörningsplaner.  Den här processen är transparent för användare som tillhandahåller snabbare frågeprestanda och kräver inte frågor för att direkt referera till de materialiserade vyerna.
- Kräver lågt underhåll i vyerna.  Alla inkrementella dataändringar från bastabellerna läggs automatiskt till i de materialiserade vyerna på ett synkront sätt.  Med den här designen kan du ställa in materialiserade vyer för att returnera samma data som att direkt fråga bastabellerna.
- Data i en materialiserad vy kan fördelas på ett annat sätt än bastabellerna.  
- Data i materialiserade vyer får samma fördelar med hög tillgänglighet och återhämtning som data i vanliga tabeller.  

De materialiserade vyer som implementeras i SQL-poolen ger också följande ytterligare fördelar:

Jämfört med andra informationslagerleverantörer ger de materialiserade vyerna som implementerats i Azure SQL Data Warehouse även följande ytterligare fördelar:

- Automatisk och synkron data uppdateras med dataändringar i bastabeller. Ingen användaråtgärd krävs.
- Brett stöd för aggregerad funktion. Se [Skapa materialiserad vy som select (Transact-SQL)](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).
- Stödet för frågespecifik materialiserad vyrekommendation.  Se [FÖRKLARA (Transact-SQL)](/sql/t-sql/queries/explain-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="common-scenarios"></a>Vanliga scenarier  

Materialiserade vyer används vanligtvis i följande scenarier:

**Behovet av att förbättra prestanda för komplexa analytiska frågor mot stora data i storlek**

Komplexa analytiska frågor använder vanligtvis fler aggregeringsfunktioner och tabellkopplingar, vilket orsakar fler beräkningstunga åtgärder som blandningar och kopplingar i frågekörning.  Det är därför dessa frågor tar längre tid att slutföra, speciellt på stora tabeller.  

Användare kan skapa materialiserade vyer för data som returneras från vanliga beräkningar av frågor, så det behövs ingen omdämning när dessa data behövs av frågor, vilket möjliggör lägre beräkningskostnader och snabbare frågesvar.

**Behöver snabbare prestanda utan eller minsta frågeändringar**

Schema- och frågeändringar i SQL-pooler hålls vanligtvis till ett minimum för att stödja vanliga ETL-åtgärder och rapportering.  Personer kan använda materialiserade vyer för frågeprestandajustering, om kostnaden för vyerna kan kompenseras av förstärkningen i frågeprestanda.

I jämförelse med andra justeringsalternativ som skalning och statistikhantering är det en mycket mindre effektfull produktionsförändring för att skapa och underhålla en materialiserad vy och dess potentiella prestandavinst är också högre.

- Att skapa eller underhålla materialiserade vyer påverkar inte de frågor som körs mot bastabellerna.
- Frågeoptimeraren kan automatiskt använda de distribuerade materialiserade vyerna utan direktvisningsreferens i en fråga. Den här funktionen minskar behovet av frågeändring i prestandajustering.

**Behöver olika datadistributionsstrategi för snabbare frågeprestanda**

SQL pool är ett distribuerat massivt parallellt bearbetningssystem (MPP).   Data i en SQL-pooltabell distribueras över 60 noder med en av tre [distributionsstrategier](sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (hash, round_robin eller replikerade).  

Datadistributionen anges när tabellen skapas och förblir oförändrad tills tabellen tas bort. Materialiserad vy som en virtuell tabell på disken stöder hash- och round_robin datadistributioner.  Användare kan välja en datadistribution som skiljer sig från bastabellerna men som är optimal för prestanda för frågor som använder vyerna mest.  

## <a name="design-guidance"></a>Designvägledning

Här är den allmänna vägledningen om hur du använder materialiserade vyer för att förbättra frågeprestanda:

**Design för din arbetsbelastning**

Innan du börjar skapa materialiserade vyer är det viktigt att ha en djup förståelse för din arbetsbelastning när det gäller frågemönster, betydelse, frekvens och storleken på resulterande data.  

Användare kan köra EXPLAIN WITH_RECOMMENDATIONS <SQL_statement> för de materialiserade vyer som rekommenderas av frågeoptimeraren.  Eftersom dessa rekommendationer är frågespecifika kan det hända att en materialiserad vy som gynnar en enskild fråga inte är optimal för andra frågor i samma arbetsbelastning.  

Utvärdera dessa rekommendationer med dina arbetsbelastningsbehov i åtanke.  De idealiska materialiserade vyerna är de som gynnar arbetsbelastningens prestanda.  

**Var medveten om avvägningen mellan snabbare frågor och kostnaden**

För varje materialiserad vy finns det en datalagringskostnad och en kostnad för att underhålla vyn.  När data ändras i bastabeller ökar storleken på den materialiserade vyn och dess fysiska struktur ändras också.  För att undvika försämring av frågeprestanda underhålls varje materialiserad vy separat av SQL-poolmotorn.  

Underhållsarbetsbelastningen blir högre när antalet materialiserade vyer och bastabelländringar ökar.   Användare bör kontrollera om kostnaden för alla materialiserade vyer kan kompenseras av frågeprestandavinsten.  

Du kan köra den här frågan för listan över materialiserad vy i en databas:

```sql
SELECT V.name as materialized_view, V.object_id
FROM sys.views V
JOIN sys.indexes I ON V.object_id= I.object_id AND I.index_id < 2;
```

Alternativ för att minska antalet materialiserade vyer:

- Identifiera vanliga datauppsättningar som ofta används av komplexa frågor i din arbetsbelastning.  Skapa materialiserade vyer för att lagra dessa datauppsättningar så att optimeraren kan använda dem som byggstenar när du skapar körningsplaner.  

- Släpp de materialiserade vyer som har låg användning eller som inte längre behövs.  En inaktiverad materialiserad vy underhålls inte, men den medför fortfarande lagringskostnader.  

- Kombinera materialiserade vyer som skapats i samma eller liknande bastabeller även om deras data inte överlappar varandra.  Kamning materialiserade vyer kan resultera i en större vy i storlek än summan av de separata vyerna, men visningsunderhållskostnaden bör minska.  Ett exempel:

```sql

-- Query 1 would benefit from having a materialized view created with this SELECT statement

SELECT A, SUM(B)
FROM T
GROUP BY A

-- Query 2 would benefit from having a materialized view created with this SELECT statement

SELECT C, SUM(D)
FROM T
GROUP BY C

-- You could create a single mateiralized view of this form

SELECT A, C, SUM(B), SUM(D)
FROM T
GROUP BY A, C

```

**Alla prestandajusteringar kräver inte frågeändring**

SQL-pooloptimeraren kan automatiskt använda distribuerade materialiserade vyer för att förbättra frågeprestanda.  Det här stödet tillämpas transparent på frågor som inte refererar till vyer och frågor som använder aggregerade som inte stöds i materialiserade vyer.  Ingen frågeändring behövs. Du kan kontrollera en frågas uppskattade körningsplan för att bekräfta om en materialiserad vy används.  

**Övervaka materialiserade vyer**

En materialiserad vy lagras i SQL-poolen precis som en tabell med klustrade columnstore index (CCI).  Läsa data från en materialiserad vy inkluderar att skanna CCI-indexsegmenten och tillämpa eventuella inkrementella ändringar från bastabeller. När antalet inkrementella ändringar är för högt kan det ta längre tid att lösa en fråga från en materialiserad vy än att fråga bastabellerna direkt.  

För att undvika försämring av frågeprestanda är det en bra idé att köra [DBCC-PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) för att övervaka vyns overhead_ratio (total_rows / max(1, base_view_row)).  Användare bör återskapa den materialiserade vyn om overhead_ratio är för hög.

**Materialiserad vy och resultatuppsättningscache**

Dessa två funktioner introduceras i SQL-poolen ungefär samtidigt för frågeprestandajustering.  Cachelagring av resultatuppsättning används för att få hög samtidighet och snabb respons från repetitiva frågor mot statiska data.  

Om du vill använda det cachelagrade resultatet måste formen på den cachebegärande frågan matchas med frågan som producerade cacheminnet.  Dessutom måste det cachelagrade resultatet gälla för hela frågan.  

Materialiserade vyer tillåter dataändringar i bastabellerna.  Data i materialiserade vyer kan användas på en del av en fråga.  Med det här stödet kan samma materialiserade vyer användas av olika frågor som delar viss beräkning för snabbare prestanda.

## <a name="example"></a>Exempel

I det här exemplet används en TPCDS-liknande fråga som hittar kunder som spenderar mer pengar via katalog än i butiker, identifierar de kunder som föredras och deras ursprungsland.   Frågan innebär att välja TOPP 100-poster från UNION av tre sub-SELECT-satser som involverar SUM() och GROUP BY.

```sql
WITH year_total AS (
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
       ,'s' sale_type
FROM customer
     ,store_sales
     ,date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
       ,'c' sale_type
FROM customer
     ,catalog_sales
     ,date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
       ,'w' sale_type
FROM customer
     ,web_sales
     ,date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
         )
  SELECT TOP 100
                  t_s_secyear.customer_id
                 ,t_s_secyear.customer_first_name
                 ,t_s_secyear.customer_last_name
                 ,t_s_secyear.customer_birth_country
FROM year_total t_s_firstyear
     ,year_total t_s_secyear
     ,year_total t_c_firstyear
     ,year_total t_c_secyear
     ,year_total t_w_firstyear
     ,year_total t_w_secyear
WHERE t_s_secyear.customer_id = t_s_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_c_secyear.customer_id
   AND t_s_firstyear.customer_id = t_c_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_secyear.customer_id
   AND t_s_firstyear.sale_type = 's'
   AND t_c_firstyear.sale_type = 'c'
   AND t_w_firstyear.sale_type = 'w'
   AND t_s_secyear.sale_type = 's'
   AND t_c_secyear.sale_type = 'c'
   AND t_w_secyear.sale_type = 'w'
   AND t_s_firstyear.dyear+0 =  1999
   AND t_s_secyear.dyear+0 = 1999+1
   AND t_c_firstyear.dyear+0 =  1999
   AND t_c_secyear.dyear+0 =  1999+1
   AND t_w_firstyear.dyear+0 = 1999
   AND t_w_secyear.dyear+0 = 1999+1
   AND t_s_firstyear.year_total > 0
   AND t_c_firstyear.year_total > 0
   AND t_w_firstyear.year_total > 0
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_s_firstyear.year_total > 0 THEN t_s_secyear.year_total / t_s_firstyear.year_total ELSE NULL END
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_w_firstyear.year_total > 0 THEN t_w_secyear.year_total / t_w_firstyear.year_total ELSE NULL END
ORDER BY t_s_secyear.customer_id
         ,t_s_secyear.customer_first_name
         ,t_s_secyear.customer_last_name
         ,t_s_secyear.customer_birth_country
OPTION ( LABEL = 'Query04-af359846-253-3');
```

Kontrollera frågans uppskattade körningsplan.  Det finns 18 blandningar och 17 kopplingar till åtgärder, som tar längre tid att utföra. Nu ska vi skapa en materialiserad vy för var och en av de tre sub-SELECT-satserna.

```sql
CREATE materialized view nbViewSS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.store_sales
     ,dbo.date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
GO
CREATE materialized view nbViewCS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
          , count_big(*) as cb
FROM dbo.customer
     ,dbo.catalog_sales
     ,dbo.date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

GO
CREATE materialized view nbViewWS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.web_sales
     ,dbo.date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

```

Kontrollera körningsplanen för den ursprungliga frågan igen.  Nu antalet kopplingar ändras från 17 till 5 och det finns ingen shuffle längre.  Klicka på ikonen Filteråtgärd i planen, dess utdatalista visar att data läss från de materialiserade vyerna i stället för bastabeller.  

 ![Plan_Output_List_with_Materialized_Views](./media/performance-tuning-materialized-views/output-list.png)

Med materialiserade vyer körs samma fråga mycket snabbare utan kodändringar.  

## <a name="next-steps"></a>Nästa steg

Fler utvecklingstips finns i [Översikt över utveckling av Synapse SQL-pool](sql-data-warehouse-overview-develop.md).
