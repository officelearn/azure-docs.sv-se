---
title: Samla in anpassade JSON-data i Azure Monitor | Microsoft-dokument
description: 'Anpassade JSON-datakällor kan samlas in i Azure Monitor med Log Analytics Agent för Linux.  Dessa anpassade datakällor kan vara enkla skript som returnerar JSON som curl eller en av FluentD: s 300 + plugins. I den här artikeln beskrivs den konfiguration som krävs för den här datainsamlingen.'
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: 49eb3fa22bc9afffb9e93f3152cdc00323b76d41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662169"
---
# <a name="collecting-custom-json-data-sources-with-the-log-analytics-agent-for-linux-in-azure-monitor"></a>Samla in anpassade JSON-datakällor med Log Analytics-agenten för Linux i Azure Monitor
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

Anpassade JSON-datakällor kan samlas in i [Azure Monitor](data-platform.md) med Log Analytics-agenten för Linux.  Dessa anpassade datakällor kan vara enkla skript som returnerar JSON som [curl](https://curl.haxx.se/) eller en av [FluentD: s 300 + plugins](https://www.fluentd.org/plugins/all). I den här artikeln beskrivs den konfiguration som krävs för den här datainsamlingen.


> [!NOTE]
> Log Analytics-agent för Linux v1.1.0-217+ krävs för anpassade JSON-data

## <a name="configuration"></a>Konfiguration

### <a name="configure-input-plugin"></a>Konfigurera indatainsticksprogrammet

Om du vill samla in `oms.api.` JSON-data i Azure Monitor lägger du till början av en FluentD-tagg i ett indatainsticksprogram.

Följande följer till exempel en `exec-json.conf` `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`separat konfigurationsfil i .  Detta använder FluentD `exec` plugin för att köra en curl kommando var 30 sekunder.  Utdata från det här kommandot samlas in av JSON-utdatainsticksprogrammet.

```
<source>
  type exec
  command 'curl localhost/json.output'
  format json
  tag oms.api.httpresponse
  run_interval 30s
</source>

<match oms.api.httpresponse>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api_httpresponse*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```
Konfigurationsfilen som `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` läggs till under måste få ägarskapet ändrat med följande kommando.

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>Konfigurera plugin-program för utdata 
Lägg till följande konfiguration av plugin-utdata i huvudkonfigurationen i `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` eller som en separat konfigurationsfil som placeras i`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`

```
<match oms.api.**>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

### <a name="restart-log-analytics-agent-for-linux"></a>Starta om Log Analytics-agent för Linux
Starta om Log Analytics-agenten för Linux-tjänsten med följande kommando.

    sudo /opt/microsoft/omsagent/bin/service_control restart 

## <a name="output"></a>Resultat
Data samlas in i Azure Monitor med `<FLUENTD_TAG>_CL`en posttyp av .

Till exempel den `tag oms.api.tomcat` anpassade taggen i Azure `tomcat_CL`Monitor med en posttyp av .  Du kan hämta alla poster av den här typen med följande loggfråga.

    Type=tomcat_CL

Kapslade JSON-datakällor stöds, men indexeras baserat på det överordnade fältet. Följande JSON-data returneras till exempel från `tag_s : "[{ "a":"1", "b":"2" }]`en loggfråga som .

```
{
    "tag": [{
        "a":"1",
        "b":"2"
    }]
}
```


## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [loggfrågor](../log-query/log-query-overview.md) för att analysera data som samlas in från datakällor och lösningar. 
