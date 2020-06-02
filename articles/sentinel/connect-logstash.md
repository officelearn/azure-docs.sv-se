---
title: Anslut data källor via Logstash till Azure Sentinel | Microsoft Docs
description: Lär dig hur du använder Logstash för att vidarebefordra loggar från externa data källor till Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2020
ms.author: yelevin
ms.openlocfilehash: 5edba7f5a7576cac189aa44936f9d8f24f3ffcaf
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84249015"
---
# <a name="use-logstash-to-connect-data-sources-to-azure-sentinel"></a>Använd Logstash för att ansluta data källor till Azure Sentinel

> [!IMPORTANT]
> Data inmatning med Logstash output-plugin-programmet är för närvarande en offentlig för hands version. Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Med hjälp av Azure Sentinels nya plugin-program för **data insamlings motorn för Logstash**kan du nu skicka vilken typ av logg du vill via Logstash direkt till din Log Analytics arbets yta i Azure Sentinel. Loggarna skickas till en anpassad tabell som du definierar med hjälp av plugin-programmet för utdata.

Mer information om hur du arbetar med Logstash data insamlings motor finns i [komma igång med Logstash](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html).

## <a name="overview"></a>Översikt

### <a name="architecture-and-background"></a>Arkitektur och bakgrund

   ![Logstash-arkitektur](./media/connect-logstash/logstash-architecture.png)

Logstash-motorn består av tre komponenter:

- Plugin-program för indata – anpassad insamling av data från olika källor
- Filtrera plugin-program – manipulering och normalisering av data enligt angivna villkor
- Utgående plugin-program – anpassad sändning av insamlade och bearbetade data till olika destinationer

> [!NOTE]
> Azure Sentinel stöder endast det angivna plugin-programmet för utdata. Det stöder inte plugin-program från tredje part för Azure Sentinel eller något annat Logstash-plugin-program av valfri typ.

Plugin-programmet för Azure Sentinel-utdata för Logstash skickar JSON-formaterade data till din Log Analytics-arbetsyta med Log Analytics http-insamlaren REST API. Data matas in i anpassade loggar.

- [Läs mer om Log Analytics REST API](https://docs.microsoft.com/rest/api/loganalytics/create-request).
- [Läs mer om anpassade loggar](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-custom-logs).

## <a name="deploying-the-azure-sentinel-output-plugin-in-logstash"></a>Distribuera plugin-programmet för Azure Sentinel-utdata i Logstash

1. **Installation**

    Plugin-programmet för Azure Sentinel-utdata är tillgängligt i Logstash-samlingen.

    - Följ instruktionerna i Logstash [work with plugin](https://www.elastic.co/guide/en/logstash/current/working-with-plugins.html) -dokument för att installera plugin-programmet ***Microsoft-Logstash-output-Azure-loganalytics*** .
    - Om ditt Logstash-system inte har Internet åtkomst följer du anvisningarna i dokumentet Logstash [Offline-plugin-hantering](https://www.elastic.co/guide/en/logstash/current/offline-plugins.html) för att förbereda och använda ett offline-plugin-paket. (Detta kräver att du skapar ett annat Logstash-system med Internet åtkomst.)

1. **Konfiguration**

    Med hjälp av informationen i Logstash- [strukturen i ett konfigurations fil](https://www.elastic.co/guide/en/logstash/current/configuration-file-structure.html) dokument lägger du till plugin-programmet för Azure Sentinel-utdata i konfigurationen med följande nycklar och värden (se rätt syntax för config-filen under tabellen):

    | **Fältnamn** | **Datatyp** | **Beskrivning** |
    |----------------|---------------|-----------------|
    | `workspace_id` | sträng | Ange arbetsyte-ID-GUID |
    | `workspace_key` | sträng | Ange arbets ytans primära nyckel-GUID |
    | `custom_log_table_name` | sträng | Ange namnet på den tabell där loggarna ska matas in. Det går bara att konfigurera ett tabell namn per utdatafil-plugin-program. Tabellen logg visas i Azure Sentinel under **loggar**i **tabeller** i kategorin **anpassade loggar** med ett `_CL` suffix. |
    | `endpoint` | sträng | Valfritt fält. Som standard är detta Log Analytics slut punkten. Använd det här fältet om du vill ange en alternativ slut punkt. |
    | `time_generated_field` | sträng | Valfritt fält. Den här egenskapen åsidosätter standard fältet **TimeGenerated** i Log Analytics. Ange namnet på tidsstämpel-fältet i data källan. Data i fältet måste följa ISO 8601-formatet ( `YYYY-MM-DDThh:mm:ssZ` ) |
    | `key_names` | matris | Ange en lista med Log Analytics utmatnings schema fält. Varje List objekt ska omges av enkla citat tecken och objekten avgränsade med kommatecken, och hela listan omges av hakparenteser. Se exemplet nedan. |
    | `plugin_flush_interval` | nummer | Valfritt fält. Ange för att definiera det maximala intervallet (i sekunder) mellan meddelande överföring till Log Analytics. Standardvärdet är 5. |
    | `amount_resizing` | boolean | True/false. Aktivera eller inaktivera funktionen för automatisk skalning, vilket justerar storleken på meddelandets buffertstorlek enligt volymen av loggdata som tagits emot. |
    | `max_items` | nummer | Valfritt fält. Gäller endast om `amount_resizing` värdet är "false". Används för att ange ett tak för buffertstorleken (i poster). Standardvärdet är 2 000.  |

    **Exempel på konfiguration**

        input {
            tcp {
                port => 514
                type => syslog
            }
        }

        filter {
            grok {
                match => { "message" => "<%{NUMBER:PRI}>1 (?<TIME_TAG>[0-9]{4}-[0-9]{1,2}-[0-9]{1,2}T[0-9]{1,2}:[0-9]{1,2}:[0-9]{1,2})[^ ]* (?<HOSTNAME>[^ ]*) %{GREEDYDATA:MSG}" }
            }
        }

        output {
            logstash-output-azure-loganalytics {
                workspace_id => "<WS_ID>"
                workspace_key => "${WS_KEY}"
                custom_log_table_name => "logstashCustomTable"
                key_names => ['PRI','TIME_TAG','HOSTNAME','MSG']
                plugin_flush_interval => 5
            }
        } 

    > [!NOTE]
    > Besök plugin-programmets [GitHub](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/microsoft-logstash-output-azure-loganalytics) om du vill veta mer om dess interna arbete, konfiguration och prestanda inställningar.

1. **Starta om Logstash**

1. **Visa inkommande loggar i Azure Sentinel**

    1. Verifiera att meddelanden skickas till plugin-programmet för utdata.

    1. Klicka på **loggar**på navigerings menyn i Azure Sentinel. Under rubriken **tabeller** expanderar du kategorin **anpassade loggar** . Leta upp och klicka på namnet på den tabell som du har angett (med ett `_CL` suffix) i konfigurationen.

        :::image type="content" source="./media/connect-logstash/logstash-custom-logs-menu.png" alt-text="Logstash anpassade loggar":::

    1. Om du vill se poster i tabellen frågar du tabellen med hjälp av tabell namnet som schema.

        :::image type="content" source="./media/connect-logstash/logstash-custom-logs-query.png" alt-text="Logstash anpassade loggar fråga":::

## <a name="monitor-output-plugin-audit-logs"></a>Övervaka gransknings loggar för utdata-plugin

Om du vill övervaka anslutningen och aktiviteten för plugin-programmet för Azure Sentinel-utdata aktiverar du lämplig Logstash-loggfil. Se filen [Logstash Directory layout](https://www.elastic.co/guide/en/logstash/current/dir-layout.html#dir-layout) för logg filens plats.

Om du inte ser några data i den här logg filen genererar och skickar du vissa händelser lokalt (via plugin-programmet för inkommande och filter) för att se till att utdata-plugin-programmet tar emot data. Azure Sentinel stöder endast problem som rör plugin-programmet för utdata.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du använder Logstash för att ansluta externa data källor till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång med att identifiera hot med Azure Sentinel, med [inbyggda](tutorial-detect-threats-built-in.md) eller [anpassade](tutorial-detect-threats-custom.md) regler.