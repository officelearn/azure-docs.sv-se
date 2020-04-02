---
title: Logisk avkodning – Azure-databas för PostgreSQL - Single Server
description: Beskriver logiskt avkodning och wal2json för ändringsdatainsamling i Azure Database for PostgreSQL - Single Server
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 1213b38f2b67e8fed179cfda4308943808893e1b
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522152"
---
# <a name="logical-decoding"></a>Logisk avkodning
 
[Med logisk avkodning i PostgreSQL](https://www.postgresql.org/docs/current/logicaldecoding.html) kan du strömma dataändringar till externa konsumenter. Logisk avkodning används populärt för händelseströmning och ändring av datainsamlingsscenarier.

Logisk avkodning använder en utdata plugin för att konvertera Postgres skriva framåt logg (WAL) till ett läsbart format. Azure Database för PostgreSQL innehåller två plugins för utdata: [test_decoding](https://www.postgresql.org/docs/current/test-decoding.html) och [wal2json](https://github.com/eulerto/wal2json).
 

> [!NOTE]
> Logisk avkodning är i offentlig förhandsversion på Azure Database för PostgreSQL - Single Server.


## <a name="set-up-your-server"></a>Konfigurera servern
Om du vill börja använda logisk avkodning aktiverar du servern för att spara och strömma WAL.To start using logical avcoding, enable your server to save and stream the WAL. 

1. Ange azure.replication_support till `logical` att använda Azure CLI. 
   ```
   az postgres server configuration set --resource-group mygroup --server-name myserver --name azure.replication_support --value logical
   ```

   > [!NOTE]
   > Om du använder läsrepliker, azure.replication_support `logical` inställd på att också tillåter repliker att köras. Om du slutar använda logisk avkodning `replica`ändrar du tillbaka inställningen till . 


2. Starta om servern för att tillämpa ändringarna.
   ```
   az postgres server restart --resource-group mygroup --name myserver
   ```

## <a name="start-logical-decoding"></a>Starta logiskt avkodning

Logisk avkodning kan förbrukas via streamingprotokoll eller SQL-gränssnitt. Båda metoderna använder [replikeringsplatser](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS). En plats representerar en ström av ändringar från en enda databas.

Om du använder en replikeringsplats krävs Postgres replikeringsbehörighet. För närvarande är replikeringsbehörigheten endast tillgänglig för serverns administratörsanvändare. 

### <a name="streaming-protocol"></a>Protokoll för direktuppspelning
Att konsumera ändringar med hjälp av direktuppspelningsprotokollet är ofta att föredra. Du kan skapa din egen konsument / kontakt, eller använda ett verktyg som [Debezium](https://debezium.io/). 

Besök wal2json-dokumentationen i [ett exempel med hjälp av streamingprotokollet med pg_recvlogical](https://github.com/eulerto/wal2json#pg_recvlogical).


### <a name="sql-interface"></a>SQL-gränssnitt
I exemplet nedan använder vi SQL-gränssnittet med wal2json plugin.
 
1. Skapa en plats.
   ```SQL
   SELECT * FROM pg_create_logical_replication_slot('test_slot', 'wal2json');
   ```
 
2. Utfärda SQL-kommandon. Ett exempel:
   ```SQL
   CREATE TABLE a_table (
      id varchar(40) NOT NULL,
      item varchar(40),
      PRIMARY KEY (id)
   );
   
   INSERT INTO a_table (id, item) VALUES ('id1', 'item1');
   DELETE FROM a_table WHERE id='id1';
   ```

3. Konsumera ändringarna.
   ```SQL
   SELECT data FROM pg_logical_slot_get_changes('test_slot', NULL, NULL, 'pretty-print', '1');
   ```

   Utdata kommer att se ut:
   ```
   {
         "change": [
         ]
   }
   {
         "change": [
                  {
                           "kind": "insert",
                           "schema": "public",
                           "table": "a_table",
                           "columnnames": ["id", "item"],
                           "columntypes": ["character varying(40)", "character varying(40)"],
                           "columnvalues": ["id1", "item1"]
                  }
         ]
   }
   {
         "change": [
                  {
                           "kind": "delete",
                           "schema": "public",
                           "table": "a_table",
                           "oldkeys": {
                                 "keynames": ["id"],
                                 "keytypes": ["character varying(40)"],
                                 "keyvalues": ["id1"]
                           }
                  }
         ]
   }
   ```

4. Släpp facket när du är klar med den.
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```


## <a name="monitoring-slots"></a>Övervakning av ankomst- och avgångstider

Du måste övervaka logiska avkodning. Oanvänd replikeringsplats måste släppas. Slots håller fast vid Postgres WAL-loggar och relevanta systemkataloger tills ändringar har lästs av en konsument. Om din konsument misslyckas eller inte har konfigurerats korrekt samlas de okonsumerade loggarna och fyller lagringsutrymmet. Dessutom ökar oomräknade loggar risken för transaktions-ID wraparound. Båda situationerna kan leda till att servern blir otillgänglig. Därför är det viktigt att logiska replikeringsplatser förbrukas kontinuerligt. Om en logisk replikeringsplats inte längre används släpper du den omedelbart.

Kolumnen "aktiv" i pg_replication_slots visar om det finns en konsument som är ansluten till en plats.
```SQL
SELECT * FROM pg_replication_slots;
```

[Ange aviseringar](howto-alert-on-metric.md) för *lagring som används* och Max fördröjning över *replikermått* för att meddela dig när värdena ökar tidigare normala tröskelvärden. 

> [!IMPORTANT]
> Du måste släppa oanvända replikeringsplatser. Om du inte gör det kan det leda till otillgänglighet för servern.

## <a name="how-to-drop-a-slot"></a>Hur man släpper en plats
Om du inte aktivt förbrukar en replikeringsplats bör du släppa den.

Så här släpper `test_slot` du en replikeringsplats som anropas med SQL:
```SQL
SELECT pg_drop_replication_slot('test_slot');
```

> [!IMPORTANT]
> Om du slutar använda logisk avkodning ändrar du `replica` `off`azure.replication_support tillbaka till eller . WAL-detaljerna som `logical` behålls av är mer utförliga och bör inaktiveras när logisk avkodning inte används. 

 
## <a name="next-steps"></a>Nästa steg

* Besök Postgres dokumentation om du vill [veta mer om logiska avkodning](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html).
* Kontakta [vårt team](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) om du har frågor om logiska avkodning.
* Läs mer om [läsrepliker](concepts-read-replicas.md).

