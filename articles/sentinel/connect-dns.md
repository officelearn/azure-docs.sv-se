---
title: Anslut DNS-data i Azure Sentinel| Microsoft-dokument
description: Lär dig hur du ansluter DNS-data i Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 6d43b82ecd13ac5e082d270ee44ce61fef763d2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588322"
---
# <a name="connect-your-domain-name-server"></a>Anslut domännamnsservern

> [!IMPORTANT]
> DNS-dataanslutningen i Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här funktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan ansluta alla DNS-servrar (Domain Name Server) som körs på Windows till Azure Sentinel. Detta görs genom att installera en agent på DNS-datorn. Med hjälp av DNS-loggar kan du få säkerhets-, prestanda- och verksamhetsrelaterade insikter i organisationens DNS-infrastruktur genom att samla in, analysera och korrelera analytiska och granskningsloggar och andra relaterade data från DNS-servrarna.

När du aktiverar DNS-logganslutning kan du:
- Identifiera klienter som försöker lösa skadliga domännamn
- Identifiera inaktuella resursposter
- Identifiera ofta efterfrågade domännamn och pratsamma DNS-klienter
- Visa begärandeläsning på DNS-servrar
- Visa dynamiska DNS-registreringsfel

## <a name="connected-sources"></a>Anslutna källor

I följande tabell beskrivs de anslutna källor som stöds av den här lösningen:

| **Ansluten källa** | **Support** | **Beskrivning** |
| --- | --- | --- |
| [Windows-agenter](../azure-monitor/platform/agent-windows.md) | Ja | Lösningen samlar in DNS-information från Windows-agenter. |
| [Linux-agenter](../azure-monitor/learn/quick-collect-linux-computer.md) | Inga | Lösningen samlar inte in DNS-information från direkta Linux-agenter. |
| [System Center Operations Manager-hanteringsgrupp](../azure-monitor/platform/om-agents.md) | Ja | Lösningen samlar in DNS-information från agenter i en ansluten Hanteringsgrupp för Operations Manager. En direkt anslutning från Operations Manager-agenten till Azure Monitor krävs inte. Data vidarebefordras från hanteringsgruppen till log analytics-arbetsytan. |
| [Azure-lagringskonto](../azure-monitor/platform/collect-azure-metrics-logs.md) | Inga | Azure-lagring används inte av lösningen. |

### <a name="data-collection-details"></a>Information om datainsamling

Lösningen samlar in DNS-inventering och DNS-händelserelaterade data från DNS-servrarna där en Log Analytics-agent är installerad. Lagerrelaterade data, till exempel antalet DNS-servrar, zoner och resursposter, samlas in genom att köra DNS PowerShell-cmdletar. Uppgifterna uppdateras en gång varannan dag. Händelserelaterade data samlas in i nära realtid från de [analytiska och granskningsloggar](https://technet.microsoft.com/library/dn800669.aspx#enhanc) som tillhandahålls av förbättrad DNS-loggning och diagnostik i Windows Server 2012 R2.


## <a name="connect-your-dns-appliance"></a>Anslut DNS-apparaten

1. I Azure Sentinel-portalen väljer du **Data-kopplingar** och väljer panelen **DNS (Förhandsversion).**
1. Om dina DNS-datorer finns i Azure:
    1. Klicka på **Installera agent på den virtuella Azure Windows-datorn**.
    1. I listan **Virtuella datorer** väljer du den DNS-dator som du vill streama till Azure Sentinel. Kontrollera att det här är en virtuell windows-dator.
    1. Klicka på **Anslut**i fönstret som öppnas för den virtuella datorn.  
    1. Klicka på **Aktivera** i **DNS-anslutningsfönstret.** 

2. Om DNS-datorn inte är en virtuell Azure-dator:
    1. Klicka på **Installera agent på datorer som inte är Azure.**
    1. I fönstret **Direct-agent** väljer du antingen **Hämta Windows-agent (64 bitar)** eller **Hämta Windows-agent (32 bitar)**.
    1. Installera agenten på DNS-datorn. Kopiera **arbetsyte-ID,** **primärnyckel**och **sekundärnyckel** och använd dem när du uppmanas att göra det under installationen.

3. Om du vill använda det relevanta schemat i Log Analytics för DNS-loggarna söker du efter **DnsEvents**.

## <a name="validate"></a>Verifiera 

Sök efter schemat **DnsEvents** i Logganalys och se till att det finns händelser.

## <a name="troubleshooting"></a>Felsökning

Om uppslagsfrågor inte visas i Azure Sentinel följer du dessa steg så att frågorna visas korrekt:
1. Aktivera [DNS Analytics-loggarna på servrarna](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11)).
2. Kontrollera att DNSEvents visas i insamlingslistan för Logganalys.
3. Aktivera [Azure DNS Analytics](../azure-monitor/insights/dns-analytics.md).
4. I Azure DNS Analytics, under **Konfiguration**, ändra någon av inställningarna, spara den och ändra den sedan om du behöver och spara den sedan igen.
5. Kontrollera Azure DNS-analys för att se till att frågorna nu visas.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter DNS-lokala enheter till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
