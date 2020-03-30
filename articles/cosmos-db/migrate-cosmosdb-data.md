---
title: Migrera hundratals terabyte data till Azure Cosmos DB
description: Det här dokumentet beskriver hur du kan migrera 100-tals terabyte data till Cosmos DB
author: bharathsreenivas
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: bharathb
ms.openlocfilehash: 69b400eb7838c986ac6f275da58c7457179ebea6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72880210"
---
# <a name="migrate-hundreds-of-terabytes-of-data-into-azure-cosmos-db"></a>Migrera hundratals terabyte data till Azure Cosmos DB 

Azure Cosmos DB kan lagra flera terabyte data. Du kan utföra en storskalig datamigrering för att flytta din produktionsarbetsbelastning till Azure Cosmos DB. I den här artikeln beskriver vi utmaningarna med att flytta storskaliga data till Azure Cosmos DB och introducerar ett verktyg som hjälper dig med migreringen. I den här fallstudien använde kunden Cosmos DB SQL API.  

Innan du migrerar hela arbetsbelastningen till Azure Cosmos DB kan du migrera en delmängd av data för att validera några av de aspekter som partitionsnyckelval, frågeprestanda och datamodellering. När du har validerat konceptbeviset kan du flytta hela arbetsbelastningen till Azure Cosmos DB.  

## <a name="tools-for-data-migration"></a>Verktyg för datamigrering 

Azure Cosmos DB migreringsstrategier skiljer sig för närvarande baserat på API-valet och storleken på data. Om du vill migrera mindre datauppsättningar – för att validera datamodellering, frågeprestanda, partitionsnyckelval etc. – kan du välja [DataMigreringsverktyget](import-data.md) eller [Azure Data Factorys Azure Cosmos DB-anslutning](../data-factory/connector-azure-cosmos-db.md). Om du är bekant med Spark kan du också välja att använda [Azure Cosmos DB Spark-kopplingen](spark-connector.md) för att migrera data.

## <a name="challenges-for-large-scale-migrations"></a>Utmaningar för storskaliga migrationer 

De befintliga verktygen för att migrera data till Azure Cosmos DB har vissa begränsningar som blir särskilt uppenbara i stor skala:

 * **Begränsad skala ut funktioner:** För att migrera terabyte data till Azure Cosmos DB så snabbt som möjligt, och för att effektivt använda hela etablerat dataflöde, bör migreringsklienter ha möjlighet att skala ut på obestämd tid.  

* **Brist på förloppsspårning och kontrollpunkter**: Det är viktigt att spåra migrreringsförloppet och kontrollera samtidigt som stora datauppsättningar migreras. Annars stoppas migreringen när migreringen inträffar och du måste starta processen från början. Det skulle inte vara produktivt att starta om hela migreringen när 99 procent av den redan har slutförts.  

* **Brist på kö för död bokstav**: Inom stora datamängder kan det i vissa fall finnas problem med delar av källdata. Dessutom kan det finnas tillfälliga problem med klienten eller nätverket. Något av dessa fall bör inte orsaka att hela migreringen misslyckas. Även om de flesta migreringsverktyg har robusta funktioner för återförsök som skyddar mot återkommande problem, är det inte alltid tillräckligt. Om till exempel mindre än 0,01 % av källdatadokumenten är större än 2 MB i storlek, kommer det att orsaka att dokumentskrivningen misslyckas i Azure Cosmos DB. Helst är det användbart för migreringsverktyget att bevara dessa "misslyckade" dokument till en annan kö för obeställbara meddelanden, som kan bearbetas efter migreringen. 

Många av dessa begränsningar åtgärdas för verktyg som Azure Data factory, Azure Data Migration Services. 

## <a name="custom-tool-with-bulk-executor-library"></a>Anpassat verktyg med massutnämningsbibliotek 

De utmaningar som beskrivs i ovanstående avsnitt, kan lösas med hjälp av ett anpassat verktyg som enkelt kan skalas ut över flera instanser och det är motståndskraftigt mot tillfälliga fel. Dessutom kan det anpassade verktyget pausa och återuppta migreringen vid olika kontrollpunkter. Azure Cosmos DB tillhandahåller redan [det massutnämningsbibliotek](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-overview) som innehåller några av dessa funktioner. Massutfyllnadsbiblioteket har till exempel redan funktionen för att hantera tillfälliga fel och kan skala ut trådar i en enda nod för att förbruka cirka 500 K RU per nod. Bulk executor biblioteket partitionerar också källdatauppsättningen i mikrobatchar som drivs oberoende av dem som en form av kontrollpunkt.  

Det anpassade verktyget använder massutnjämnarbiblioteket och stöder skalning över flera klienter och för att spåra fel under inmatningsprocessen. Om du vill använda det här verktyget bör källdata delas in i olika filer i Azure Data Lake Storage (ADLS) så att olika migreringsarbetare kan plocka upp varje fil och förtära dem i Azure Cosmos DB. Det anpassade verktyget använder sig av en separat samling som lagrar metadata om migreringsförloppet för varje enskild källfil i ADLS och spårar eventuella fel som är associerade med dem.  

Följande bild beskriver migreringsprocessen med det här anpassade verktyget. Verktyget körs på en uppsättning virtuella datorer och varje virtuell dator frågar spårningssamlingen i Azure Cosmos DB för att skaffa ett lån på en av källdatapartitionerna. När detta är gjort läss källdatapartitionen av verktyget och intas i Azure Cosmos DB med hjälp av massutnämningsbiblioteket. Därefter uppdateras spårningssamlingen för att registrera förloppet för datainmatning och eventuella fel som påträffats. När en datapartition har bearbetats försöker verktyget fråga efter nästa tillgängliga källpartition. Den fortsätter att bearbeta nästa källpartition tills alla data migreras. Källkoden för verktyget finns [här](https://github.com/Azure-Samples/azure-cosmosdb-bulkingestion).  

 
![Inställningar för migreringsverktyg](./media/migrate-cosmosdb-data/migrationsetup.png)
 

 

Spårningssamlingen innehåller dokument som visas i följande exempel. Du kommer att se sådana dokument en för varje partition i källdata.  Varje dokument innehåller metadata för källdatapartitionen, till exempel dess plats, migreringsstatus och eventuella fel (om sådana finns):  

```json
{ 
  "owner": "25812@bulkimporttest07", 
  "jsonStoreEntityImportResponse": { 
    "numberOfDocumentsReceived": 446688, 
    "isError": false, 
    "totalRequestUnitsConsumed": 3950252.2800000003, 
    "errorInfo": [], 
    "totalTimeTakenInSeconds": 188, 
    "numberOfDocumentsImported": 446688 
  }, 
  "storeType": "AZURE_BLOB", 
  "name": "sourceDataPartition", 
  "location": "sourceDataPartitionLocation", 
  "id": "sourceDataPartitionId", 
  "isInProgress": false, 
  "operation": "unpartitioned-writes", 
  "createDate": { 
    "seconds": 1561667225, 
    "nanos": 146000000 
  }, 
  "completeDate": { 
    "seconds": 1561667515, 
    "nanos": 180000000 
  }, 
  "isComplete": true 
} 
```
 

## <a name="prerequisites-for-data-migration"></a>Förutsättningar för datamigrering 

Innan datamigreringen startar finns det några förutsättningar att tänka på:  

#### <a name="estimate-the-data-size"></a>Uppskatta datastorleken:  

Källdatastorleken kanske inte exakt mappas till datastorleken i Azure Cosmos DB. Några exempeldokument från källan kan infogas för att kontrollera deras datastorlek i Azure Cosmos DB. Beroende på exempeldokumentstorleken kan den totala datastorleken i Azure Cosmos DB efter migreringen uppskattas. 

Om till exempel varje dokument efter migreringen i Azure Cosmos DB är cirka 1 KB och om det finns cirka 60 miljarder dokument i källdatauppsättningen, skulle det innebära att den uppskattade storleken i Azure Cosmos DB skulle vara nära 60 TB. 

 

#### <a name="pre-create-containers-with-enough-rus"></a>Skapa behållare i föra med tillräckligt många ru: 

Även om Azure Cosmos DB skalar ut lagring automatiskt, är det inte tillrådligt att utgå från den minsta behållarstorleken. Mindre behållare har lägre dataflödestillgänglighet, vilket innebär att migreringen skulle ta mycket längre tid att slutföra. I stället är det användbart att skapa behållarna med den slutliga datastorleken (som beräknats i föregående steg) och se till att migreringsarbetsbelastningen förbrukar det etablerade dataflödet fullt ut.  

I föregående steg. Eftersom datastorleken uppskattades till cirka 60 TB krävs en behållare på minst 2,4 M RU:er för att rymma hela datauppsättningen.  

 

#### <a name="estimate-the-migration-speed"></a>Uppskatta migreringshastigheten: 

Förutsatt att migreringsarbetsbelastningen kan förbruka hela etablerat dataflöde, skulle den etablerade genomgående ge en uppskattning av migreringshastigheten. Fortsätter föregående exempel, 5 RU krävs för att skriva ett 1 KB-dokument till Azure Cosmos DB SQL API-konto.  2,4 miljoner ru skulle tillåta en överföring av 480.000 dokument per sekund (eller 480 MB / s). Det innebär att den fullständiga migreringen av 60 TB tar 125 000 sekunder eller cirka 34 timmar.  

Om du vill att migreringen ska slutföras inom en dag bör du öka det etablerade dataflödet till 5 miljoner ru: er. 

 

#### <a name="turn-off-the-indexing"></a>Stäng av indexeringen:  

Eftersom migreringen bör slutföras så snart som möjligt är det lämpligt att minimera tiden och ru:er som spenderas på att skapa index för vart och ett av de dokument som används.  Azure Cosmos DB indexerar automatiskt alla egenskaper, det är värt att minimera indexering till ett fåtal valda termer eller stänga av den helt för migreringen. Du kan inaktivera behållarens indexeringsprincip genom att ändra indexingMode till ingen som visas nedan:  

 
```
  { 
        "indexingMode": "none" 
  } 
```
 

När migreringen är klar kan du uppdatera indexeringen.  

## <a name="migration-process"></a>Migreringsprocessen 

När förutsättningarna har slutförts kan du migrera data med följande steg:  

1. Importera först data från källan till Azure Blob Storage. För att öka hastigheten på migreringen är det bra att parallellisera över olika källpartitioner. Innan migreringen påbörjas bör källdatauppsättningen delas upp i filer med storlek runt 200 MB storlek.   

2. Bulk körbiblioteket kan skala upp, att förbruka 500.000 RU:er i en enda klient VM. Eftersom det tillgängliga dataflödet är 5 miljoner RU:er bör 10 Ubuntu 16.04 virtuella datorer (Standard_D32_v3) etableras i samma region där din Azure Cosmos-databas finns. Du bör förbereda dessa virtuella datorer med migreringsverktyget och dess inställningsfil.  

3. Kör kösteget på en av klientens virtuella datorer. Det här steget skapar spårningssamlingen, som söker igenom ADLS-behållaren och skapar ett förloppsspårningsdokument för var och en av källdatauppsättningens partitionsfiler.  

4. Kör sedan importsteget på alla klient-virtuella datorer. Var och en av klienterna kan bli ägare på en källpartition och inta sina data i Azure Cosmos DB. När den är klar och dess status uppdateras i spårningssamlingen kan klienterna sedan fråga efter nästa tillgängliga källpartition i spårningssamlingen.  

5. Den här processen fortsätter tills hela uppsättningen källpartitioner förtärdes. När alla källpartitioner har bearbetats ska verktyget köras på felkorrigeringsläget i samma spårningssamling. Det här steget krävs för att identifiera källpartitioner som ska bearbetas på rätt sätt på grund av fel.  

6. Vissa av dessa fel kan bero på felaktiga dokument i källdata. Dessa bör identifieras och åtgärdas. Därefter bör du köra importsteget igen på de misslyckade partitionerna för att återdetärvält dem. 

När migreringen är klar kan du verifiera att antalet dokument i Azure Cosmos DB är samma som antalet dokument i källdatabasen. I det här exemplet visade sig den totala storleken i Azure Cosmos DB till 65 terabyte. Efter migrering kan indexering vara selektivt aktiverat och ru:erna kan sänkas till den nivå som krävs av arbetsbelastningens åtgärder.

## <a name="contact-the-azure-cosmos-db-team"></a>Kontakta Azure Cosmos DB-teamet
Även om du kan följa den här guiden för att migrera stora datauppsättningar till Azure Cosmos DB, för storskaliga migreringar, rekommenderar vi att du når ut azure Cosmos DB-produktteamet för att validera datamodelleringen och en allmän arkitekturgranskning. Baserat på din datauppsättning och arbetsbelastning kan produktteamet också föreslå andra prestanda- och kostnadsoptimeringar som kan vara tillämpliga på dig. Om du vill kontakta Azure Cosmos DB-teamet för hjälp med storskaliga migreringar kan du öppna en supportbiljett under problemtypen "Allmän rådgivning" och problemtypen "Stor (TB+) som visas nedan.

![Stödavsnitt för migrering](./media/migrate-cosmosdb-data/supporttopic.png)


## <a name="next-steps"></a>Nästa steg

* Läs mer genom att prova exempelprogrammen som använder massutdrivarbiblioteket i [.NET](bulk-executor-dot-net.md) och [Java](bulk-executor-java.md). 
* Massutnämningsbiblioteket är integrerat i Cosmos DB Spark-kopplingen, mer information finns i azure [cosmos DB Spark-anslutningsartikeln.](spark-connector.md)  
* Kontakta Azure Cosmos DB-produktteamet genom att öppna en supportbiljett under problemtypen "Allmän rådgivning" och "Stora (TB+) migreringar" för ytterligare hjälp med storskaliga migreringar. 
