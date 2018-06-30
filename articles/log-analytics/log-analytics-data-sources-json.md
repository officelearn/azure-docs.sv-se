---
title: Samlar in anpassad JSON-data i OMS Log Analytics | Microsoft Docs
description: Anpassad JSON-datakällor kan samlas in Log Analytics med hjälp av OMS-Agent för Linux.  Dessa anpassade datakällor kan vara enkla skript som returnerar JSON som curl eller något av Fluentd's 300 + plugin-program. Den här artikeln beskrivs den konfiguration som krävs för den här Datasamlingen.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.component: na
ms.openlocfilehash: d3c8807b7624e68ff55557922f97d51e24fc2c19
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131811"
---
# <a name="collecting-custom-json-data-sources-with-the-oms-agent-for-linux-in-log-analytics"></a>Samla in anpassad JSON-datakällor med OMS-Agent för Linux i logganalys
Anpassad JSON-datakällor kan samlas in Log Analytics med hjälp av OMS-Agent för Linux.  Dessa anpassade datakällor kan vara enkla skript som returnerar JSON [curl](https://curl.haxx.se/) eller någon av [Fluentd's 300 + plugin-program](http://www.fluentd.org/plugins/all). Den här artikeln beskrivs den konfiguration som krävs för den här Datasamlingen.

> [!NOTE]
> OMS-Agent för Linux v1.1.0-217 + krävs för anpassad JSON-Data

## <a name="configuration"></a>Konfiguration

### <a name="configure-input-plugin"></a>Konfigurera plugin-programmet för indata

Lägg till för att samla in JSON-data i logganalys `oms.api.` till början av en FluentD-tagg i en inkommande plugin-programmet.

Till exempel följande är en separat fil `exec-json.conf` i `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`.  Här används FluentD plugin-programmet `exec` att köra ett curl-kommando med 30 sekunders mellanrum.  Utdata från kommandot samlas in av pluginprogrammet JSON-utdata.

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
Konfigurationsfilen har lagts till under `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` måste du ha ägarskapet ändras med följande kommando.

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>Konfigurera plugin-programmet för utdata 
Lägg till följande utdata plugin-programmet för konfiguration i huvudsakliga konfigurationen i `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` eller som en separat fil placeras i `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`

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

### <a name="restart-oms-agent-for-linux"></a>Starta om OMS-Agent för Linux
Starta om OMS-Agent för Linux-tjänst med följande kommando.

    sudo /opt/microsoft/omsagent/bin/service_control restart 

## <a name="output"></a>Resultat
Data samlas in i logganalys med en typ av post för `<FLUENTD_TAG>_CL`.

Till exempel anpassade taggar `tag oms.api.tomcat` i logganalys med en typ av post för `tomcat_CL`.  Du kan hämta alla poster för den här typen med följande loggen sökningen.

    Type=tomcat_CL

Kapslade JSON-data som stöds, men indexeras baserad på överordnade fältet. Till exempel följande JSON-data har returnerats från en logganalys sökning som `tag_s : "[{ "a":"1", "b":"2" }]`.

```
{
    "tag": [{
        "a":"1",
        "b":"2"
    }]
}
```


## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [logga sökningar](log-analytics-log-searches.md) att analysera data som samlas in från datakällor och lösningar. 
 