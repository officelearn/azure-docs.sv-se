---
title: Konsekvensnivåer och API:er för Azure Cosmos DB
description: 'För att förstå konsekvensnivåerna i API: er i Azure Cosmos DB.'
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.reviewer: sngun
ms.openlocfilehash: a506c696cdb9ca6c6221b54c63d2446b7cb86a69
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54430577"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Konsekvensnivåer och API:er för Azure Cosmos DB

Fem konsekvensmodeller erbjuds av Azure Cosmos DB stöds internt av Azure Cosmos DB SQL API. När du använder Azure Cosmos DB är SQL API standardinställningen. 

Azure Cosmos DB erbjuder även inbyggt stöd för wire protocol-kompatibla API: er för populära databaser. Databaser innehåller MongoDB, Apache Cassandra, Gremlin och Azure Table storage. Dessa databaser erbjuder inte exakt definierade konsekvensmodeller eller omfattas av serviceavtalet garantier för konsekvensnivåer. De tillhandahåller normalt endast en delmängd av de fem konsekvensmodeller erbjuds av Azure Cosmos DB. Standard-konsekvensnivå som konfigurerats på Azure Cosmos DB-kontot används för SQL-API, Gremlin-API och tabell-API. 

I följande avsnitt visas mappningen mellan datakonsekvens som begärts av en klient OSS-drivrutinen för Apache Cassandra 4.x och MongoDB 3.4. Det här dokumentet visar även motsvarande Azure Cosmos DB-konsekvensnivåer för Apache Cassandra och MongoDB.

## <a id="cassandra-mapping"></a>Mappning mellan Apache Cassandra och Azure Cosmos DB-konsekvensnivåer

Den här tabellen visar konsekvens mappningen mellan Apache Cassandra och konsekvensnivåer i Azure Cosmos DB. För var och en av Cassandra läsning och skrivning konsekvensnivåer motsvarande konsekvensnivå för Cosmos-DB tillhandahåller starkare, d.v.s. striktare garantier.


<table>
<tr> 
  <th rowspan="2">Cassandra-konsekvensnivå</th> 
  <th rowspan="2">Cosmos DB-konsekvensnivå</th> 
  <th colspan="3">Skriva konsekvens mappning</th> 
  <th colspan="3">Läs konsekvens mappning</th> 
</tr> 


 
 <tr> 
  <th>Cassandra</th> 
  <th>Cosmos DB</th> 
  <th>Garanti</th> 
  <th>Från Cassandra</th> 
  <th>To Cosmos DB</th> 
  <th>Garanti</th> 
 </tr> 
 
  <tr> 
  <td rowspan="6">Alla</td> 
  <td rowspan="6">Stark</td> 
  <td>Alla</td> 
  <td>Stark</td> 
  <td>Lineariserbarhet</td> 
  <td>ALLA KVORUM, SERIENUMMER, LOCAL_QUORUM, LOCAL_SERIAL, TRE, TVÅ, EN, LOCAL_ONE</td> 
  <td>Stark</td> 
  <td>Lineariserbarhet</td> 
 </tr> 
 
 <tr> 
  <td rowspan="2">EACH_QUORUM</td> 
  <td rowspan="2">Stark</td> 
  <td rowspan="2">Lineariserbarhet</td> 
  <td>ALLA KVORUM, SERIENUMMER, LOCAL_QUORUM, LOCAL_SERIAL, TRE, TVÅ</td> 
  <td>Stark</td> 
  <td >Lineariserbarhet</td> 
 </tr> 
 
 <tr>
 <td>LOCAL_ONE, EN</td>
  <td>Konsekvent prefix</td>
   <td>Global konsekvent Prefix</td>
 </tr>
 

 <tr> 
  <td rowspan="2">KVORUM ÄR SERIELL</td> 
  <td rowspan="2">Stark</td> 
  <td rowspan="2">Lineariserbarhet</td> 
  <td>ALLA, KVORUM, SERIE</td> 
  <td>Stark</td> 
  <td >Lineariserbarhet</td> 
 </tr> 

 <tr>
   <td>LOCAL_ONE, EN, LOCAL_QUORUM, LOCAL_SERIAL, TVÅ, TRE</td>
   <td>Konsekvent prefix</td>
   <td>Global konsekvent Prefix</td>
 </tr>
 
 
 <tr> 
 <td>LOCAL_QUORUM, TRE, TVÅ, EN, LOCAL_ONE, <b>ANY</b></td> 
  <td>Konsekvent prefix</td> 
  <td>Global konsekvent Prefix</td> 
  <td>LOCAL_ONE, EN, TVÅ, TRE, LOCAL_QUORUM, KVORUM</td> 
  <td>Konsekvent prefix</td> 
  <td>Global konsekvent Prefix</td>
 </tr> 
 
 
  <tr> 
  <td rowspan="6">EACH_QUORUM</td> 
  <td rowspan="6">Stark</td> 
  <td rowspan="2">EACH_QUORUM</td> 
  <td rowspan="2">Stark</td> 
  <td rowspan="2">Lineariserbarhet</td> 
  <td>ALLA KVORUM, SERIENUMMER, LOCAL_QUORUM, LOCAL_SERIAL, TRE, TVÅ</td> 
  <td>Stark</td> 
  <td>Lineariserbarhet</td> 
 </tr> 
 
 <tr>
 <td>LOCAL_ONE, EN</td>
  <td>Konsekvent prefix</td>
   <td>Global konsekvent Prefix</td>
 </tr>
 
 
 
 <tr> 
  <td rowspan="2">KVORUM ÄR SERIELL</td> 
  <td rowspan="2">Stark</td> 
  <td rowspan="2">Lineariserbarhet</td> 
  <td>ALLA, KVORUM, SERIE</td> 
  <td>Stark</td> 
  <td>Lineariserbarhet</td> 
 </tr> 
 
 <tr>
 <td>LOCAL_ONE, EN, LOCAL_QUORUM, LOCAL_SERIAL, TVÅ, TRE</td>
  <td>Konsekvent prefix</td>
   <td>Global konsekvent Prefix</td>
 </tr>
 
 
  <tr> 
  <td rowspan="2">LOCAL_QUORUM, TRE, TVÅ, EN, LOCAL_ONE, ALLA</td> 
  <td rowspan="2">Konsekvent prefix</td> 
  <td rowspan="2">Global konsekvent Prefix</td> 
  <td>Alla</td> 
  <td>Stark</td> 
  <td>Lineariserbarhet</td> 
 </tr> 
 
 <tr>
 <td>LOCAL_ONE, EN, TVÅ, TRE, LOCAL_QUORUM, KVORUM</td>
  <td>Konsekvent prefix</td>
   <td>Global konsekvent Prefix</td>
 </tr>


  <tr> 
  <td rowspan="4">QUORUM</td> 
  <td rowspan="4">Stark</td> 
  <td rowspan="2">KVORUM ÄR SERIELL</td> 
  <td rowspan="2">Stark</td> 
  <td rowspan="2">Lineariserbarhet</td> 
  <td>ALLA, KVORUM, SERIE</td> 
  <td>Stark</td> 
  <td>Lineariserbarhet</td> 
 </tr> 
 
 <tr>
 <td>LOCAL_ONE, EN, LOCAL_QUORUM, LOCAL_SERIAL, TVÅ, TRE</td>
  <td>Konsekvent prefix</td>
   <td>Global konsekvent Prefix</td>
 </tr>
 
 
 <tr> 
  <td rowspan="2">LOCAL_QUORUM, TRE, TVÅ, EN, LOCAL_ONE, ALLA</td> 
  <td rowspan="2">Konsekvent prefix </td> 
  <td rowspan="2">Global konsekvent Prefix </td> 
  <td>Alla</td> 
  <td>Stark</td> 
  <td>Lineariserbarhet</td> 
 </tr> 
 
 <tr>
 <td>LOCAL_ONE, EN, TVÅ, TRE, LOCAL_QUORUM, KVORUM</td>
  <td>Konsekvent prefix</td>
   <td>Global konsekvent Prefix</td>
 </tr>
 
 <tr> 
  <td rowspan="4">LOCAL_QUORUM, TRE, TVÅ</td> 
  <td rowspan="4">Begränsad föråldring</td> 
  <td rowspan="2">LOCAL_QUORUM, LOCAL_SERIAL, TVÅ, TRE</td> 
  <td rowspan="2">Begränsad föråldring</td> 
  <td rowspan="2">Begränsad föråldring.<br/>
I de flesta K versioner eller tiden t bakom.<br/>
Läs senaste allokerade värdet i regionen. 
</td> 
  
  <td>KVORUM, LOCAL_QUORUM, LOCAL_SERIAL, TVÅ, TRE</td> 
  <td>Begränsad föråldring</td> 
  <td>Begränsad föråldring.<br/>
I de flesta K versioner eller tiden t bakom. <br/>
Läs senaste allokerade värdet i regionen. </td> 
 </tr> 
 
 <tr>
 <td>LOCAL_ONE, EN</td>
  <td>Konsekvent prefix</td>
   <td>Per region konsekvent Prefix</td>
 </tr>
 
 
 <tr> 
  <td>EN LOCAL_ONE, ALLA</td> 
  <td>Konsekvent prefix </td> 
  <td >Per region konsekvent Prefix </td> 
  <td>LOCAL_ONE, EN, TVÅ, TRE, LOCAL_QUORUM, KVORUM</td> 
  <td>Konsekvent prefix</td> 
  <td>Per region konsekvent Prefix</td> 
 </tr> 
</table>

## <a id="mongo-mapping"></a>Mappning mellan MongoDB 3.4 och Azure Cosmos DB-konsekvensnivåer

I följande tabell visas ”Läs funderingar” mappningen mellan MongoDB 3.4 och standard-konsekvensnivå i Azure Cosmos DB. Tabellen visar flera regioner och en region.

| **MongoDB 3.4** | **Azure Cosmos DB (flera region)** | **Azure Cosmos DB (enskild region)** |
| - | - | - |
| Linearizable | Stark | Stark |
| Majoritet | Begränsad föråldring | Stark |
| Lokal | Konsekvent prefix | Konsekvent prefix |

## <a name="next-steps"></a>Nästa steg

Läs mer om konsekvensnivåer och kompatibiliteten mellan Azure Cosmos DB API: er med öppen källkod-API: er. Se följande artiklar:

* [Tillgänglighet och prestanda kompromisser för olika konsekvensnivåer](consistency-levels-tradeoffs.md)
* [MongoDB-funktioner som stöds av Azure Cosmos DB: s API för MongoDB](mongodb-feature-support.md)
* [Apache Cassandra-funktioner som stöds av Azure Cosmos DB Cassandra-API](cassandra-support.md)