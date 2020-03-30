---
title: Samla in data från Insamlad i Azure Monitor | Microsoft-dokument
description: CollectD är en Linux-demon med öppen källkod som regelbundet samlar in data från program och information på systemnivå.  Den här artikeln innehåller information om hur du samlar in data från Insamlad i Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/27/2018
ms.openlocfilehash: b8c09d4ac5d0856eb0d448a1cabd9adc567850c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670618"
---
# <a name="collect-data-from-collectd-on-linux-agents-in-azure-monitor"></a>Samla in data från Insamlade data på Linux-agenter i Azure Monitor
[CollectD](https://collectd.org/) är en Linux-demon med öppen källkod som regelbundet samlar in prestandamått från program och information på systemnivå. Exempel på program är Java Virtual Machine (JVM), MySQL Server och Nginx. Den här artikeln innehåller information om hur du samlar in prestandadata från Insamlad i Azure Monitor.

En fullständig lista över tillgängliga plugins finns på [Table of Plugins](https://collectd.org/wiki/index.php/Table_of_Plugins).

![Översikt över insamlingar](media/data-sources-collectd/overview.png)

Följande CollectD-konfiguration ingår i Log Analytics-agenten för Linux för att dirigera insamlade data till Log Analytics-agenten för Linux.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

    LoadPlugin write_http

    <Plugin write_http>
         <Node "oms">
         URL "127.0.0.1:26000/oms.collectd"
         Format "JSON"
         StoreRates true
         </Node>
    </Plugin>

Om du använder en version av collectD före 5.5 använder du dessutom följande konfiguration i stället.

    LoadPlugin write_http

    <Plugin write_http>
       <URL "127.0.0.1:26000/oms.collectd">
        Format "JSON"
         StoreRates true
       </URL>
    </Plugin>

Den insamlade konfigurationen`write_http` använder standardinsticksprogrammet för att skicka prestandamåttdata över port 26000 till Log Analytics-agenten för Linux. 

> [!NOTE]
> Den här porten kan konfigureras till en anpassad definierad port om det behövs.

Log Analytics-agenten för Linux lyssnar också på port 26000 för insamlade mått och konverterar dem sedan till Azure Monitor-schemamått. Följande är Log Analytics-agenten `collectd.conf`för Linux-konfiguration .

    <source>
      type http
      port 26000
      bind 127.0.0.1
    </source>

    <filter oms.collectd>
      type filter_collectd
    </filter>

> [!NOTE]
> CollectD som standard är inställd på att läsa [interval](https://collectd.org/wiki/index.php/Interval)värden med ett 10-sekundersintervall . Eftersom detta direkt påverkar mängden data som skickas till Azure Monitor Logs, kan du behöva justera det här intervallet inom CollectD-konfigurationen för att hitta en bra balans mellan övervakningskraven och tillhörande kostnader och användning för Azure Monitor Logs.

## <a name="versions-supported"></a>Versioner som stöds
- Azure Monitor stöder för närvarande CollectD version 4.8 och senare.
- Log Analytics-agent för Linux v1.1.0-217 eller högre krävs för insamling av mätinsamling.


## <a name="configuration"></a>Konfiguration
Följande är grundläggande steg för att konfigurera insamling av insamlade data i Azure Monitor.

1. Konfigurera insamlad för att skicka data till Log Analytics-agenten för Linux med hjälp av plugin-programmet write_http.  
2. Konfigurera Log Analytics-agenten för Linux för att lyssna efter insamlade data på lämplig port.
3. Starta om CollectD och Log Analytics agent för Linux.

### <a name="configure-collectd-to-forward-data"></a>Konfigurera insamlad för att vidarebefordra data 

1. Om du vill dirigera insamlade data `oms.conf` till Log Analytics-agenten för Linux måste du läggas till i CollectD:s konfigurationskatalog. Målet för den här filen beror på Linux-distributioner av din maskin.

    Om din CollectD config-katalog finns i /etc/collectd.d/:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd.d/oms.conf

    Om din CollectD config-katalog finns i /etc/collectd/collectd.conf.d/:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd/collectd.conf.d/oms.conf

    >[!NOTE]
    >För collectd-versioner före 5.5 måste du `oms.conf` ändra taggarna i som visas ovan.
    >

2. Kopiera collectd.conf till den önskade arbetsytans konfigurationskatalog för omsagent.

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/collectd.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/
        sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/collectd.conf

3. Starta om CollectD och Log Analytics-agenten för Linux med följande kommandon.

    sudo tjänsten samlas omstart sudo / opt / microsoft / omsagent / bin / service_control omstart

## <a name="collectd-metrics-to-azure-monitor-schema-conversion"></a>Insamlade mått till azure monitor-schemakonvertering
För att upprätthålla en välbekant modell mellan infrastrukturmått som redan samlats in av Log Analytics-agent för Linux och de nya mått som samlas in av CollectD används följande schemamappning:

| Fältet Insamlad mått | Fältet Azure Monitor |
|:--|:--|
| `host` | Dator |
| `plugin` | Inget |
| `plugin_instance` | Förekomstnamn<br>Om **plugin_instance** är *null* _Total *_Total*" |
| `type` | ObjectName |
| `type_instance` | CounterName (Motnamn)<br>Om **type_instance** är *null* då CounterName =**tom** |
| `dsnames[]` | CounterName (Motnamn) |
| `dstypes` | Inget |
| `values[]` | Motvärdering |

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [loggfrågor](../log-query/log-query-overview.md) för att analysera data som samlas in från datakällor och lösningar. 
* Använd [anpassade fält](custom-fields.md) för att tolka data från syslog-poster i enskilda fält.
