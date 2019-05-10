---
title: Traffic Manager-Slutpunktstyper | Microsoft Docs
description: Den här artikeln beskriver olika typer av slutpunkter som kan användas med Azure Traffic Manager
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2017
ms.author: kumud
ms.openlocfilehash: dc76f56b6c05f22a380ff33715fe22e8c72e4891
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65508439"
---
# <a name="traffic-manager-endpoints"></a>Traffic Manager-slutpunkter
Microsoft Azure Traffic Manager kan du styra hur nätverkstrafiken distribueras till distribution av program som körs i olika datacenter. Du konfigurerar varje programdistribution som en slutpunkt i Traffic Manager. När Traffic Manager tar emot en DNS-begäran, väljer en slutpunkt som är tillgängliga för att returnera i DNS-svaret. Traffic manager baser valet aktuella statusen för slutpunkten och routning av nätverkstrafik-metoden. Mer information finns i [hur Traffic Manager fungerar](traffic-manager-how-it-works.md).

Det finns tre typer av slutpunkten som stöds av Traffic Manager:
* **Azure-slutpunkter** används för tjänsterna i Azure.
* **Externa slutpunkter** används för IPv4/IPv6-adresser, fullständiga domännamn, eller för tjänster som hanteras utanför Azure kan antingen vara en lokal eller med en annan värdleverantör.
* **Kapslade slutpunkter** används för att kombinera Traffic Manager-profiler om du vill skapa mer flexibel system för routning av nätverkstrafik som har stöd för större och mer komplexa distributioner.

Det finns ingen begränsning för hur slutpunkter för olika typer kombineras i en enda Traffic Manager-profil. Varje profil kan innehålla alla olika typer av slutpunkter.

I följande avsnitt beskrivs varje typ av slutpunkt i större detalj.

## <a name="azure-endpoints"></a>Azure-slutpunkter

Azure-slutpunkter används för Azure-baserade tjänster i Traffic Manager. Följande typer av Azure-resurs stöds:

* PaaS-molntjänster.
* Webbappar
* Webbappsplatser
* PublicIPAddress-resurser (som kan anslutas till virtuella datorer direkt eller via en Azure Load Balancer). PublicIpAddress måste ha ett DNS-namn som tilldelats som ska användas i en Traffic Manager-profil.

PublicIPAddress-resurser är Azure Resource Manager-resurser. De finns inte i den klassiska distributionsmodellen. De är därför endast stöds i Traffic Manager Azure Resource Manager upplevelser. Andra typer av slutpunkter stöds via både Resource Manager och den klassiska distributionsmodellen.

När du använder Azure-slutpunkter, identifierar Traffic Manager när en ”klassisk” IaaS VM, molntjänst eller en Webbapp stoppas och startas. Den här statusen visas i statusen för slutpunkten. Se [Traffic Manager endpoint monitoring](traffic-manager-monitoring.md#endpoint-and-profile-status) mer information. Om den underliggande tjänsten har stoppats, utför inte Traffic Manager hälsokontroller av slutpunkter eller dirigera trafik till slutpunkten. Inga fakturering Traffic Manager-händelser inträffar för den stoppade instansen. Fakturering återupptar och slutpunkten är berättigade att ta emot trafik när tjänsten startas. Den här identifieringen gäller inte för PublicIpAddress-slutpunkter.

## <a name="external-endpoints"></a>Externa slutpunkter

Externa slutpunkter som används för antingen IPv4/IPv6-adresser, fullständiga domännamn, eller för tjänster utanför Azure. Användning av IPv4/IPv6-adress-slutpunkter kan traffic manager för att kontrollera hälsotillståndet för slutpunkter utan att ett DNS-namn för dessa. Traffic Manager kan därför kan svara på frågor med A/AAAA-poster när slutpunkten och returnerar ett svar. Tjänster utanför Azure är en tjänst som finns lokalt eller med en annan provider. Externa slutpunkter kan användas enskilt eller tillsammans med Azure-slutpunkter i samma Traffic Manager-profilen förutom slutpunkter som angetts som IPv4 eller IPv6-adresser som bara kan vara externa slutpunkter. Kombinera Azure-slutpunkter med externa slutpunkter kan olika scenarier:

* Ger ökad redundans för ett befintligt lokalt program i antingen en aktiv-aktiv eller aktiv-passiv redundans-modell som använder Azure. 
* Dirigera trafik till slutpunkter som inte har ett DNS-namn som är kopplade till standardrisknivåer. Dessutom kan minska den övergripande svarstiden för DNS-sökning genom att ta bort behovet av att köra en andra DNS-fråga för att få en IP-adressen för en DNS-namn som returneras. 
* Sänk svarstiderna i programmet för användare i hela världen, utöka ett befintligt lokala program till fler geografiska platser i Azure. Mer information finns i [Traffic Manager ”Performance” trafikroutning](traffic-manager-routing-methods.md#performance).
* Ge ytterligare kapacitet för en befintlig lokala program, kontinuerligt eller som en ”burst-till-moln”-lösning som uppfyller en topp i begäran med hjälp av Azure.

I vissa fall är det praktiskt att använda externa slutpunkter för att referera till Azure-tjänster (exempel finns i den [vanliga frågor och svar](traffic-manager-faqs.md#traffic-manager-endpoints)). I det här fallet faktureras hälsokontroller enligt priset för Azure-slutpunkter, inte den externa slutpunkter hastigheten. Men till skillnad från Azure-slutpunkter, om du stoppar eller ta bort den underliggande tjänsten hälsokontrollen fakturering fortsätter tills du inaktiverar eller ta bort slutpunkt i Traffic Manager.

## <a name="nested-endpoints"></a>Kapslade slutpunkter

Kapslade slutpunkter kombinera flera Traffic Manager-profiler för att skapa flexibla scheman för routning av nätverkstrafik och har stöd för större, komplexa distributioner. Med kapslade slutpunkter läggs en ”underordnad”-profil som en slutpunkt för en ”överordnad”-profil. Över- och underordnade profiler kan innehålla andra slutpunkter av valfri typ, inklusive andra kapslade profiler. Mer information finns i [kapslade Traffic Manager-profiler](traffic-manager-nested-profiles.md).

## <a name="web-apps-as-endpoints"></a>Web Apps som slutpunkter

Vissa ytterligare förutsättningar gäller när du konfigurerar Webbappar som slutpunkter i Traffic Manager:

1. Endast Webbappar på ”Standard” SKU eller högre är tillämpliga för användning med Traffic Manager. Försök att lägga till en Webbapp med en lägre SKU misslyckas. SKU-nedgradering av en befintlig Webbapp resulterar i Traffic Manager inte längre skickar trafik till Webbappen. Mer information om stöds planer finns i den [App Service-planer](https://azure.microsoft.com/pricing/details/app-service/plans/)
2. När en slutpunkt får en HTTP-förfrågan, används 'host'-huvudet i begäran för att avgöra vilken Webbapp som ska hantera begäran. Värdhuvudet innehåller DNS-namn som används för att initiera begäran, till exempel ”contosoapp.azurewebsites.net”. Om du vill använda ett annat DNS-namn med ditt webbprogram, måste DNS-namnet registreras som ett anpassat domännamn för appen. När du lägger till en Web App-slutpunkt som en Azure-slutpunkt, registreras automatiskt Traffic Manager profilen DNS-namn för appen. Denna registrering tas bort automatiskt när slutpunkten tas bort.
3. Varje Traffic Manager-profil kan ha högst en Web App-slutpunkt från varje Azure-region. Undvik för den här begränsningen genom kan du konfigurera en Webbapp som en extern slutpunkt. Mer information finns i den [vanliga frågor och svar](traffic-manager-faqs.md#traffic-manager-endpoints).

## <a name="enabling-and-disabling-endpoints"></a>Aktivera och inaktivera slutpunkter

Inaktiveringen av en slutpunkt i Traffic Manager kan vara praktiskt att tillfälligt ta bort trafik från en slutpunkt som är i underhållsläge eller som omdistribueras. När slutpunkten körs igen kan aktiveras den igen.

Slutpunkter kan aktiveras och inaktiveras via Traffic Manager-portalen, PowerShell, CLI eller REST API.

> [!NOTE]
> Inaktiveringen av en Azure slutpunkt har inget samband med dess Distributionsstatus i Azure. En Azure-tjänst (till exempel en virtuell dator eller Web App fortfarande körs och kan ta emot trafik även om den är inaktiverad i Traffic Manager. Trafiken kan åtgärdas direkt till tjänstinstansen i stället för via DNS-namnet på Traffic Manager-profilen. Mer information finns i [så här fungerar Traffic Manager](traffic-manager-how-it-works.md).

Aktuell kvalificering för varje slutpunkt för att ta emot trafik beror på följande faktorer:

* Profilstatus (aktiverat/inaktiverat)
* Statusen för slutpunkten (aktiverat/inaktiverat)
* Resultaten av söker efter att slutpunkten

Mer information finns i [Traffic Manager endpoint monitoring](traffic-manager-monitoring.md#endpoint-and-profile-status).

> [!NOTE]
> Eftersom Traffic Manager fungerar på DNS-nivå, är det att påverka befintliga anslutningar till valfri slutpunkt. När en slutpunkt är inte tillgänglig, leder Traffic Manager nya anslutningar till en annan tillgänglig slutpunkt. Värden bakom inaktiverad eller skadad slutpunkten kan dock fortsätta att ta emot trafik via befintliga anslutningar förrän dessa sessioner avslutas. Program bör begränsa hela sessionen för att tillåta trafik att tömma från befintliga anslutningar.

Om alla slutpunkter i en profil har inaktiverats, eller om själva profilen är inaktiverad, skickar ett ”NXDOMAIN”-svar till en ny DNS-fråga med Traffic Manager.


## <a name="next-steps"></a>Nästa steg

* Lär dig [så här fungerar Traffic Manager](traffic-manager-how-it-works.md).
* Läs mer om Traffic Manager [slutpunkt för övervakning och automatisk redundans](traffic-manager-monitoring.md).
* Läs mer om Traffic Manager [trafikroutningsmetoder](traffic-manager-routing-methods.md).
