---
title: Felsöka Azure Log Analytics-agenten för Linux | Microsoft Docs
description: Beskriv problem, orsaker och upplösning för de vanligaste problemen med Log Analytics Linux-agent.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: baa81a52d4b007cd690a2b01df642cd3775f7d6b
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2018
ms.locfileid: "48044144"
---
# <a name="how-to-troubleshoot-issues-with-the-linux-agent-for-log-analytics"></a>Så här felsöker du problem med Linux-agenten för Log Analytics

Du får hjälp med att felsöka fel du kan uppleva med Linux-agenten för Log Analytics och föreslår lösningar för att lösa dem.

## <a name="issue-unable-to-connect-through-proxy-to-log-analytics"></a>Problem: Det gick inte att ansluta via proxy till Log Analytics

### <a name="probable-causes"></a>Troliga orsaker
* Proxyn som anges under publiceringen var felaktig
* Log Analytics och Azure Automation-tjänstslutpunkter är inte tillåten i ditt datacenter 

### <a name="resolutions"></a>Lösningar
1. Reonboard till Log Analytics-tjänsten med OMS-agenten för Linux med hjälp av följande kommando med alternativet `-v` aktiverat. På så sätt kan utförliga utdata för den agent som ansluter via proxy till OMS-tjänsten. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`

2. Läs avsnittet [uppdatera proxyinställningar](log-analytics-agent-manage.md#update-proxy-settings) att verifiera att du har konfigurerat agenten för kommunikation via en proxyserver korrekt.    
* Kontrollera att följande slutpunkter för Log Analytics-tjänsten är vitlistade:

    |Agentresurs| Portar | Riktning |
    |------|---------|----------|  
    |*.ods.opinsights.azure.com | Port 443| Inkommande och utgående |  
    |*.oms.opinsights.azure.com | Port 443| Inkommande och utgående |  
    |*.blob.core.windows.net | Port 443| Inkommande och utgående |  
    |*.azure-automation.net | Port 443| Inkommande och utgående | 

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Problem: Du får ett 403-fel vid försök att publicera

### <a name="probable-causes"></a>Troliga orsaker
* Datum och tid stämmer på Linux-Server 
* Arbetsyte-ID och Arbetsytenyckel används är inte rätt

### <a name="resolution"></a>Lösning

1. Kontrollera tiden på Linux-server med kommandot datum. Om tiden är +/-15 minuter efter den aktuella tiden, misslyckas onboarding. Till rätt detta Uppdatera datum och/eller tidszonen för din Linux-server. 
2. Kontrollera att du har installerat den senaste versionen av OMS-agenten för Linux.  Den senaste versionen nu meddelar dig om tidsförskjutningsintervallet som orsakar onboarding felet.
3. Reonboard med rätt arbetsyte-ID och Arbetsytenyckel följa installationsinstruktionerna tidigare i det här avsnittet.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Problem: Du ser ett 500 och 404-fel i loggfilen direkt efter integreringen
Det här är ett känt problem som uppstår vid första överföring av Linux-data till Log Analytics-arbetsytan. Detta påverkar inte data som skickas eller service-upplevelse.

## <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>Problem: Du inte ser några data i Azure portal

### <a name="probable-causes"></a>Troliga orsaker

- Kom igång med Log Analytics-tjänsten misslyckades
- Anslutning till Log Analytics-tjänsten är blockerad
- OMS-agenten för Linux-data som säkerhetskopieras

### <a name="resolutions"></a>Lösningar
1. Kontrollera om onboarding Log Analytics-tjänsten lyckades genom att kontrollera om det finns följande fil: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Reonboard med hjälp av den `omsadmin.sh` kommandoradsinstruktioner
3. Om du använder en proxyserver, referera till proxy Lösningssteg som angavs tidigare.
4. I vissa fall när OMS-agenten för Linux inte kan kommunicera med tjänsten data på agenten är i kö till den fullständiga buffertstorleken, vilket är 50 MB. OMS-agenten för Linux ska startas genom att köra följande kommando: `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Det här problemet löses i agenten version 1.1.0-28 och senare.

