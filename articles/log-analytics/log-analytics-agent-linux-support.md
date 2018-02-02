---
title: "Felsöka Azure Log Analytics Linux-agenten | Microsoft Docs"
description: "Beskriv symptom, orsaker och lösningar på vanliga problem med Log Analytics Linux-agent."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: magoedte
ms.openlocfilehash: 895a77a66f50b4c5217ec7d672f6441b85bf1856
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="how-to-troubleshoot-issues-with-the-linux-agent-for-log-analytics"></a>Felsökning av problem med Linux-agent för logganalys

Du får hjälp med att felsöka fel som du kan uppleva med Linux-agent för Log Analytics och ger förslag på lösningar för att lösa dem.

## <a name="issue-unable-to-connect-through-proxy-to-log-analytics"></a>Problem: Det gick inte att ansluta via proxy till logganalys

### <a name="probable-causes"></a>Troliga orsaker
* Proxyn som anges under onboarding var felaktigt
* Log Analytics och Azure Automation-Tjänsteslutpunkter är inte vitlistad i ditt datacenter 

### <a name="resolutions"></a>Lösningar
1. Reonboard till Log Analytics-tjänsten med OMS-Agent för Linux med hjälp av följande kommando med alternativet `-v` aktiverat. Detta gör att utförliga utdata av agenten ansluter via proxy till OMS-tjänsten. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`

2. Läs avsnittet [uppdatera proxyinställningar](log-analytics-agent-manage.md#update-proxy-settings) att verifiera att du har konfigurerat agenten för kommunikation via en proxyserver korrekt.    
* Kontrollera att följande slutpunkter för Log Analytics-tjänsten är godkända:

    |Agentresurs| Portar |  
    |------|---------|  
    |*.ods.opinsights.azure.com | Port 443|   
    |*.oms.opinsights.azure.com | Port 443|   
    |ods.systemcenteradvisor.com | Port 443|   
    |*.blob.core.windows.net/ | Port 443|   

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Problem: Du får ett 403-fel vid försök att publicera

### <a name="probable-causes"></a>Troliga orsaker
* Datum och tid är felaktiga på Linux-Server 
* Arbetsyte-ID och Arbetsytenyckel som används är inte korrekt

### <a name="resolution"></a>Lösning

1. Kontrollera tiden på Linux-servern med kommandot datum. Om tiden är +/-15 minuter från aktuell tid, misslyckas onboarding. Till rätt detta Uppdatera datum och/eller tidszonen för Linux-servern. 
2. Kontrollera att du har installerat den senaste versionen av OMS-Agent för Linux.  Den senaste versionen nu meddelas du om tidsförskjutningsintervallet orsakar onboarding felet.
3. Reonboard med rätt arbetsyte-ID och Arbetsytenyckel följa installationsanvisningarna tidigare i det här avsnittet.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Problem: Du ser ett 500 och 404-fel i loggfilen direkt efter onboarding
Detta är ett känt problem som uppstår på första uppladdning av Linux-data i logganalys-arbetsytan. Detta påverkar inte data som skickas eller tjänsten erfarenhet.

## <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>Problem: Du inte ser några data i Azure-portalen

### <a name="probable-causes"></a>Troliga orsaker

- Det gick inte att Onboarding till Log Analytics-tjänsten
- Anslutningen till Log Analytics-tjänsten är blockerad
- OMS-Agent för Linux data säkerhetskopieras

### <a name="resolutions"></a>Lösningar
1. Kontrollera om onboarding Log Analytics-tjänsten lyckades genom att kontrollera om det finns följande fil:`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Reonboard med hjälp av den `omsadmin.sh` kommandoradsinstruktioner
3. Om du använder en proxyserver, referera till proxy upplösning stegen ovan.
4. I vissa fall när OMS-Agent för Linux inte kan kommunicera med tjänsten, data på agenten är i kö på den fullständiga buffertstorleken, vilket är 50 MB. OMS-Agent för Linux ska startas om genom att köra följande kommando: `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Det här problemet har lösts i agenten version 1.1.0-28 och senare.

