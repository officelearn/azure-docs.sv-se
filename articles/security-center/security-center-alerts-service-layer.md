---
title: Hotidentifiering för Azure-tjänst-lager i Azure Security Center | Microsoft Docs
description: Det här avsnittet innehåller Azure-tjänsten layer aviseringar tillgängliga i Azure Security Center.
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
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 8c1733877834f82d9ee2524cf8bf54f532e7d9c4
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571730"
---
# <a name="threat-detection-for-azure-service-layer-in-azure-security-center"></a>Hotidentifiering för Azure-tjänst-lager i Azure Security Center

Det här avsnittet innehåller de Säkerhetscenter aviseringarna som är tillgängliga när du övervakar följande nivåer i Azure-tjänsten.

* [Azure nätverksnivå](#network-layer)
* [Azure hanteringslager (Azure Resource Manager) (förhandsversion)](#management-layer)

>[!NOTE]
>Med hjälp av telemetrin som Security Center använder från att interna Azure-feeds gäller analytics som anges nedan för alla typer av resurser.

## Azure nätverksnivå<a name="network-layer"></a>

Security Center nätverkslager analytics baseras på exemplet [IPFIX data](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), som är paketrubriker som samlas in av Azure core routrar. Security Center machine learning-modeller är baserat på denna datafeed, identifiera och flaggan skadlig trafik aktiviteter. Security Center använder Microsofts Threat Intelligence databas om du vill utöka IP-adresser.

> [!div class="mx-tableFixed"]

|Varning|Beskrivning|
|---|---|
|**Misstänkt utgående RDP-nätverksaktivitet**|Provade analysen av nätverkstrafik identifierade avvikande utgående Remote Desktop Protocol (RDP)-kommunikation från en resurs i din distribution. Den här aktiviteten anses onormalt för den här miljön och kan indikera att din resurs har komprometterats och används nu för råstyrkeattacker mot externa RDP-slutpunkt. Observera att den här typen av aktivitet skulle kunna göra att din IP-adress flaggas som skadlig av externa enheter.|
|**Misstänkt utgående RDP-nätverksaktivitet till flera mål**|Provade analysen av nätverkstrafik identifierade avvikande utgående Remote Desktop Protocol (RDP)-kommunikation från en resurs i din distribution till flera destinationer. Den här aktiviteten anses onormalt för den här miljön och kan indikera att din resurs har komprometterats och används nu för råstyrkeattacker mot externa RDP-slutpunkter. Observera att den här typen av aktivitet skulle kunna göra att din IP-adress flaggas som skadlig av externa enheter.|
|**Misstänkt utgående SSH-nätverksaktivitet**|Provade analysen av nätverkstrafik identifierade avvikande utgående SSH (Secure Shell)-kommunikation från en resurs i din distribution. Den här aktiviteten anses onormalt för den här miljön och kan indikera att din resurs har komprometterats och används nu för råstyrkeattacker mot externa SSH-slutpunkten. Observera att den här typen av aktivitet skulle kunna göra att din IP-adress flaggas som skadlig av externa enheter.|
|**Misstänkt utgående SSH-nätverksaktivitet till flera mål**|Provade analysen av nätverkstrafik identifierade avvikande utgående SSH (Secure Shell)-kommunikation från en resurs i din distribution till flera destinationer. Den här aktiviteten anses onormalt för den här miljön och kan indikera att din resurs har komprometterats och används nu för råstyrkeattacker mot externa SSH-slutpunkter. Observera att den här typen av aktivitet skulle kunna göra att din IP-adress flaggas som skadlig av externa enheter.|
|**Misstänkt inkommande SSH-nätverksaktivitet från flera källor**|Provade analysen av nätverkstrafik identifierade avvikande inkommande SSH-kommunikation från flera källor till en resurs i distributionen. Olika unika IP-adresser ansluter till din resurs anses onormalt för den här miljön. Den här aktiviteten kan indikera ett försök till råstyrkeattacker mot din SSH-gränssnittet från flera värdar (Botnät).|
|**Misstänkt inkommande SSH-nätverksaktivitet**|Provade analysen av nätverkstrafik identifierade avvikande inkommande SSH-kommunikation till en resurs i distributionen. Relativt högt antal inkommande anslutningar till resursen anses onormalt för den här miljön. Den här aktiviteten kan indikera ett försök till råstyrkeattacker mot din SSH-gränssnittet.
|**Misstänkt inkommande RDP-nätverksaktivitet från flera källor**|Provade analysen av nätverkstrafik identifierade avvikande inkommande RDP-kommunikation från flera källor till en resurs i distributionen. Olika unika IP-adresser ansluter till din resurs anses onormalt för den här miljön. Den här aktiviteten kan indikera ett försök till råstyrkeattacker mot din RDP-gränssnittet från flera värdar (Botnät).|
|**Misstänkt inkommande RDP-nätverksaktivitet**|Provade analysen av nätverkstrafik identifierade avvikande inkommande RDP-kommunikation till en resurs i distributionen. Relativt högt antal inkommande anslutningar till resursen anses onormalt för den här miljön. Den här aktiviteten kan indikera ett försök till råstyrkeattacker mot din SSH-gränssnittet.|

Att förstå hur Security Center kan använda nätverk relaterade signaler att tillämpa skydd mot hot, se [heuristisk DNS identifieringar i Azure Security Center](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).
## Azure hanteringslager (Azure Resource Manager) (förhandsversion)<a name ="management-layer"></a>

>[!NOTE]
>Security Center skyddslager baserat på Azure Resource Manager är för närvarande i förhandsversion.

Security Center erbjuder ett extra skyddslager med hjälp av Azure Resource Manager-händelser, vilket anses vara kontrollplanet för Azure. Genom att analysera poster för Azure Resource Manager kan identifierar Security Center onormal eller potentiellt skadliga åtgärder i Azure-prenumeration-miljö.

> [!div class="mx-tableFixed"]

|Varning|Beskrivning|
|---|---|
|**MicroBurst toolkit kör**|Ett känt molnmiljö rekognosering-verktyg som körs har identifierats i din miljö. Verktyget ”MicroBurst” (se https://github.com/NetSPI/MicroBurst) kan användas av en angripare (eller penetrationstester tester) att mappa dina prenumerationer resurser, identifiera osäkert konfigurationer och läcka konfidentiell information.|
|**Azurite toolkit kör**|Ett känt molnmiljö rekognosering-verktyg som körs har identifierats i din miljö. Verktyget ”Azurite” (se https://github.com/mwrlabs/Azurite) kan användas av en angripare (eller penetrationstester tester) att mappa resurserna i prenumerationer och identifiera osäkert konfigurationer.|
|**Av misstänkt hanteringssession med ett inaktivt konto**|Analys av loggar för prenumeration aktivitet har identifierat ett misstänkt beteende. Ett huvudkonto som inte används under en lång tidsperiod, utför nu åtgärder som kan skydda persistence för en angripare.|
|**Av misstänkt hanteringssession med hjälp av PowerShell**|Analys av loggar för prenumeration aktivitet har identifierat ett misstänkt beteende. Ett huvudkonto som inte använder PowerShell regelbundet för att hantera prenumerationsmiljö är nu med hjälp av PowerShell och utföra åtgärder som skyddar persistence för en angripare.|
|**Användning av avancerade Azure persistence-tekniker**|Analys av loggar för prenumeration aktivitet har identifierat ett misstänkt beteende. Anpassade roller har fått legitimized identitet entiteter. Detta kan leda till att angriparen tar persistens i en Azure-kund-miljö.|
|**Aktivitet från ovanligt land**|Aktivitet från en plats som nyligen eller aldrig inte besöktes av någon användare i organisationen har uppstått.<br/>Den här identifieringen tar hänsyn till de senaste aktivitet platser att fastställa nya och ovanliga platser. Avvikelseidentifieringsmotorn lagrar information om tidigare platser som används av användarna i organisationen. 
|**Aktivitet från anonyma IP-adresser**|Användare aktivitet från en IP-adress som har identifierats som en anonym proxy IP-adress har identifierats. <br/>Dessa proxyservrar används av personer som vill dölja sina enheters IP-adress och kan användas i skadligt syfte. Den här identifieringen använder machine learning-algoritm som minskar ”FALSKT positiva resultat”, till exempel argumentantal taggade IP-adresser som ofta används av användare i organisationen.|
|**Omöjlig resa har identifierats**|Två användaraktiviteter (är en eller flera sessioner) som har inträffat kommer från geografiskt avlägsna platser inom en tidsperiod som är kortare än tid det skulle ha tagit användaren överföras från den första platsen till andra. Detta anger att en annan användare använder samma autentiseringsuppgifter. <br/>Den här identifieringen använder machine learning-algoritm som ignorerar uppenbara ”FALSKT positiva resultat” bidrar till omöjlig resa villkor, till exempel virtuella privata nätverk och platser som ofta används av andra användare i organisationen. Identifieringen har en inledande inlärningsperiod på sju dagar under vilken den lär sig den nya användarens aktivitetsmönster.|

>[!NOTE]
> Flera av de ovanstående analyser drivs av Microsoft Cloud App Security (MCAS). Om du vill dra nytta av dessa analytics, krävs en aktiverad MCAS-licens. Om du har en MCAS-licens kan är dessa aviseringar aktiverade som standard. Inaktivera dem:
>
> 1. I Security Center-bladet väljer **säkerhetsprincip**. För den prenumeration som du vill ändra, klickar du på **redigera inställningar för**.
> 2. Klicka på **Hotidentifiering**.
> 3. Under **för att göra integreringar**, avmarkera **Tillåt Microsoft Cloud App Security åtkomst till Mina data**, och klicka på **spara**.

>[!NOTE]
>Azure Security Center lagrar säkerhetsrelaterade kunddata i samma geografiska område som dess resurs. Om Microsoft inte har ännu distribuerats Azure Security Center i resursens geo, lagrar data i USA. Den här informationen lagras i enlighet med reglerna för geo-plats för MCAS när Microsoft Cloud App Security (MCAS) är aktiverat. Se [datalagring för icke-regionala tjänster för mer information](http://azuredatacentermap.azurewebsites.net/).
