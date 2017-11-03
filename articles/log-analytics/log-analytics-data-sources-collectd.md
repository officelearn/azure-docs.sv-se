---
title: "Samla in data från CollectD i OMS Log Analytics | Microsoft Docs"
description: "CollectD är en öppen källkod Linux-demonen som regelbundet samlar in data från program och information om systemet.  Den här artikeln innehåller information om att samla in data från CollectD i logganalys."
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/02/2017
ms.author: magoedte
ms.openlocfilehash: a63b15ca5126b45451f0694c9ee75d7b67b1ceaf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="collect-data-from-collectd-on-linux-agents-in-log-analytics"></a>Samla in data från CollectD på Linux-agenter i logganalys
[CollectD](https://collectd.org/) är en öppen källkod Linux-demonen som regelbundet samlar in prestandastatistik från program och information om systemet. Exempelprogram inkluderar Java Virtual Machine (JVM), MySQL-servern och Nginx. Den här artikeln innehåller information om att samla in prestandadata från CollectD i logganalys.

En fullständig lista över tillgängliga plugin-program finns på [tabell av plugin-program](https://collectd.org/wiki/index.php/Table_of_Plugins).

![Översikt över CollectD](media/log-analytics-data-sources-collectd/overview.png)

Konfigurera följande CollectD ingår i OMS-Agent för Linux att vidarebefordra CollectD data till OMS-Agent för Linux.

    LoadPlugin write_http

    <Plugin write_http>
         <Node "oms">
         URL "127.0.0.1:26000/oms.collectd"
         Format "JSON"
         StoreRates true
         </Node>
    </Plugin>

Dessutom, om du använder en versioner av collectD innan 5.5 använder följande konfiguration i stället.

    LoadPlugin write_http

    <Plugin write_http>
       <URL "127.0.0.1:26000/oms.collectd">
        Format "JSON"
         StoreRates true
       </URL>
    </Plugin>

CollectD konfigurationen använder standard`write_http` plugin-programmet för att skicka mått prestandadata via port 26000 till OMS-Agent för Linux. 

> [!NOTE]
> Den här porten kan konfigureras att ett egendefinierat porten om det behövs.

OMS-Agent för Linux också lyssnar på port 26000 CollectD mått och konverterar dem till OMS-schemat statistik. Följande är OMS-Agent för Linux-konfiguration `collectd.conf`.

    <source>
      type http
      port 26000
      bind 127.0.0.1
    </source>

    <filter oms.collectd>
      type filter_collectd
    </filter>


## <a name="versions-supported"></a>Versioner som stöds
- Logganalys stöder för närvarande CollectD version 4.8 och högre.
- OMS-Agent för Linux v1.1.0-217 eller senare krävs för CollectD mått samling.


## <a name="configuration"></a>Konfiguration
Här följer grundläggande steg för att konfigurera insamling av data för CollectD i logganalys.

1. Konfigurera CollectD för att skicka data till OMS-Agent för Linux med write_http plugin-programmet.  
2. Konfigurera OMS-Agent för Linux att lyssna efter CollectD data på rätt port.
3. Starta om CollectD och OMS-Agent för Linux.

### <a name="configure-collectd-to-forward-data"></a>Konfigurera CollectD för att vidarebefordra data 

1. Att vidarebefordra CollectD data till OMS-Agent för Linux `oms.conf` måste läggas till Collectds konfigurationskatalogen. Mål för den här filen är beroende av Linux-distro av din dator.

    Om din CollectD config directory finns i /etc/collectd.d/:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd.d/oms.conf

    Om din CollectD config directory finns i /etc/collectd/collectd.conf.d/:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd/collectd.conf.d/oms.conf

    >[!NOTE]
    >Du behöver ändra taggar i för CollectD versioner före 5.5 `oms.conf` som ovan.
    >

2. Kopiera collectd.conf till arbetsytan önskade omsagent konfigurationskatalogen.

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/collectd.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/
        sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/collectd.conf

3. Starta om CollectD och OMS-Agent för Linux med följande kommandon.

    sudo service collectd omstart sudo /opt/microsoft/omsagent/bin/service_control omstart

## <a name="collectd-metrics-to-log-analytics-schema-conversion"></a>CollectD mått till logganalys schemat konvertering
Om du vill behålla en bekant modell mellan infrastruktur-mätvärden som redan samlats in av OMS-Agent för Linux och nya mått som samlas in av CollectD följande schemamappning används:

| Fältet CollectD mått | Log Analytics-fält |
|:--|:--|
| värden | Dator |
| plugin-programmet | Ingen |
| plugin_instance | Instansnamn<br>Om **plugin_instance** är *null* sedan InstanceName = ”*_Total*” |
| typ | Objektnamn |
| type_instance | CounterName<br>Om **type_instance** är *null* sedan CounterName =**tomt** |
| dsnames] | CounterName |
| dstypes | Ingen |
| [] värden | CounterValue |

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [logga sökningar](log-analytics-log-searches.md) att analysera data som samlas in från datakällor och lösningar. 
* Använd [anpassade fält](log-analytics-custom-fields.md) att tolka data från syslog-poster till enskilda fält.

