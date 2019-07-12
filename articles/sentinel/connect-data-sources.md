---
title: Ansluta datakällor till Sentinel-förhandsversion i Azure? | Microsoft Docs
description: Lär dig hur du ansluter datakällor till Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: a3b63cfa-b5fe-4aff-b105-b22b424c418a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/04/2019
ms.author: rkarlin
ms.openlocfilehash: 9f64497cdf27729cebc243deca1def9ff1e5c680
ms.sourcegitcommit: 80aaf27e3ad2cc4a6599a3b6af0196c6239e6918
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673816"
---
# <a name="connect-data-sources"></a>Ansluta till datakällor

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Att integrera Azure Sentinel måste du först ansluta till dina datakällor. Azure Sentinel levereras med ett antal anslutningsappar för Microsoft-lösningar, tillgängliga utanför rutan och ge i realtid integration, inklusive Microsoft Threat Protection lösningar och Microsoft 365 källor, till exempel Office 365, Azure AD, Azure ATP och Microsoft Cloud App Security, och mycket mer. Det finns dessutom inbyggda anslutningar till bredare säkerhetsekosystemet för icke-Microsoft-lösningar. Du kan också använda common event format, Syslog eller REST-API för att ansluta dina datakällor med Azure Sentinel samt.  

1. På menyn, Välj **datakopplingar**. Den här sidan kan du se en fullständig lista över kopplingar som Sentinel-Azure tillhandahåller och deras status. Välj den koppling som du vill ansluta och välj **öppna anslutningsapp-sidan**. 

   ![Datainsamlare](./media/collect-data/collect-data-page.png)

1. Kontrollera att du har uppfyllt alla krav och följ instruktionerna för att ansluta data till Azure Sentinel på sidan viss koppling. Det kan ta lite tid att börja synkronisera med Azure Sentinel-loggar. När du ansluter kan du se en sammanfattning av data i den **mottagna Data** graph och anslutningsstatusen för datatyperna.

   ![Ansluta insamlare](./media/collect-data/opened-connector-page.png)
  
1. Klicka på den **nästa steg** fliken för att få en lista över out-of the box innehåll Sentinel-Azure tillhandahåller för den specifika datatypen.

   ![Datainsamlare](./media/collect-data/data-insights.png)
 

## <a name="data-connection-methods"></a>Data anslutningsmetoder

Följande data anslutningsmetoder stöds av Azure Sentinel:

- **Microsoft-tjänster**:<br> Microsoft-tjänster är anslutna internt, att utnyttja Azure grunden för out-nyckelfärdig integrering, följande lösningar kan anslutas med några få klick:
    - [Office 365](connect-office-365.md)
    - [Azure AD granska loggar och inloggningar](connect-azure-active-directory.md)
    - [Azure-aktivitet](connect-azure-activity.md)
    - [Azure AD Identity Protection](connect-azure-ad-Identity-protection.md)
    - [Azure Security Center](connect-azure-security-center.md)
    - [Azure Information Protection](connect-azure-information-protection.md)
    - [Azure Advanced Threat Protection](connect-azure-atp.md)
    - [Cloud App Security](connect-cloud-app-security.md)
    - [Säkerhetshändelser i Windows](connect-windows-security-events.md) 
    - [Windows-brandväggen](connect-windows-firewall.md)

- **Externa lösningar via API: et**: Vissa datakällor är anslutna med API: er som tillhandahålls av den anslutna datakällan. De flesta säkerhetstekniker ger vanligtvis en uppsättning API: er som händelseloggar kan hämtas. API: erna ansluta till Azure Sentinel och samla in specifika datatyper och skicka dem till Azure Log Analytics. Enheter som är anslutna via API: T är:
    - [Barracuda](connect-barracuda.md)
    - [Symantec](connect-symantec.md)
- **Externa lösningar via agent**: Azure Sentinel kan anslutas till andra datakällor som kan utföra i realtid loggströmningen med hjälp av protokollet Syslog via en agent. <br>De flesta installationer använda Syslog-protokollet för att skicka meddelanden om händelser som innehåller loggen själva och data om loggen. Formatet på loggarna varierar, men de flesta installationer stöder Common Event Format (CEF)-standarden. <br>Azure Sentinel agenten som är baserad på Microsoft Monitoring Agent, konverterar formaterad CEF-loggar till ett format som kan matas in av Log Analytics. Beroende på typen av enhet installeras agenten direkt på installationen eller på en dedikerad server för Linux. Agenten för Linux tar emot händelser från Syslog-daemon över UDP, men i fall där en Linux-dator förväntas samla in en stor mängd Syslog-händelser, skickas de via TCP från Syslog-daemon till agenten och därifrån till Log Analytics.
    - Brandväggar, proxyservrar och slutpunkter:
        - [F5](connect-f5.md)
        - [Check Point](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [Fortinet](connect-fortinet.md)
        - [Palo Alto](connect-paloalto.md)
        - [Andra CEF-installationer](connect-common-event-format.md)
        - [Andra Syslog-installationer](connect-syslog.md)
    - DLP-lösningar
    - [Säkerhetsexperter](connect-threat-intelligence.md)
    - [DNS-datorer](connect-dns.md) -agenten är installerad direkt på DNS-dator
    - Linux-servrar
    - Andra moln
    
## Anslutningsalternativ för agenten<a name="agent-options"></a>

Om du vill ansluta din externa installation till Azure Sentinel agenten måste distribueras på en dedikerad dator (VM eller lokalt) för kommunikationen mellan enheten och Azure Sentinel. Du kan distribuera agenten automatiskt eller manuellt. Automatisk distribution är endast tillgänglig om den dedikerade datorn är en ny virtuell dator som du skapar i Azure. 


![CEF i Azure](./media/connect-cef/cef-syslog-azure.png)

Du kan också distribuera agenten manuellt på en befintlig Azure-dator på en virtuell dator i ett annat moln eller på en lokal dator.

![CEF lokalt](./media/connect-cef/cef-syslog-onprem.png)


## <a name="next-steps"></a>Nästa steg

- Om du vill komma igång med Azure Sentinel, behöver du en prenumeration på Microsoft Azure. Om du inte har någon prenumeration kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/free/).
- Lär dig hur du [publicera dina data till Azure Sentinel](quickstart-onboard.md), och [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
