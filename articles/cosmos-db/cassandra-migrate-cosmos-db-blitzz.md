---
title: Migrera data från Cassandra till Azure Cosmos DB Cassandra API med Blitzz
description: Lär dig hur du migrerar data från Apache Cassandra-databasen till Azure Cosmos DB Cassandra API med Blitzz.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 746232a85e326f08d44cf2dbe0a4ae718c7cc312
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "69984373"
---
# <a name="migrate-data-from-cassandra-to-azure-cosmos-db-cassandra-api-account-using-blitzz"></a>Migrera data från Cassandra till Azure Cosmos DB Cassandra API-konto med Blitzz

Cassandra API i Azure Cosmos DB har blivit ett utmärkt val för företagsarbetsbelastningar som körs på Apache Cassandra av olika skäl, till exempel: 

* **Inga omkostnader för hantering och övervakning:** Det eliminerar omkostnaderna för att hantera och övervaka en myriad av inställningar över OS, JVM och Yaml-filer och deras interaktioner.

* **Betydande kostnadsbesparingar:** Du kan spara kostnader med Azure Cosmos DB, som inkluderar kostnaden för VM, bandbredd och eventuella tillämpliga licenser. Dessutom behöver du inte hantera datacenter, servrar, SSD-lagring, nätverk och elkostnader. 

* **Möjlighet att använda befintlig kod och verktyg:** Azure Cosmos DB tillhandahåller kompatibilitet på trådprotokollnivå med befintliga Cassandra SDK:er och verktyg. Den här kompatibiliteten garanterar att du kan använda din befintliga kodbas med Cassandra-API:et för Azure Cosmos DB med minimala ändringar.

Det finns olika sätt att migrera databasarbetsbelastningar från en plattform till en annan. [Blitzz](https://www.blitzz.io) är ett verktyg som erbjuder ett säkert och tillförlitligt sätt att utföra noll driftstoppsmigrering från en mängd olika databaser till Azure Cosmos DB. I den här artikeln beskrivs de steg som krävs för att migrera data från Apache Cassandra-databasen till Azure Cosmos DB Cassandra API med Blitzz.

## <a name="benefits-using-blitzz-for-migration"></a>Fördelar med att använda Blitzz för migration

Blitzz migreringslösning följer en steg för steg-strategi för att migrera komplexa operativa arbetsbelastningar. Följande är några av de viktigaste aspekterna av Blitzz noll-driftstopp migration plan:

* Det erbjuder automatisk migrering av affärslogik (tabeller, index, vyer) från Apache Cassandra-databasen till Azure Cosmos DB. Du behöver inte skapa scheman manuellt.

* Blitzz erbjuder högvolyms- och parallell databasreplikering. Det gör att både käll- och målplattformarna kan vara synkroniserade under migreringen med hjälp av en teknik som kallas Change-Data-Capture (CDC). Genom att använda CDC hämtar Blitzz kontinuerligt en ström av ändringar från källdatabasen (Apache Cassandra) och tillämpar den på måldatabasen (Azure Cosmos DB).

* Det är feltolerant och garanterar exakt en gång leverans av data även under ett maskinvaru- eller programvarufel i systemet.

* Det säkrar data under överföringen med hjälp av en mängd olika säkerhetsmetoder som SSL, kryptering.

## <a name="steps-to-migrate-data"></a>Åtgärder för att migrera data

I det här avsnittet beskrivs de steg som krävs för att konfigurera Blitzz och migrerar data från Apache Cassandra-databasen till Azure Cosmos DB.

1. Lägg till ett säkerhetscertifikat från datorn där du planerar att installera Blitzz-replikanten. Det här certifikatet krävs av Blitzz-replikaten för att upprätta en SSL-anslutning med det angivna Azure Cosmos DB-kontot. Du kan lägga till certifikatet med följande steg:

   ```bash
   wget https://cacert.omniroot.com/bc2025.crt
   mv bc2025.crt bc2025.cer
   keytool -keystore $JAVA_HOME/lib/security/cacerts -importcert -alias bc2025ca -file bc2025.cer
   ```

1. Du kan få Blitzz installation och binära filer antingen genom att begära en demo på [Blitzz hemsida](https://www.blitzz.io). Alternativt kan du också skicka ett [e-postmeddelande](mailto:success@blitzz.io) till teamet.

   ![Blitzz replicant verktyg nedladdning](./media/cassandra-migrate-cosmos-db-blitzz/blitzz-replicant-download.png)

   ![Blitzz replicant filer](./media/cassandra-migrate-cosmos-db-blitzz/replicant-files.png)

1. Ställ in källdatabaskonfigurationen från CLI-terminalen. Öppna konfigurationsfilen **`vi conf/conn/cassandra.yml`** med kommandot och lägg till en kommaavgränsad lista med IP-adresser till Cassandra-noderna, portnummer, användarnamn, lösenord och annan nödvändig information. Följande är ett exempel på innehåll i konfigurationsfilen:

   ```bash
   type: CASSANDRA
  
   host: 172.17.0.2
   port: 9042

   username: 'cassandra'
   password: 'cassandra'

   max-connections: 30

   ```

   ![Öppna Cassandra-anslutningsredigeraren](./media/cassandra-migrate-cosmos-db-blitzz/open-connection-editor-cassandra.png)

   ![Konfiguration av Cassandra-anslutning](./media/cassandra-migrate-cosmos-db-blitzz/cassandra-connection-configuration.png)

   När du har fyllt i konfigurationsinformationen sparar och stänger du filen.

1. Du kan också ställa in källdatabasfilen. Filterfilen anger vilka scheman eller tabeller som ska migreras. Öppna konfigurationsfilen **`vi filter/cassandra_filter.yml`** med kommandot och ange följande konfigurationsinformation:

   ```bash

   allow:
   -    schema: “io_blitzz”
   Types: [TABLE]
   ```

   När du har fyllt i information om databasfilter sparar och stänger du filen.

1. Därefter ställer du in måldatabaskonfigurationen. Innan du definierar konfigurationen [skapar du ett Azure Cosmos DB Cassandra API-konto](create-cassandra-dotnet.md#create-a-database-account) och skapar sedan ett Keyspace och en tabell för att lagra migrerade data. Eftersom du migrerar från Apache Cassandra till Cassandra API i Azure Cosmos DB kan du använda samma partitionsnyckel som du har använt med Apache cassandra.

1. Innan du migrerar data ökar du behållardataflödet till det belopp som krävs för att programmet ska kunna migrera snabbt. Du kan till exempel öka dataflödet till 100000 RU:er. Genom att skala dataflödet innan migreringen startas hjälper dig att migrera dina data på kortare tid.

   ![Skala Azure Cosmos-behållaren i hela](./media/cassandra-migrate-cosmos-db-blitzz/scale-throughput.png)

   Minska dataflödet när migreringen är klar. Baserat på mängden data som lagras och ru:er som krävs för varje åtgärd kan du uppskatta dataflödet som krävs efter datamigrering. Mer information om hur du uppskattar de ru:er som krävs finns i [Etablera dataflöde på behållare och databaser](set-throughput.md) och uppskatta RU/s med hjälp av azure [Cosmos DB-kapacitetsplanerare.](estimate-ru-with-capacity-planner.md)

1. Hämta **kontaktpunkten, porten, användarnamnet**och **det primära lösenordet för** ditt Azure Cosmos-konto från fönstret **Anslutningssträng.** Du kommer att använda dessa värden i konfigurationsfilen.

1. Ställ in måldatabaskonfigurationen från CLI-terminalen. Öppna konfigurationsfilen **`vi conf/conn/cosmosdb.yml`** med kommandot och lägg till en kommaavgränsad lista över värd-URI, portnummer, användarnamn, lösenord och andra nödvändiga parametrar. I följande exempel visas innehållet i konfigurationsfilen:

   ```bash
   type: COSMOSDB

   host: '<Azure Cosmos account’s Contact point>'
   port: 10350

   username: 'blitzzdemo'
   password: '<Your Azure Cosmos account’s primary password>'

   max-connections: 30
   ```

1. Nästa migrera data med Blitzz. Du kan köra Blizz-replikanten i **hel-** eller **snapshot-läge:**

   * **Fullständigt läge** – I det här läget fortsätter replikanten att köras efter migreringen och lyssnar efter eventuella ändringar på källan Apache Cassandra-systemet. Om den upptäcker några ändringar replikeras de på azure Cosmos-kontot i realtid.

   * **Snapshot-läge** – I det här läget kan du utföra schemamigrering och engångsdatareplikering. Replikering i realtid stöds inte med det här alternativet.

   Genom att använda ovanstående två lägen kan migrering utföras med noll driftstopp. 

1. Om du vill migrera data från Blitzz-replikerade CLI-terminalen kör du följande kommando:

   ```bash
   ./bin/replicant full conf/conn/cassandra.yaml conf/conn/cosmosdb.yaml --filter filter/cassandra_filter.yaml --replace-existing
   ```

   Det replikniga användargränssnittet visar replikeringsstatusen. När schemamigrering och ögonblicksbildåtgärd är klar visar förloppet 100 %. När migreringen är klar kan du validera data på azure Cosmos-databasen för målet.

   ![Cassandra-datamigreringsutdata](./media/cassandra-migrate-cosmos-db-blitzz/cassandra-data-migration-output.png)


1. Eftersom du har använt fullständigt läge för migrering kan du utföra åtgärder som att infoga, uppdatera eller ta bort data i apache cassandra-databasen. Senare verifiera att de replikeras i realtid på målet Azure Cosmos-databasen. Efter migreringen, se till att minska dataflödet som konfigurerats för din Azure Cosmos-behållare.

1. Du kan stoppa repliken valfri punkt och starta om den med **växeln --resume.** Replikeringen återupptas från den punkt den har stoppats utan att kompromissa med datakonsekvensen. Följande kommando visar hur du använder återställningsväxeln.

   ```bash
   ./bin/replicant full conf/conn/cassandra.yaml conf/conn/cosmosdb.yaml --filter filter/cassandra_filter.yaml --replace-existing --resume
   ```

Mer information om datamigrering till mål, migrering i realtid finns i [Blitzz replicant-demon](https://www.youtube.com/watch?v=fsUhF9LUZmM).

## <a name="next-steps"></a>Nästa steg

* [Etablera dataflöde på containrar och databaser](set-throughput.md) 
* [Metodtips för partitionsnyckel](partitioning-overview.md#choose-partitionkey)
* [Uppskatta RU/s med hjälp av azure cosmos DB-kapacitetsplanerare artiklar](estimate-ru-with-capacity-planner.md)