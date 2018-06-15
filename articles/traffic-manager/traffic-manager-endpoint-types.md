---
title: Traffic Manager-Slutpunktstyper | Microsoft Docs
description: Den här artikeln beskrivs olika typer av slutpunkter som kan användas med Azure Traffic Manager
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: timlt
editor: ''
ms.assetid: 4e506986-f78d-41d1-becf-56c8516e4d21
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2017
ms.author: kumud
ms.openlocfilehash: 792712e3e529d77ff20a7603b5fbf028ca60f8c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23877519"
---
# <a name="traffic-manager-endpoints"></a>Traffic Manager-slutpunkter
Microsoft Azure Traffic Manager kan du styra hur nätverkstrafik distribueras till distribution av program körs i olika datacenter. Konfigurera varje programdistribution som en slutpunkt i Traffic Manager. När Traffic Manager tar emot en begäran om DNS-, väljer en tillgänglig slutpunkt ska returneras i DNS-svaret. Traffic manager baserar valet på den aktuella statusen för slutpunkten och metoden routning av nätverkstrafik. Mer information finns i [så Traffic Manager fungerar](traffic-manager-how-traffic-manager-works.md).

Det finns tre typer av slutpunkten som stöds av Traffic Manager:
* **Azure-slutpunkter** används för tjänster i Azure.
* **Externa slutpunkter** används för tjänster som finns utanför Azure, antingen lokalt eller med en annan värd provider.
* **Kapslade slutpunkter** används för att kombinera Traffic Manager-profiler för att skapa mer flexibla system för routning av nätverkstrafik som har stöd för större och mer komplexa distributioner.

Det finns ingen begränsning för hur slutpunkter av olika typer som kombineras i en enda Traffic Manager-profil. Varje profil kan innehåller en blandning av olika typer av slutpunkter.

I följande avsnitt beskrivs varje typ av slutpunkt i större djup.

## <a name="azure-endpoints"></a>Azure-slutpunkter

Azure-slutpunkter används för Azure-baserade tjänster i Traffic Manager. Följande typer av Azure-resurs stöds:

* 'Klassiskt' IaaS-VM och PaaS-molntjänster.
* Web Apps
* PublicIPAddress resurser (som kan vara anslutna till virtuella datorer direkt eller via en Azure belastningsutjämnare). PublicIpAddress måste ha ett DNS-namn som tilldelats som ska användas i en Traffic Manager-profilen.

PublicIPAddress resurser är Azure Resource Manager-resurser. De finns inte i den klassiska distributionsmodellen. Därför är de enda stöds i Traffic Manager-Azure Resource Manager upplevelser. Andra typer av slutpunkter stöds via både Resource Manager och den klassiska distributionsmodellen.

När du använder Azure-slutpunkter, identifierar Traffic Manager när en 'Klassiskt' IaaS VM, molnbaserad tjänst eller ett webbprogram stoppas och startas. Den här statusen visas i status för endpoint. Se [Traffic Manager slutpunktsövervakning](traffic-manager-monitoring.md#endpoint-and-profile-status) mer information. När den underliggande tjänsten har stoppats Traffic Manager inte att utföra hälsokontroller för slutpunkten eller trafiken till slutpunkten. Ingen fakturering Traffic Manager-händelser inträffar för den stoppade instansen. När tjänsten startas, kan fakturering återupptar och slutpunkten ta emot trafik. Denna identifiering gäller inte för PublicIpAddress-slutpunkter.

## <a name="external-endpoints"></a>Externa slutpunkter

Externa slutpunkter används för utanför Azure-tjänster. Till exempel en tjänst finns lokalt eller med en annan provider. Externa slutpunkter kan användas enskilt eller tillsammans med Azure-slutpunkter i samma Traffic Manager-profilen. Kombinera Azure-slutpunkter med externa slutpunkter kan olika scenarier:

* Använda Azure för att ge ökad redundans för en befintlig lokal program i antingen en aktiv-aktiv eller aktivt-passivt växling modell.
* Om du vill minska svarstiden för programmet för användare i världen, utöka ett befintligt lokalt program till ytterligare geografiska platser i Azure. Mer information finns i [Traffic Manager-prestanda' routning av nätverkstrafik](traffic-manager-routing-methods.md#performance).
* Använd Azure för att ge ytterligare kapacitet för en befintlig lokal program kontinuerligt eller som en ”burst-to-cloud-lösning som uppfyller en topp i begäran.

I vissa fall är det bra att använda externa slutpunkter för att referera till Azure-tjänster (exempel finns i [vanliga frågor och svar](traffic-manager-faqs.md#traffic-manager-endpoints)). I det här fallet debiteras hälsokontroller med Azure-slutpunkter hastighet, inte den externa slutpunkter hastigheten. Men till skillnad från Azure-slutpunkter, om du stoppar eller ta bort den underliggande tjänsten hälsokontroll fakturering fortsätter tills du inaktivera eller ta bort Traffic Manager-slutpunkten.

## <a name="nested-endpoints"></a>Kapslade slutpunkter

Kapslade slutpunkter kombinera flera Traffic Manager-profiler för att skapa flexibla routning av nätverkstrafik scheman och har stöd för större, komplexa distributioner. Med kapslade slutpunkter läggs en 'child'-profil som en slutpunkt i en profil för ”överordnad”. Över- och underordnade profiler kan innehålla slutpunkter av valfri typ, inklusive andra kapslade profiler. Mer information finns i [kapslade Traffic Manager-profiler](traffic-manager-nested-profiles.md).

## <a name="web-apps-as-endpoints"></a>Web Apps som slutpunkter

Vissa ytterligare förutsättningar gäller när du konfigurerar webbprogram som slutpunkter i Traffic Manager:

1. Endast Web Apps på ”Standard” SKU: N eller högre är berättigade för användning med Traffic Manager. Försök att lägga till en Webbapp för en lägre SKU: N misslyckas. Nedgradera SKU av en befintlig Webbapp resulterar i Traffic Manager längre skickar trafik till det webbprogram.
2. När en slutpunkt tar emot en HTTP-begäran används 'host'-huvud i begäran för att avgöra vilka webbprogram som ska hantera begäran. Värdadressen innehåller det DNS-namnet som används för att initiera begäran, till exempel 'contosoapp.azurewebsites.net'. Om du vill använda ett annat DNS-namn med ditt webbprogram, måste DNS-namnet registreras som ett anpassat domännamn för appen. När du lägger till en slutpunkt för webbprogram som en Azure slutpunkt registreras automatiskt DNS-namnet för Traffic Manager-profil för appen. Den här registreringen tas bort automatiskt när slutpunkten har tagits bort.
3. Varje Traffic Manager-profilen kan ha högst en Web App slutpunkt från varje Azure-region. Du kan konfigurera ett webbprogram som en extern slutpunkt löser för den här begränsningen. Mer information finns i [vanliga frågor och svar](traffic-manager-faqs.md#traffic-manager-endpoints).

## <a name="enabling-and-disabling-endpoints"></a>Aktivera och inaktivera slutpunkter

Inaktiveringen av en slutpunkt i Traffic Manager kan vara praktiskt att tillfälligt ta bort trafik från en slutpunkt som är i underhållsläge eller som omdistribueras. När slutpunkten är igång igen kan aktiveras den igen.

Slutpunkter kan aktiveras och inaktiveras via Traffic Manager-portal, PowerShell, CLI eller REST API som stöds i både Resource Manager och den klassiska distributionsmodellen.

> [!NOTE]
> Inaktiveringen av en Azure slutpunkt har inget samband med dess Distributionsstatus i Azure. En Azure-tjänst (som en virtuell dator eller Web App förblir körs och kan ta emot trafik även om inaktiverad i Traffic Manager. Trafik kan åtgärdas direkt till instansen för tjänsten i stället för via DNS-namnet på Traffic Manager-profilen. Mer information finns i [hur Traffic Manager fungerar](traffic-manager-how-traffic-manager-works.md).

Aktuella förutsättningarna för varje slutpunkt som tar emot trafik beror på följande faktorer:

* Profilstatusen (aktiverat/inaktiverat)
* Status för endpoint (aktiverat/inaktiverat)
* Resultaten av kontrollerar för denna slutpunkt

Mer information finns i [Traffic Manager slutpunktsövervakning](traffic-manager-monitoring.md#endpoint-and-profile-status).

> [!NOTE]
> Eftersom Traffic Manager fungerar på DNS-nivå, är det att påverka befintliga anslutningar till valfri slutpunkt. När en slutpunkt är inte tillgänglig, leder nya anslutningar till en annan tillgänglig slutpunkt i Traffic Manager. Värden bakom inaktiverade eller dåligt slutpunkten kan dock fortsätta att ta emot trafik via befintliga anslutningar förrän sessionerna avslutas. Program bör begränsa varaktighet för sessionen för att tillåta trafik att tömma från befintliga anslutningar.

Om alla slutpunkter i en profil är inaktiverade, eller om själva profilen är inaktiverad, skickar ett 'NXDOMAIN' svar till en ny DNS-fråga med Traffic Manager.


## <a name="next-steps"></a>Nästa steg

* Läs [hur Traffic Manager fungerar](traffic-manager-how-traffic-manager-works.md).
* Lär dig mer om Traffic Manager [endpoint övervaknings- och automatisk redundans](traffic-manager-monitoring.md).
* Lär dig mer om Traffic Manager [trafikroutningsmetoder](traffic-manager-routing-methods.md).
