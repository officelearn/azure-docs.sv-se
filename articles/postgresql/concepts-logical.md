---
title: Logisk avkodning-Azure Database for PostgreSQL-enskild server
description: Beskriver logisk avkodning och wal2json för registrering av ändrings data i Azure Database for PostgreSQL-enskild server
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 1213b38f2b67e8fed179cfda4308943808893e1b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80522152"
---
# <a name="logical-decoding"></a>Logisk avkodning
 
[Med logisk avkodning i postgresql](https://www.postgresql.org/docs/current/logicaldecoding.html) kan du strömma data ändringar till externa konsumenter. Logisk avkodning används ofta för händelse strömning och ändring av data insamlings scenarier.

Med den logiska avkodningen används ett output-pluginprogram för att konvertera postgres Write Ahead-logg (WAL) till ett läsbart format. Azure Database for PostgreSQL innehåller två plugin-program för utdata: [test_decoding](https://www.postgresql.org/docs/current/test-decoding.html) och [wal2json](https://github.com/eulerto/wal2json).
 

> [!NOTE]
> Logisk avkodning är i offentlig för hands version på Azure Database for PostgreSQL-enskild server.


## <a name="set-up-your-server"></a>Konfigurera servern
Om du vill börja använda logisk avkodning kan du aktivera servern för att spara och strömma WAL. 

1. Ställ in Azure. replication_support `logical` att använda Azure CLI. 
   ```
   az postgres server configuration set --resource-group mygroup --server-name myserver --name azure.replication_support --value logical
   ```

   > [!NOTE]
   > Om du använder Läs repliker kan Azure. replication_support inställd så `logical` att repliker kan köras. Om du slutar använda logisk avkodning ändrar du inställningen tillbaka till `replica`. 


2. Starta om servern för att tillämpa ändringarna.
   ```
   az postgres server restart --resource-group mygroup --name myserver
   ```

## <a name="start-logical-decoding"></a>Starta logisk avkodning

Logisk avkodning kan användas via direkt uppspelnings protokoll eller SQL-gränssnitt. Båda metoderna använder [replikerings-platser](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS). En plats representerar en data ström med ändringar från en enskild databas.

Om du använder en replikerings plats krävs postgres. Vid det här tillfället är replik privilegiet bara tillgängligt för serverns administratörs användare. 

### <a name="streaming-protocol"></a>Strömnings protokoll
Användning av ändringar med streaming-protokollet är ofta bättre. Du kan skapa en egen konsument/anslutning eller använda ett verktyg som [Debezium](https://debezium.io/). 

Besök wal2json-dokumentationen för [ett exempel som använder streaming-protokollet med pg_recvlogical](https://github.com/eulerto/wal2json#pg_recvlogical).


### <a name="sql-interface"></a>SQL-gränssnitt
I exemplet nedan använder vi SQL-gränssnittet med wal2json-plugin-programmet.
 
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

3. Förbrukar ändringarna.
   ```SQL
   SELECT data FROM pg_logical_slot_get_changes('test_slot', NULL, NULL, 'pretty-print', '1');
   ```

   Resultatet kommer att se ut så här:
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

4. Ta bort facket när du är färdig med den.
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```


## <a name="monitoring-slots"></a>Övervaknings platser

Du måste övervaka logisk avkodning. Eventuell oanvänd replikerings plats måste släppas. Platser är kvar på postgres WAL-loggar och relevanta system kataloger tills ändringar har lästs av en konsument. Om din konsument Miss lyckas eller inte har kon figurer ATS korrekt, kommer de oanvända loggarna att stapla och fylla lagringen. Oanvända loggar ökar också risken för transaktions-ID wraparound. Båda situationerna kan orsaka att servern blir otillgänglig. Därför är det viktigt att logiska replikerings platser konsumeras kontinuerligt. Om ingen logisk replikerings plats används tar du bort den direkt.

Kolumnen "aktiv" i vyn pg_replication_slots visar om det finns en konsument som är ansluten till en plats.
```SQL
SELECT * FROM pg_replication_slots;
```

[Ange aviseringar](howto-alert-on-metric.md) för *använt lagrings utrymme* och *högsta fördröjning i repliker* mått för att meddela dig när värdena ökar de tidigare tröskelvärdena för normala perioder. 

> [!IMPORTANT]
> Du måste ta bort oanvända replikerings-platser. Om du inte gör det kan det leda till att servern inte är tillgänglig.

## <a name="how-to-drop-a-slot"></a>Så här släpper du en plats
Om du inte aktivt konsumerar en replikerings plats bör du släppa den.

Så här släpper du en replikerings-kortplats med namnet `test_slot` med hjälp av SQL:
```SQL
SELECT pg_drop_replication_slot('test_slot');
```

> [!IMPORTANT]
> Om du slutar använda logisk avkodning ändrar du Azure. replication_support tillbaka till `replica` eller `off`. WAL-informationen som behålls av `logical` är mer utförlig och bör inaktive ras när logisk avkodning inte används. 

 
## <a name="next-steps"></a>Nästa steg

* Besök postgres-dokumentationen om du vill [veta mer om logisk avkodning](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html).
* Kontakta [vårt team](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) om du har frågor om logisk avkodning.
* Läs mer om [Läs repliker](concepts-read-replicas.md).

