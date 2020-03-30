---
title: Migrera data från Oracle till Azure Cosmos DB Cassandra API med Blitzz
description: Lär dig hur du migrerar data från Oracle-databasen till Azure Cosmos DB Cassandra API med Blitzz.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: fe132ee6ab90fdae99463f11ecf46f352690b810
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "69984360"
---
# <a name="migrate-data-from-oracle-to-azure-cosmos-db-cassandra-api-account-using-blitzz"></a>Migrera data från Oracle till Azure Cosmos DB Cassandra API-konto med Blitzz

Cassandra API i Azure Cosmos DB har blivit ett utmärkt val för företagsarbetsbelastningar som körs på Oracle av olika skäl, till exempel:

* **Bättre skalbarhet och tillgänglighet:** Det eliminerar enskilda felpunkter, bättre skalbarhet och tillgänglighet för dina program.

* **Betydande kostnadsbesparingar:** Du kan spara kostnader med Azure Cosmos DB, som inkluderar kostnaden för VM, bandbredd och eventuella tillämpliga Oracle-licenser. Dessutom behöver du inte hantera datacenter, servrar, SSD-lagring, nätverk och elkostnader.

* **Inga omkostnader för hantering och övervakning:** Som en fullständigt hanterad molntjänst tar Azure Cosmos DB bort kostnaderna för att hantera och övervaka en myriad av inställningar.

Det finns olika sätt att migrera databasarbetsbelastningar från en plattform till en annan. [Blitzz](https://www.blitzz.io) är ett verktyg som erbjuder ett säkert och tillförlitligt sätt att utföra noll driftstoppsmigrering från en mängd olika databaser till Azure Cosmos DB. I den här artikeln beskrivs de steg som krävs för att migrera data från Oracle-databasen till Azure Cosmos DB Cassandra API med Blitzz.

## <a name="benefits-using-blitzz-for-migration"></a>Fördelar med att använda Blitzz för migration

Blitzz migreringslösning följer en steg för steg-strategi för att migrera komplexa operativa arbetsbelastningar. Följande är några av de viktigaste aspekterna av Blitzz noll-driftstopp migration plan:

* Det erbjuder automatisk migrering av affärslogik (tabeller, index, vyer) från Oracle-databas till Azure Cosmos DB. Du behöver inte skapa scheman manuellt.

* Blitzz erbjuder högvolyms- och parallell databasreplikering. Det gör att både käll- och målplattformarna kan vara synkroniserade under migreringen med hjälp av en teknik som kallas Change-Data-Capture (CDC). Genom att använda CDC hämtar Blitzz kontinuerligt en ström av ändringar från källdatabasen(Oracle) och tillämpar den på måldatabasen (Azure Cosmos DB).

* Det är feltolerant och garanterar exakt en gång leverans av data även under ett maskinvaru- eller programvarufel i systemet.

* Det säkrar data under överföringen med hjälp av en mängd olika säkerhetsmetoder som SSL, kryptering.

* Det erbjuder tjänster för att konvertera komplex affärslogik skriven i PL/SQL till motsvarande affärslogik i Azure Cosmos DB.

## <a name="steps-to-migrate-data"></a>Åtgärder för att migrera data

I det här avsnittet beskrivs de steg som krävs för att konfigurera Blitzz och migrerar data från Oracle-databasen till Azure Cosmos DB.

1. Lägg till ett säkerhetscertifikat från datorn där du planerar att installera Blitzz-replikanten. Det här certifikatet krävs av Blitzz-replikaten för att upprätta en SSL-anslutning med det angivna Azure Cosmos DB-kontot. Du kan lägga till certifikatet med följande steg:

   ```bash
   wget https://cacert.omniroot.com/bc2025.crt
   mv bc2025.crt bc2025.cer
   keytool -keystore $JAVA_HOME/lib/security/cacerts -importcert -alias bc2025ca -file bc2025.cer
   ```

1. ou kan få Blitzz installation och binära filer antingen genom att begära en demo på [Blitzz hemsida](https://www.blitzz.io). Alternativt kan du också skicka ett [e-postmeddelande](mailto:success@blitzz.io) till teamet.

   ![Blitzz replicant verktyg nedladdning](./media/oracle-migrate-cosmos-db-blitzz/blitzz-replicant-download.png)

   ![Blitzz replicant filer](./media/oracle-migrate-cosmos-db-blitzz/replicant-files.png)

1. Ställ in källdatabaskonfigurationen från CLI-terminalen. Öppna konfigurationsfilen **`vi conf/conn/oracle.yml`** med kommandot och lägg till en kommaavgränsad lista med IP-adresser för oracle-noderna, portnummer, användarnamn, lösenord och annan nödvändig information. Följande kod visar en exempelkonfigurationsfil:

   ```bash
   type: ORACLE

   host: localhost
   port: 53546

   service-name: IO

   username: '<Username of your Oracle database>'
   password: '<Password of your Oracle database>'

   conn-cnt: 30
   use-ssl: false
   ```

   ![Öppna Oracle-anslutningsredigeraren](./media/oracle-migrate-cosmos-db-blitzz/open-connection-editor-oracle.png)

   ![Oracle-anslutningskonfiguration](./media/oracle-migrate-cosmos-db-blitzz/oracle-connection-configuration.png)

   När du har fyllt i konfigurationsinformationen sparar och stänger du filen.

1. Du kan också ställa in källdatabasfilen. Filterfilen anger vilka scheman eller tabeller som ska migreras. Öppna konfigurationsfilen **`vi filter/oracle_filter.yml`** med kommandot och ange följande konfigurationsinformation:

   ```bash

   allow:
   -    schema: “io_blitzz”
   Types: [TABLE]
   ```
 
   När du har fyllt i information om databasfilter sparar och stänger du filen.

1. Därefter ställer du in konfigurationen av måldatabasen. Innan du definierar konfigurationen [skapar du ett Azure Cosmos DB Cassandra API-konto](create-cassandra-dotnet.md#create-a-database-account). [Välj rätt partitionsnyckel](partitioning-overview.md#choose-partitionkey) från dina data och skapa sedan ett Keyspace och en tabell för att lagra migrerade data.

1. Innan du migrerar data ökar du behållardataflödet till det belopp som krävs för att programmet ska kunna migrera snabbt. Du kan till exempel öka dataflödet till 100000 RU:er. Genom att skala dataflödet innan migreringen startas hjälper dig att migrera dina data på kortare tid. 

   ![Skala Azure Cosmos-behållaren i hela](./media/oracle-migrate-cosmos-db-blitzz/scale-throughput.png)

   Du måste minska dataflödet när migreringen har slutförts. Baserat på mängden data som lagras och ru:er som krävs för varje åtgärd kan du uppskatta dataflödet som krävs efter datamigrering. Mer information om hur du uppskattar de ru:er som krävs finns i [Etablera dataflöde på behållare och databaser](set-throughput.md) och uppskatta RU/s med hjälp av azure [Cosmos DB-kapacitetsplanerare.](estimate-ru-with-capacity-planner.md)

1. Hämta **kontaktpunkten, porten, användarnamnet**och **det primära lösenordet för** ditt Azure Cosmos-konto från fönstret **Anslutningssträng.** Du kommer att använda dessa värden i konfigurationsfilen.

1. Ställ in måldatabaskonfigurationen från CLI-terminalen. Öppna konfigurationsfilen **`vi conf/conn/cosmosdb.yml`** med kommandot och lägg till en kommaavgränsad lista över värd-URI, portnummer, användarnamn, lösenord och andra nödvändiga parametrar. Följande är ett exempel på innehåll i konfigurationsfilen:

   ```bash
   type: COSMOSDB

   host: `<Azure Cosmos account’s Contact point>`
   port: 10350

   username: 'blitzzdemo'
   password: `<Your Azure Cosmos account’s primary password>'

   max-connections: 30
   use-ssl: false
   ```

1. Nästa migrera data med Blitzz. Du kan köra Blizz-replikanten i **hel-** eller **snapshot-läge:**

   * **Fullständigt läge** – I det här läget fortsätter replikanten att köras efter migreringen och lyssnar efter eventuella ändringar på källan Oracle-systemet. Om den upptäcker några ändringar replikeras de på azure Cosmos-kontot i realtid.

   * **Snapshot-läge** – I det här läget kan du utföra schemamigrering och engångsdatareplikering. Replikering i realtid stöds inte med det här alternativet.


   Genom att använda ovanstående två lägen kan migrering utföras med noll driftstopp.

1. Om du vill migrera data från Blitzz-replikerade CLI-terminalen kör du följande kommando:

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing
   ```

   Det replikniga användargränssnittet visar replikeringsstatusen. När schemamigrering och ögonblicksbildåtgärd är klar visar förloppet 100 %. När migreringen är klar kan du validera data på azure Cosmos-databasen för målet.

   ![Oracle-datamigreringsutdata](./media/oracle-migrate-cosmos-db-blitzz/oracle-data-migration-output.png)

1. Eftersom du har använt fullständigt läge för migrering kan du utföra åtgärder som att infoga, uppdatera eller ta bort data i Oracle-källdatabasen. Senare kan du verifiera att de replikeras i realtid på azure Cosmos-databasen för målet. Efter migreringen, se till att minska dataflödet som konfigurerats för din Azure Cosmos-behållare.

1. Du kan stoppa repliken valfri punkt och starta om den med **växeln --resume.** Replikeringen återupptas från den punkt den har stoppats utan att kompromissa med datakonsekvensen. Följande kommando visar hur du använder återställningsväxeln.

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing --resume
   ```

Mer information om datamigrering till mål, migrering i realtid finns i [Blitzz replicant-demon](https://www.youtube.com/watch?v=y5ZeRK5A-MI).

## <a name="next-steps"></a>Nästa steg

* [Etablera dataflöde på containrar och databaser](set-throughput.md) 
* [Metodtips för partitionsnyckel](partitioning-overview.md#choose-partitionkey)
* [Uppskatta RU/s med hjälp av azure cosmos DB-kapacitetsplanerare artiklar](estimate-ru-with-capacity-planner.md)