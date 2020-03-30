---
title: Slutpunktstyper för Trafikhanteraren | Microsoft-dokument
description: I den här artikeln beskrivs olika typer av slutpunkter som kan användas med Azure Traffic Manager
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2017
ms.author: rohink
ms.openlocfilehash: 3d8f899a7899243129d31c2620a51dc764a8e917
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250938"
---
# <a name="traffic-manager-endpoints"></a>Traffic Manager-slutpunkter

Med Microsoft Azure Traffic Manager kan du styra hur nätverkstrafik distribueras till programdistributioner som körs i olika datacenter. Du konfigurerar varje programdistribution som en ”slutpunkt” i Traffic Manager. När Traffic Manager tar emot en DNS-begäran väljer den en tillgänglig slutpunkt att returnera i DNS-svaret. Traffic Manager baserar valet på den aktuella slutpunktsstatusen och trafikroutningsmetoden. Mer information finns i [Så här fungerar Traffic Manager](traffic-manager-how-it-works.md).

Det finns tre typer av slutpunkter som stöds av Traffic Manager:

* **Azure-slutpunkter** används för tjänster som värdhanteras i Azure.
* **Externa slutpunkter** används för IPv4-/IPv6-adresser, FQDN eller för tjänster som värdhanteras utanför Azure och som antingen kan vara lokala eller använda en annan värdleverantör.
* **Kapslade slutpunkter** används till att kombinera Traffic Manager-profiler för att skapa mer flexibla trafikdirigeringsscheman som stöd för större, mer komplexa distributioner.

Det finns ingen begränsning för hur slutpunkter av olika typer kombineras i en och samma Traffic Manager-profil. Varje profil kan innehålla valfri blandning av slutpunktstyper.

I följande avsnitt beskrivs varje slutpunktstyp på större djup.

## <a name="azure-endpoints"></a>Slutpunkter i Azure

Azure-slutpunkter används för Azure-baserade tjänster i Traffic Manager. Följande Azure-resurstyper stöds:

* PaaS molntjänster.
* Web Apps
* Platser för webbapp
* PublicIPAdress-resurser (som kan anslutas till virtuella datorer antingen direkt eller via en Azure Load Balancer). Den offentliga Åklagaren måste ha ett DNS-namn tilldelat för att kunna användas i en Traffic Manager-profil.

PublicIPAddress-resurser är Azure Resource Manager-resurser. De finns inte i den klassiska distributionsmodellen. På så sätt stöds de bara i Traffic Manager Azure Resource Manager-upplevelser. De andra slutpunktstyperna stöds via både Resource Manager och den klassiska distributionsmodellen.

När du använder Azure-slutpunkter identifierar Traffic Manager när en webbapp stoppas och startas. Den här statusen återspeglas i slutpunktsstatusen. Mer information finns i [Slutpunktsövervakning](traffic-manager-monitoring.md#endpoint-and-profile-status) i Traffic Manager. När den underliggande tjänsten stoppas utför Traffic Manager inte hälsokontroller av slutpunkter eller dirigerar trafik till slutpunkten. Inga Traffic Manager-faktureringshändelser inträffar för den stoppade instansen. När tjänsten startas om återupptas faktureringen och slutpunkten kan ta emot trafik. Den här identifieringen gäller inte för PublicIpAddress-slutpunkter.

## <a name="external-endpoints"></a>Externa slutpunkter

Externa slutpunkter används för antingen IPv4/IPv6-adresser, FQDN:er eller för tjänster utanför Azure. Med hjälp av IPv4/IPv6-adressslutpunkter kan trafikhanteraren kontrollera slutpunkternas hälsa utan att kräva ett DNS-namn för dem. Därför kan Traffic Manager svara på frågor med A/AAAA-poster när slutpunkten returneras i ett svar. Tjänster utanför Azure kan innehålla en tjänst som finns lokalt eller med en annan leverantör. Externa slutpunkter kan användas individuellt eller kombineras med Azure-slutpunkter i samma Traffic Manager-profil förutom slutpunkter som anges som IPv4- eller IPv6-adresser som bara kan vara externa slutpunkter. Genom att kombinera Azure-slutpunkter med externa slutpunkter kan olika scenarier:

* Ge ökad redundans för ett befintligt lokalt program i antingen en aktiv eller aktiv-passiv redundansmodell med Azure. 
* Dirigera trafik till slutpunkter som inte har ett DNS-namn kopplat till dem. Dessutom minskar du den totala DNS-svarstiden genom att ta bort behovet av att köra en andra DNS-fråga för att få en IP-adress för ett DNS-namn returnerat.
* Minska programfördröjningen för användare runt om i världen, utöka ett befintligt lokalt program till ytterligare geografiska platser i Azure. Mer information finns i [Traffic Manager 'Prestanda-trafikroutning](traffic-manager-routing-methods.md#performance).
* Ge ytterligare kapacitet för ett befintligt lokalt program, antingen kontinuerligt eller som en burst-to-cloud-lösning för att möta en ökning av efterfrågan med Azure.

I vissa fall är det användbart att använda externa slutpunkter för att referera till Azure-tjänster (för exempel, se [vanliga frågor och svar](traffic-manager-faqs.md#traffic-manager-endpoints)). I det här fallet faktureras hälsokontroller med Azure-slutpunkter, inte hastigheten för externa slutpunkter. Till skillnad från Azure-slutpunkter, om du stoppar eller tar bort den underliggande tjänsten, fortsätter hälsokontrollfaktureringen tills du inaktiverar eller tar bort slutpunkten i Traffic Manager.

## <a name="nested-endpoints"></a>Kapslade slutpunkter

Kapslade slutpunkter kombinerar flera Traffic Manager-profiler för att skapa flexibla trafikroutningsscheman och stöder behoven hos större, komplexa distributioner. Med kapslade slutpunkter läggs en "underordnad" profil till som en slutpunkt i en överordnad profil. Både de underordnade och överordnade profilerna kan innehålla andra slutpunkter av alla typer, inklusive andra kapslade profiler. Mer information finns i [kapslade Traffic Manager-profiler](traffic-manager-nested-profiles.md).

## <a name="web-apps-as-endpoints"></a>Web Apps som slutpunkter

Några ytterligare överväganden gäller när du konfigurerar Webbappar som slutpunkter i Traffic Manager:

1. Endast Web Apps på Standard SKU eller högre är berättigade att användas med Traffic Manager. Försök att lägga till en webbapp med en lägre SKU misslyckas. Nedgradering av SKU för en befintlig webbapp resulterar i Traffic Manager inte längre skicka trafik till den Web App. Mer information om planer som stöds finns i [App Service Plans](https://azure.microsoft.com/pricing/details/app-service/plans/)
2. När en slutpunkt tar emot en HTTP-begäran används värdhuvudet i begäran för att avgöra vilken Webbapp som ska serva begäran. Värdhuvudet innehåller DET DNS-namn som används för att initiera begäran, till exempel "contosoapp.azurewebsites.net". Om du vill använda ett annat DNS-namn med din webbapp måste DNS-namnet registreras som ett anpassat domännamn för Appen. När du lägger till en Web App-slutpunkt som en Azure-slutpunkt registreras TRAFFIC Manager-profilens DNS-namn automatiskt för appen. Den här registreringen tas automatiskt bort när slutpunkten tas bort.
3. Varje Traffic Manager-profil kan ha högst en Web App-slutpunkt från varje Azure-region. Om du vill ta dig runt för det här villkoret kan du konfigurera en webbapp som en extern slutpunkt. Mer information finns i [vanliga frågor](traffic-manager-faqs.md#traffic-manager-endpoints)och svar .

## <a name="enabling-and-disabling-endpoints"></a>Aktivera och inaktivera slutpunkter

Det kan vara användbart att inaktivera en slutpunkt i Traffic Manager om du tillfälligt vill ta bort trafik från en slutpunkt som är i underhållsläge eller som distribueras om. När slutpunkten körs igen kan den återaktiveras.

Slutpunkter kan aktiveras och inaktiveras via Traffic Manager-portalen, PowerShell, CLI eller REST API.

> [!NOTE]
> Inaktivera en Azure-slutpunkt har ingenting att göra med dess distributionstillstånd i Azure. En Azure-tjänst (till exempel en virtuell dator eller en webbapp körs och kan ta emot trafik även när den är inaktiverad i Traffic Manager. Trafiken kan adresseras direkt till tjänstinstansen i stället för via Traffic Manager-profilens DNS-namn. Mer information finns i [hur Traffic Manager fungerar](traffic-manager-how-it-works.md).

Den aktuella berättigandet för varje slutpunkt för att ta emot trafik beror på följande faktorer:

* Profilstatus (aktiverad/inaktiverad)
* Slutpunktsstatus (aktiverad/inaktiverad)
* Resultaten av hälsokontrollerna för den slutpunkten

Mer information finns i [Traffic Manager-slutpunktsövervakning](traffic-manager-monitoring.md#endpoint-and-profile-status).

> [!NOTE]
> Eftersom Traffic Manager fungerar på DNS-nivå kan den inte påverka befintliga anslutningar till någon slutpunkt. När en slutpunkt inte är tillgänglig dirigerar Traffic Manager nya anslutningar till en annan tillgänglig slutpunkt. Värden bakom den inaktiverade eller felaktiga slutpunkten kan dock fortsätta att ta emot trafik via befintliga anslutningar tills dessa sessioner avslutas. Program bör begränsa sessionens varaktighet så att trafiken kan tömmas från befintliga anslutningar.

Om alla slutpunkter i en profil är inaktiverade, eller om själva profilen är inaktiverad, skickar Traffic Manager ett NXDOMAIN-svar till en ny DNS-fråga.

## <a name="faqs"></a>Vanliga frågor och svar

* [Kan jag använda Traffic Manager med slutpunkter från flera prenumerationer?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions)

* [Kan jag använda Traffic Manager med Cloud Service 'Staging'-platser?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-with-cloud-service-staging-slots)

* [Stöder Traffic Manager IPv6-slutpunkter?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-manager-support-ipv6-endpoints)

* [Kan jag använda Traffic Manager med mer än en webbapp i samma region?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region)

* [Hur flyttar jag min Traffic Manager-profils Azure-slutpunkter till en annan resursgrupp?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group-or-subscription)

## <a name="next-steps"></a>Nästa steg

* Läs om [hur Traffic Manager fungerar](traffic-manager-how-it-works.md).
* Läs mer om [slutpunktsövervakning och automatisk redundanskontroll](traffic-manager-monitoring.md)i Traffic Manager .
* Läs mer om [trafikstyrningsmetoder i](traffic-manager-routing-methods.md)Traffic Manager .
