---
title: Samla in Nagios och Zabbix-aviseringar i Log Analytics | Microsoft Docs
description: Nagios och Zabbix är verktyg med öppen källkod. Du kan samla in aviseringar från de här verktygen till Log Analytics för att analysera dem tillsammans med aviseringar från andra källor.  Den här artikeln beskriver hur du konfigurerar Log Analytics-agenten för Linux för att samla in varningar från dessa system.
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
ms.date: 04/13/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 6050771ee1d129edd4370079c07da721f60d2065
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2018
ms.locfileid: "51613553"
---
# <a name="collect-alerts-from-nagios-and-zabbix-in-log-analytics-from-log-analytics-agent-for-linux"></a>Samla in varningar från Nagios och Zabbix i Log Analytics från Log Analytics-agenten för Linux 
[!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]
[Nagios](https://www.nagios.org/) och [Zabbix](http://www.zabbix.com/) är öppen källkod övervakningsverktyg. Du kan samla in aviseringar från de här verktygen till Log Analytics för att analysera dem tillsammans med [aviseringar från andra källor](../monitoring-and-diagnostics/monitoring-overview-alerts.md).  Den här artikeln beskriver hur du konfigurerar Log Analytics-agenten för Linux för att samla in varningar från dessa system.
 
## <a name="prerequisites"></a>Förutsättningar
Samla in varningar från Nagios versionen har stöd för Log Analytics-agenten för Linux 4.2.x och Zabbix upp till version 2.x.

## <a name="configure-alert-collection"></a>Konfigurera varningssamlingen

### <a name="configuring-nagios-alert-collection"></a>Konfigurera Nagios varningssamlingen
Utför följande steg för att samla in aviseringar på Nagios-servern.

1. Bevilja användaren **omsagent** läsbehörighet till loggfilen Nagios `/var/log/nagios/nagios.log`. Förutsatt att filen nagios.log ägs av gruppen `nagios`, du kan lägga till användaren **omsagent** till den **nagios** grupp. 

    sudo usermod - a -G nagios omsagent

2.  Ändra konfigurationsfilen på `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`. Kontrollera att följande poster är nu och inte kommenterade ut:  

        <source>  
          type tail  
          #Update path to point to your nagios.log  
          path /var/log/nagios/nagios.log  
          format none  
          tag oms.nagios  
        </source>  
      
        <filter oms.nagios>  
          type filter_nagios_log  
        </filter>  

3. Starta om daemon omsagent

    ```
    sudo sh /opt/microsoft/omsagent/bin/service_control restart
    ```

### <a name="configuring-zabbix-alert-collection"></a>Konfigurera Zabbix varningssamlingen
Om du vill samla in aviseringar från Zabbix-servern måste du ange en användare och lösenord i *klartext*.  Även perfekt, rekommenderar vi att du skapar en Zabbix-användare med skrivskyddad behörighet att läsa relevant larm.

Utför följande steg för att samla in aviseringar på Nagios-servern.

1. Ändra konfigurationsfilen på `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`. Se till att följande poster finns på plats och inte kommenterade ut.  Ändra det användarnamn och lösenord till värden för din Zabbix-miljö.

        <source>
         type zabbix_alerts
         run_interval 1m
         tag oms.zabbix
         zabbix_url http://localhost/zabbix/api_jsonrpc.php
         zabbix_username Admin
         zabbix_password zabbix
        </source>

2. Starta om daemon omsagent

    `sudo sh /opt/microsoft/omsagent/bin/service_control restart`


## <a name="alert-records"></a>Aviseringsposter
Du kan hämta aviseringsposter från Nagios och Zabbix med [loggsökningar](log-analytics-queries.md) i Log Analytics.

### <a name="nagios-alert-records"></a>Nagios-avisering poster

Avisera poster som samlas in av Nagios har en **typ** av **avisering** och en **SourceSystem** av **Nagios**.  De har egenskaperna i följande tabell.

| Egenskap  | Beskrivning |
|:--- |:--- |
| Typ |*Avisera* |
| SourceSystem |*Nagios* |
| AlertName |Namnet på aviseringen. |
| AlertDescription | Beskrivning av aviseringen. |
| AlertState | Status för tjänsten eller värden.<br><br>Ok<br>VARNING<br>UPP<br>NEDÅT |
| Värdnamn | Namnet på den värd som skapade aviseringen. |
| PriorityNumber | Prioritetsnivån för aviseringen. |
| StateType | Typ av aviseringens status.<br><br>MJUK - problem som inte kontrolleras igen.<br>HARD - problem som har ett angivet antal gånger på nytt.  |
| TimeGenerated |Datum och tid då aviseringen skapades. |


### <a name="zabbix-alert-records"></a>Aviseringsposter som Zabbix
Avisera poster som samlas in av Zabbix har en **typ** av **avisering** och en **SourceSystem** av **Zabbix**.  De har egenskaperna i följande tabell.

| Egenskap  | Beskrivning |
|:--- |:--- |
| Typ |*Avisera* |
| SourceSystem |*Zabbix* |
| AlertName | Namnet på aviseringen. |
| AlertPriority | Allvarlighetsgrad för aviseringen.<br><br>inte klassificerats<br>information<br>varning<br>genomsnittligt<br>Hög<br>haveriberedskap  |
| AlertState | Status för aviseringen.<br><br>0 - tillståndet är uppdaterad.<br>1 - tillståndet är okänt.  |
| AlertTypeNumber | Anger om avisering kan skapa flera problem händelser.<br><br>0 - tillståndet är uppdaterad.<br>1 - tillståndet är okänt.    |
| Kommentarer | Ytterligare kommentarer för aviseringen. |
| Värdnamn | Namnet på den värd som skapade aviseringen. |
| PriorityNumber | Värde som anger aviseringens allvarlighetsgrad.<br><br>0 – inte klassificerats<br>1 – information<br>2 – varning<br>3 – genomsnitt<br>4 – hög<br>5 – haveriberedskap |
| TimeGenerated |Datum och tid då aviseringen skapades. |
| TimeLastModified |Datum och tid som tillståndet för aviseringen senast ändrades. |


## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [aviseringar](../monitoring-and-diagnostics/monitoring-overview-alerts.md) i Log Analytics.
* Lär dig mer om [loggsökningar](log-analytics-queries.md) att analysera data som samlas in från datakällor och lösningar. 
