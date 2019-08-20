---
title: Hot identifiering för Azure Service Layer i Azure Security Center | Microsoft Docs
description: I det här avsnittet presenteras Azure Service Layer-aviseringar som är tillgängliga i Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/18/2019
ms.author: v-mohabe
ms.openlocfilehash: 70b43c65703316e5dee8e9cf2cf86fe982a49592
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624771"
---
# <a name="threat-detection-for-azure-service-layer-in-azure-security-center"></a>Hot identifiering för Azure Service Layer i Azure Security Center

I det här avsnittet presenteras Security Centers aviseringar som är tillgängliga när du övervakar följande Azure Service-lager.

* [Azure nätverks lager](#network-layer)
* [Azure Management Layer (Azure Resource Manager) (för hands version)](#management-layer)

>[!NOTE]
>Med hjälp av Telemetrin som Security Center utnyttjar från att trycka in i Azures interna flöden, är den analys som anges nedan tillämplig på alla resurs typer.

## Azure nätverks lager<a name="network-layer"></a>

Security Center Network Layer Analytics baseras på [IPFIX data](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), som är paket rubriker som samlas in av Azure Core-routrar. Baserat på denna datafeed kan Security Center Machine Learning-modeller identifiera och flagga skadliga trafik aktiviteter. Security Center använder Microsoft Threat Intelligence-databasen för att utöka IP-adresser.

> [!div class="mx-tableFixed"]

|Varning|Beskrivning|
|---|---|
|**Misstänkt utgående RDP-nätverks aktivitet**|Analys av exempel på nätverks trafik upptäckte avvikande RDP-kommunikation (utgående Remote Desktop Protocol) som kommer från en resurs i distributionen. Den här aktiviteten betraktas som onormal för den här miljön och kan tyda på att din resurs har komprometterats och att den nu används för att stoppa extern RDP-slutpunkt. Observera att den här typen av aktivitet skulle kunna göra att din IP-adress flaggas som skadlig av externa enheter.|
|**Misstänkt utgående RDP-nätverks aktivitet till flera destinationer**|Analys av exempel på nätverks trafik upptäckte avvikande RDP-kommunikation (utgående Remote Desktop Protocol) som kommer från en resurs i distributionen till flera mål. Den här aktiviteten betraktas som onormal för den här miljön och kan tyda på att din resurs har komprometterats och att den nu används för att bryta gällande externa RDP-slutpunkter. Observera att den här typen av aktivitet skulle kunna göra att din IP-adress flaggas som skadlig av externa enheter.|
|**Misstänkt utgående SSH-nätverks aktivitet**|Analys av exempel på nätverks trafik upptäckte avvikande SSH-kommunikation (utgående Secure Shell) som kommer från en resurs i distributionen. Den här aktiviteten betraktas som onormal för den här miljön och kan tyda på att din resurs har komprometterats och att den nu används för att bryta den externa SSH-slutpunkten. Observera att den här typen av aktivitet skulle kunna göra att din IP-adress flaggas som skadlig av externa enheter.|
|**Misstänkt utgående SSH-nätverks aktivitet till flera mål**|Analys av exempel på nätverks trafik upptäckte avvikande SSH-kommunikation (utgående Secure Shell) som kommer från en resurs i distributionen till flera mål. Den här aktiviteten betraktas som onormal för den här miljön och kan tyda på att din resurs har komprometterats och att den nu används för att bryta tvingande externa SSH-slutpunkter. Observera att den här typen av aktivitet skulle kunna göra att din IP-adress flaggas som skadlig av externa enheter.|
|**Misstänkt inkommande SSH-nätverks aktivitet från flera källor**|Analys av exempel på nätverks trafik upptäckte avvikande inkommande SSH-kommunikation från flera källor till en resurs i distributionen. Olika unika IP-adresser som ansluter till din resurs betraktas som onormala för den här miljön. Den här aktiviteten kan indikera ett försök att bryta fram ditt SSH-gränssnitt från flera värdar (botnät).|
|**Misstänkt inkommande SSH-nätverks aktivitet**|Analys av exempel på nätverks trafik upptäckte avvikande inkommande SSH-kommunikation till en resurs i distributionen. Relativt högt antal inkommande anslutningar till din resurs betraktas som onormalt för den här miljön. Den här aktiviteten kan indikera ett försök att bryta gällande SSH-gränssnitt.
|**Misstänkt inkommande RDP-nätverks aktivitet från flera källor**|Analys av exempel på nätverks trafik upptäckte avvikande inkommande RDP-kommunikation från flera källor till en resurs i distributionen. Olika unika IP-adresser som ansluter till din resurs betraktas som onormala för den här miljön. Den här aktiviteten kan indikera ett försök att bryta mot RDP-gränssnittet från flera värdar (botnät).|
|**Misstänkt inkommande RDP-nätverks aktivitet**|Analys av exempel på nätverks trafik upptäckte avvikande inkommande RDP-kommunikation till en resurs i distributionen. Relativt högt antal inkommande anslutningar till din resurs betraktas som onormalt för den här miljön. Den här aktiviteten kan indikera ett försök att bryta gällande SSH-gränssnitt.|
|**Nätverkskommunikationen med en skadlig adress har upptäckts**|Analys av exempel på nätverks trafik har identifierat kommunikation från en resurs i distributionen med en möjlig kommando-och kontroll (C & C)-Server. Observera att den här typen av aktivitet skulle kunna göra att din IP-adress flaggas som skadlig av externa enheter.|

Information om hur Security Center kan använda nätverksrelaterade signaler för att använda hot skydd finns [i heuristisk DNS-identifieringar i Azure Security Center](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).
## Azure Management Layer (Azure Resource Manager) (för hands version)<a name ="management-layer"></a>

>[!NOTE]
>Security Center skydds lager baserat på Azure Resource Manager är för närvarande en för hands version.

Security Center erbjuder ett extra skydds lager genom att använda Azure Resource Manager händelser, vilket anses vara kontroll planet för Azure. Genom att analysera Azure Resource Manager poster Security Center identifiera ovanliga eller potentiellt skadliga åtgärder i Azure-prenumerationens miljö.

> [!div class="mx-tableFixed"]

|Varning|Beskrivning|
|---|---|
|**Körning av mikroburst-verktyget**|En känd rekognosering Toolkit-körning i moln miljö har upptäckts i din miljö. Verktyget "mikroburst" (se https://github.com/NetSPI/MicroBurst) kan användas av en angripare (eller utträngande testare) för att mappa dina prenumerations resurser, identifiera oskyddade konfigurationer och avslöja konfidentiell information.|
|**Azurite Toolkit-körning**|En känd rekognosering Toolkit-körning i moln miljö har upptäckts i din miljö. Verktyget "Azurite" (se https://github.com/mwrlabs/Azurite) kan användas av en angripare (eller utträngande testare) för att mappa dina prenumerations resurser och identifiera oskyddade konfigurationer.|
|**Misstänkt hanterings-session med ett inaktivt konto**|Analysen av prenumerations aktivitets loggar har identifierat ett misstänkt beteende. Ett huvud konto som inte används under en lång tids period utför nu åtgärder som kan skydda beständigheten för en angripare.|
|**Misstänkt hanterings-session med PowerShell**|Analysen av prenumerations aktivitets loggar har identifierat ett misstänkt beteende. Ett huvud konto som inte regelbundet använder PowerShell för att hantera prenumerations miljön, använder nu PowerShell och utför åtgärder som kan skydda beständigheten för en angripare.|
|**Användning av avancerade metoder för Azure-persistence**|Analysen av prenumerations aktivitets loggar har identifierat ett misstänkt beteende. Anpassade roller har fått legitimized identitets enheter. Detta kan leda till att angriparen får persistency i en Azure-kundmiljö.|
|**Aktivitet från ovanligt land**|Aktivitet från en plats som inte nyligen har besökts av någon användare i organisationen har inträffat.<br/>Den här identifieringen tar hänsyn till de senaste aktivitet platser att fastställa nya och ovanliga platser. Avvikelseidentifieringsmotorn lagrar information om tidigare platser som används av användarna i organisationen. 
|**Aktivitet från anonyma IP-adresser**|Användares aktivitet från en IP-adress som har identifierats som en anonym proxy-IP-adress har identifierats. <br/>Dessa proxyservrar används av personer som vill dölja sina enheters IP-adress och kan användas i skadligt syfte. Den här identifieringen utnyttjar en Machine Learning-algoritm som minskar "falska positiva identifieringar", till exempel mis-märkta IP-adresser som används ofta av användare i organisationen.|
|**Omöjlig resa upptäcktes**|Två användar aktiviteter (är en eller flera sessioner) har inträffat, som kommer från geografiskt avlägsna platser inom en tids period som är kortare än den tid då användaren skulle resa från den första platsen till den andra. Detta indikerar att en annan användare använder samma autentiseringsuppgifter. <br/>Den här identifieringen utnyttjar en algoritm för maskin inlärning som ignorerar tydliga "falska positiva identifieringar" som bidrar till omöjliga rese villkor, till exempel VPN och platser som regelbundet används av andra användare i organisationen. Identifieringen har en inledande inlärningsperiod på sju dagar under vilken den lär sig den nya användarens aktivitetsmönster.|

>[!NOTE]
> Flera av analyserna ovan drivs av Microsoft Cloud App Security (MCAS). För att dra nytta av dessa analyser krävs en aktive rad MCAS-licens. Om du har en MCAS-licens är de här aviseringarna aktiverade som standard. Så här inaktiverar du dem:
>
> 1. På bladet Security Center väljer du **säkerhets princip**. Klicka på **Redigera inställningar**för den prenumeration som du vill ändra.
> 2. Klicka på **hot identifiering**.
> 3. Under **Aktivera integreringar**avmarkerar **du Tillåt Microsoft Cloud App Security åtkomst till mina data**och klickar på **Spara**.

>[!NOTE]
>Azure Security Center lagrar säkerhetsrelaterad kund information i samma geo som dess resurs. Om Microsoft ännu inte har distribuerat Azure Security Center i resursens geografiska område, lagrar data i USA. När Microsoft Cloud App Security (MCAS) är aktive rad lagras informationen i enlighet med reglerna för geo-platsen för MCAS. [Mer information finns i data lagring för icke-regionala tjänster](https://azuredatacentermap.azurewebsites.net/).
