---
title: 'Snabbstart: Mata in data från Logstash i Azure Data Explorer'
description: I den här snabbstarten får du lära dig hur du matar in (läser in) data i Azure Data Explorer från Logstash
author: tamirkamara
ms.author: takamara
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 01/14/2019
ms.openlocfilehash: e0d81454e6036d09bb74af6f522063a1aed5fffe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60759375"
---
# <a name="quickstart-ingest-data-from-logstash-to-azure-data-explorer"></a>Snabbstart: Mata in data från Logstash i Azure Data Explorer

[Logstash](https://www.elastic.co/products/logstash) är en databearbetningspipeline för serversidan med öppen källkod som matar in data från många källor samtidigt, transformerar data och sedan skickar data till din favoritlagring. I den här snabbstarten skickar du dessa data till Azure Data Explorer, som är en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata. Först skapar du en tabell och datamappning i ett testkluster, och sedan instruerar du Logstash att skicka data till tabellen och verifiera resultatet.

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* Ett Azure Data Explorer-[testkluster och en databas](create-cluster-database-portal.md)
* [Installationsanvisningar](https://www.elastic.co/guide/en/logstash/current/installing-logstash.html) för Logstash version 6+

## <a name="create-a-table"></a>Skapa en tabell

När du har ett kluster och en databas är det dags att skapa en tabell.

1. Kör följande kommando i databasfrågefönstret för att skapa en tabell:

    ```Kusto
    .create table logs (timestamp: datetime, message: string)
    ```

1. Kör följande kommando för att bekräfta att den nya tabellen `logs` har skapats och att den är tom:
    ```Kusto
    logs
    | count
    ```

## <a name="create-a-mapping"></a>Skapa en mappning

Mappning används av Azure Data Explorer för att omvandla inkommande data till måltabellschemat. Följande kommando skapar en ny mappning med namnet `basicmsg` som extraherar egenskaper från inkommande json enligt vad som anges av `path` och matar ut dem till `column`.

Kör följande kommando i frågefönstret:

```Kusto
.create table logs ingestion json mapping 'basicmsg' '[{"column":"timestamp","path":"$.@timestamp"},{"column":"message","path":"$.message"}]'
```

## <a name="install-the-logstash-output-plugin"></a>Installera plugin-programmet för Logstash-utdata

Plugin-programmet för Logstash-utdata kommunicerar med Azure Data Explorer och skickar data till tjänsten.
Kör följande kommando i Logstash-rotkatalogen för att installera plugin-programmet:

```sh
bin/logstash-plugin install logstash-output-kusto
```

## <a name="configure-logstash-to-generate-a-sample-dataset"></a>Konfigurera Logstash att generera en exempeldatamängd

Logstash kan generera exempelhändelser som kan användas för att testa en slutpunkt till slutpunkt-pipeline.
Om du redan använder Logstash och har åtkomst till din egen händelseström hoppar du över det här och går vidare till nästa avsnitt. 

> [!NOTE]
> Om du använder dina egna data ändrar du de tabell- och mappningsobjekt som definieras i föregående steg.

1. Redigera en ny textfil som innehåller de nödvändiga pipeline-inställningarna (med hjälp av vi):

    ```sh
    vi test.conf
    ```

1. Klistra in följande inställningar som instruerar Logstash att generera 1 000 testhändelser:

    ```ruby
    input {
        stdin { }
        generator {
            message => "Test Message 123"
            count => 1000
        }
    }
    ```

Den här konfigurationen innehåller också plugin-programmet för `stdin`-indata som gör att du kan skriva fler meddelanden själv (se till att använda *RETUR* för att skicka dem till pipelinen).

## <a name="configure-logstash-to-send-data-to-azure-data-explorer"></a>Konfigurera Logstash att skicka data till Azure Data Explorer

Klistra in följande inställningar i samma konfigurationsfil som används i föregående steg. Ersätt alla platshållarna med relevanta värden för din konfiguration. Mer information finns i avsnittet om att [skapa ett AAD-program](/azure/kusto/management/access-control/how-to-provision-aad-app). 

```ruby
output {
    kusto {
            path => "/tmp/kusto/%{+YYYY-MM-dd-HH-mm-ss}.txt"
            ingest_url => "https://ingest-<cluster name>.kusto.windows.net/"
            app_id => "<application id>"
            app_key => "<application key/secret>"
            app_tenant => "<tenant id>"
            database => "<database name>"
            table => "<target table>" # logs as defined above
            mapping => "<mapping name>" # basicmsg as defined above
    }
}
```

| Parameternamn | Beskrivning |
| --- | --- |
| **path** | Logstash-plugin-programmet skriver händelser till tillfälliga filer innan de skickas till Azure Data Explorer. Den här parametern innehåller en sökväg dit filerna ska skrivas och ett tidsuttryck för filrotation för att utlösa en uppladdning till Azure Data Explorer-tjänsten.|
| **ingest_url** | Kusto-slutpunkten för inmatningsrelaterad kommunikation.|
| **app_id**, **app_key** och **app_tenant**| Autentiseringsuppgifter som krävs för att ansluta till Azure Data Explorer. Se till att använda ett program med inmatningsbehörighet. |
| **database**| Databasnamnet för att placera händelser. |
| **table** | Måltabellnamnet för att placera händelser. |
| **mapping** | Mappning används för att mappa json-strängen för en inkommande händelse till rätt radformat (definierar vilken egenskap som hamnar i vilken kolumn). |

## <a name="run-logstash"></a>Köra Logstash

Vi är nu redo att köra Logstash och testa inställningarna.

1. I Logstash-rotkatalogen kör du följande kommando:

    ```sh
    bin/logstash -f test.conf
    ```

    Du bör se information som visas på skärmen och sedan de 1 000 meddelanden som genereras av exempelkonfigurationen. Nu kan du även ange fler meddelanden manuellt.

1. Efter några minuter kör du följande Data Explorer-fråga för att se meddelandena i den tabell som du definierade:

    ```Kusto
    logs
    | order by timestamp desc
    ```

1. Tryck på Ctrl + C för att avsluta Logstash

## <a name="clean-up-resources"></a>Rensa resurser

Kör du följande kommando i databasen för att rensa tabellen `logs`:

```Kusto
.drop table logs
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skriv frågor](write-queries.md)