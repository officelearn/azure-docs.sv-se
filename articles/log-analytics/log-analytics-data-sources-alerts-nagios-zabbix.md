---
title: Samla in Nagios och Zabbix aviseringar i OMS Log Analytics | Microsoft Docs
description: "Nagios och Zabbix är öppen källkod övervakningsverktyg. Du kan samla in aviseringar från dessa verktyg till Log Analytics för att analysera dem tillsammans med aviseringar från andra källor.  Den här artikeln beskriver hur du konfigurerar OMS-Agent för Linux för att samla in varningar från dessa system."
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
ms.date: 05/04/2017
ms.author: magoedte
ms.openlocfilehash: 0b64c32e1031e704d50aab0b38eaea41e27d134b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="collect-alerts-from-nagios-and-zabbix-in-log-analytics-from-oms-agent-for-linux"></a>Samla in aviseringar från Nagios och Zabbix i logganalys från OMS-Agent för Linux 
[Nagios](https://www.nagios.org/) och [Zabbix](http://www.zabbix.com/) är öppen källkod övervakningsverktyg.  Du kan samla in aviseringar från dessa verktyg till Log Analytics för att analysera dem tillsammans med [aviseringar från andra källor](log-analytics-alerts.md).  Den här artikeln beskriver hur du konfigurerar OMS-Agent för Linux för att samla in varningar från dessa system.
 
## <a name="configure-alert-collection"></a>Konfigurera varningssamlingen

### <a name="configuring-nagios-alert-collection"></a>Konfigurera Nagios varningssamlingen
Utför följande steg på Nagios-servern för att samla in aviseringar.

1. Bevilja användaren **omsagent** läsbehörighet till loggfilen Nagios (d.v.s. `/var/log/nagios/nagios.log`). Under förutsättning att filen nagios.log ägs av gruppen `nagios`, du kan lägga till användaren **omsagent** till den **nagios** grupp. 

    sudo usermod - a -G nagios omsagent

2.  Ändra konfigurationsfilen på (`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`). Kontrollera att följande poster finns och inte kommenterad ut:  

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

3. Starta om omsagent-daemon

    ```
    sudo sh /opt/microsoft/omsagent/bin/service_control restart
    ```

### <a name="configuring-zabbix-alert-collection"></a>Konfigurera Zabbix varningssamlingen
Om du vill samla in aviseringar från en Zabbix-server, måste du ange en användare och lösenord i *klartext*. Detta är inte idealiskt, men vi rekommenderar att du skapar användaren och bevilja behörighet för att övervaka onlu.

Utför följande steg på Nagios-servern för att samla in aviseringar.

1. Ändra konfigurationsfilen på (`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`). Kontrollera att följande poster finns och inte kommenterad ut.  Ändra det användarnamn och lösenord till värden för Zabbix-miljö.

        <source>
         type zabbix_alerts
         run_interval 1m
         tag oms.zabbix
         zabbix_url http://localhost/zabbix/api_jsonrpc.php
         zabbix_username Admin
         zabbix_password zabbix
        </source>

2. Starta om omsagent-daemon

    sudo del /opt/microsoft/omsagent/bin/service_control starta om


## <a name="alert-records"></a>Varning-poster
Du kan hämta avisering poster från Nagios och Zabbix med [logga sökningar](log-analytics-log-searches.md) i logganalys.

### <a name="nagios-alert-records"></a>Avisering för Nagios-poster

Varna poster som samlas in av Nagios har en **typen** av **avisering** och en **SourceSystem** av **Nagios**.  De har egenskaperna i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| Typ |*Varning* |
| SourceSystem |*Nagios* |
| AlertName |Namnet på aviseringen. |
| AlertDescription | Beskrivning av aviseringen. |
| In AlertState | Status för tjänsten eller värden.<br><br>OKEJ<br>VARNING<br>KONFIGURERA<br>NED |
| Värdnamn | Namnet på den värd som skapade aviseringen. |
| PriorityNumber | Prioritetsnivån för aviseringen. |
| StateType | Typ av tillstånd.<br><br>SOFT - problem som inte kontrolleras igen.<br>HÅRDDISK - problem som har ett angivet antal gånger begäran igen.  |
| TimeGenerated |Datum och tid då aviseringen skapades. |


### <a name="zabbix-alert-records"></a>Varning Zabbix-poster
Varna poster som samlas in av Zabbix har en **typen** av **avisering** och en **SourceSystem** av **Zabbix**.  De har egenskaperna i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| Typ |*Varning* |
| SourceSystem |*Zabbix* |
| AlertName | Namnet på aviseringen. |
| AlertPriority | Allvarlighetsgrad för aviseringen.<br><br>inte har klassificerats<br>Information<br>Varning<br>Genomsnittlig<br>Hög<br>katastrofåterställning  |
| In AlertState | Status för aviseringen.<br><br>0 - tillståndet är uppdaterad.<br>1 - tillståndet är okänt.  |
| AlertTypeNumber | Anger om avisering kan skapa flera problem händelser.<br><br>0 - tillståndet är uppdaterad.<br>1 - tillståndet är okänt.    |
| Kommentarer | Ytterligare kommentarer för aviseringen. |
| Värdnamn | Namnet på den värd som skapade aviseringen. |
| PriorityNumber | Värdet anger allvarlighetsgrad för aviseringen.<br><br>0 - inte har klassificerats<br>1 - information<br>2 - varning<br>3 - medel<br>4 - hög<br>5 - katastrofåterställning |
| TimeGenerated |Datum och tid då aviseringen skapades. |
| TimeLastModified |Datum och tid tillståndet för aviseringen senast ändrades. |


## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [aviseringar](log-analytics-alerts.md) i logganalys.
* Lär dig mer om [logga sökningar](log-analytics-log-searches.md) att analysera data som samlas in från datakällor och lösningar. 
