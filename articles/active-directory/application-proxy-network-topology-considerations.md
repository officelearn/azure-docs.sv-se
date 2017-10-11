---
title: "Nätverk topologiöverväganden när du använder Azure Active Directory Application Proxy | Microsoft Docs"
description: "Beskriver topologiöverväganden för nätverk när du använder Azure AD Application Proxy."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 11244e0044eef8441e3a37ab8aeff0da30dacdb8
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="network-topology-considerations-when-using-azure-active-directory-application-proxy"></a>Topologiöverväganden nätverk när du använder Azure Active Directory Application Proxy

Den här artikeln förklarar topologiöverväganden för nätverk när du använder Azure Active Directory (Azure AD) Application Proxy för att publicera och åtkomst till dina program via fjärranslutning.

## <a name="traffic-flow"></a>Trafikflöde

Om ett program har publicerats till Azure AD Application Proxy flödar trafik från användarna till programmen via tre anslutningar:

1. Användaren ansluter till Azure AD Application Proxy offentliga tjänstslutpunkten på Azure
2. Application Proxy-tjänsten ansluter till Application Proxy connector
3. Application Proxy connector ansluter till målprogrammet

![Diagram över trafikflödet från användaren till målprogrammet](./media/application-proxy-network-topologies/application-proxy-three-hops.png)

## <a name="tenant-location-and-application-proxy-service"></a>Klient-plats och tjänsten för Webbprogramproxy

När du registrerar dig för en Azure AD-klient bestäms regionen för din klient av det land som du anger. När du aktiverar Application Proxy valt tjänstinstanser programproxyn för din klient eller skapats i samma region som din Azure AD-klient eller till den närmaste regionen.

Till exempel om regionen för din Azure AD-klient är Europeiska unionen (EU), använda alla Application Proxy-kopplingar tjänstinstanser i Azure-datacenter i EU. När dina användare få tillgång till publicerade program, sin trafik som passerar tjänstinstanser Application Proxy på den här platsen.

## <a name="considerations-for-reducing-latency"></a>Överväganden för att minska svarstiden

Alla proxylösningar för introducera fördröjning i anslutningen. Oavsett vilken proxy- eller VPN-lösning som du väljer som din lösning för fjärråtkomst, innehåller den alltid en uppsättning servrar som möjliggör anslutning till i företagsnätverket.

Organisationer inkluderar vanligtvis server-slutpunkter i sina perimeternätverk. Med Azure AD Application Proxy, men flödar trafiken via proxytjänst i molnet medan kopplingar finns i företagsnätverket. Det krävs inga perimeternätverk.

I nästa avsnitt innehåller ytterligare förslag för att hjälpa dig att minska svarstiden ytterligare. 

### <a name="connector-placement"></a>Placering av koppling

Programproxy väljer placeringen av instanser för dig, baserat på klient-plats. Men kommer du till Bestäm var du vill installera connector, vilket ger dig möjlighet att definiera egenskaperna för fördröjning av nätverkstrafiken.

När du konfigurerar tjänsten Application Proxy kan du ställa följande frågor:

* Var finns appen?
* Var finns de flesta användare som har åtkomst till appen?
* Var finns Application Proxy-instans
* Du redan har en dedikerad nätverksanslutning till Azure-Datacenter konfigurera Azure ExpressRoute eller en liknande VPN?

Kopplingen har att kommunicera med både Azure och dina program (steg 2 och 3 i trafikflödesdiagram), så placeringen av kopplingen påverkar svarstiden för dessa två anslutningar. När du utvärderar placeringen av kopplingen, Tänk på följande:

* Om du vill använda Kerberos-begränsad delegering (KCD) för enkel inloggning måste kopplingen en fri ett datacenter. Dessutom måste connector-servern vara ansluten till en domän.  
* Installera connector närmare till programmet i osäker.

### <a name="general-approach-to-minimize-latency"></a>Allmän metod för att minimera svarstider

Du kan minimera svarstiden för slutpunkt till slutpunkt-trafik genom att optimera varje nätverksanslutning. Varje anslutning kan optimeras genom att:

* Minskar avståndet mellan de två parterna hopp.
* Om du väljer rätt nätverket för att bläddra i. Till exempel kan gå igenom ett privat nätverk i stället för det offentliga Internet gå snabbare, på grund av dedicerade länkar.

Om du har en fast VPN eller ExpressRoute-länk mellan Azure och företagets nätverk, kan du vill använda.

## <a name="focus-your-optimization-strategy"></a>Fokusera din strategi för optimering

Det finns mycket mer som du kan göra om du vill kontrollera anslutningen mellan användare och tjänsten Application Proxy. Användare kan komma åt dina appar från ett hemnätverk, ett kafé eller ett annat land. I stället kan du optimera anslutningar från tjänsten Application Proxy till Application Proxy-kopplingar för appar. Överväg att använda följande mönster i din miljö.

### <a name="pattern-1-put-the-connector-close-to-the-application"></a>Mönstret 1: Placera kopplingen nära programmet

Placera kopplingen nära målprogrammet i kundens nätverk. Den här konfigurationen minimerar steg 3 i diagrammet topografi eftersom anslutningen och program är nära. 

Om din anslutningstjänst måste en fri domänkontrollanten, är det fördelaktigt med det här mönstret. De flesta av våra kunder att använda det här mönstret eftersom det fungerar bra för de flesta scenarier. Det här mönstret kan även kombineras med mönstret 2 att optimera trafik mellan service och anslutningen.

### <a name="pattern-2-take-advantage-of-expressroute-with-public-peering"></a>Mönstret 2: Dra nytta av ExpressRoute med offentlig peering

Om du har konfigurerats med offentlig peering ExpressRoute kan använda du snabbare ExpressRoute-anslutningen för trafik mellan Application Proxy och anslutningen. Anslutningen är fortfarande i nätverket, nära appen.

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>Mönster 3: Dra nytta av ExpressRoute med privat peering

Om du har en dedikerad VPN eller ExpressRoute som konfigurerats med privat peering mellan Azure och företagets nätverk, har du ett annat alternativ. I den här konfigurationen anses vanligtvis det virtuella nätverket i Azure som ett tillägg till företagets nätverk. Så kan du installera connector i Azure-datacentret och fortfarande uppfyller kraven låg latens för anslutningen till app-anslutningen.

Latens manipuleras inte eftersom trafiken flödar via en dedikerad anslutning. Du kan också få bättre svarstid för Application Proxy-tjänstanslutning eftersom kopplingen är installerad i ett Azure-datacenter nära din plats för Azure AD-klient.

![Diagram över anslutning är installerad i ett Azure-datacenter](./media/application-proxy-network-topologies/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>Andra metoder

Den här artikeln fokuserar på placering av koppling, men du kan också ändra placeringen av programmet för att få bättre svarstid egenskaper.

Mer och mer flyttar organisationer sina nätverk i värdbaserade miljöer. Detta gör det enkelt att placera sina appar i en värdmiljö som ingår också i företagsnätverket, och fortfarande i domänen. I det här fallet kan de mönster som beskrivs i föregående avsnitt tillämpas på den nya platsen för programmet. Om du funderar på det här alternativet, se [Azure AD Domain Services](../active-directory-domain-services/active-directory-ds-overview.md).

Dessutom kan du överväga att ordna dina kopplingar med [connector grupper](active-directory-application-proxy-connectors.md) till mål-appar som finns i olika platser och nätverk. 

## <a name="common-use-cases"></a>Vanliga användarsituationer

I det här avsnittet går vi igenom några vanliga scenarier. Anta att den Azure AD-klient (och därför proxy tjänstslutpunkten) finns i USA (US). Överväganden som beskrivs i dessa fall gäller även för andra regioner över hela världen användning.

För dessa scenarier kan vi anropa ett ”hopp” för varje anslutning och antal dem lättare beskrivning

- **Hopp 1**: användaren att tjänsten Application Proxy
- **Hopp 2**: Application Proxy-tjänsten till Application Proxy connector
- **Hopp 3**: Application Proxy connector till målprogrammet 

### <a name="use-case-1"></a>Användningsfall 1

**Scenario:** appen är i en organisations nätverk i USA med användare i samma region. Inga ExpressRoute eller VPN finns mellan Azure-datacentret och företagets nätverk.

**Rekommendation:** följer mönstret 1, som beskrivs i föregående avsnitt. Överväg att använda ExpressRoute för bättre svarstid, om det behövs.

Det här är ett enkelt mönster. Du kan optimera hopp 3 genom att placera kopplingen nära appen. Detta är också en naturlig valet eftersom anslutningen installeras normalt med fri till appen och till att utföra åtgärder för KCD-datacenter.

![Diagram över användare, proxy, koppling och app finns i USA](./media/application-proxy-network-topologies/application-proxy-pattern1.png)

### <a name="use-case-2"></a>Användningsfall 2

**Scenario:** appen är i en organisations nätverk i USA med användare som spridda globalt. Inga ExpressRoute eller VPN finns mellan Azure-datacentret och företagets nätverk.

**Rekommendation:** följer mönstret 1, som beskrivs i föregående avsnitt. 

Igen är det vanliga mönstret att optimera hopp 3, placerar du kopplingen nära appen. Hopp 3 är inte vanligtvis dyrbara, om det är allt i samma region. Hopp 1 kan dock vara dyrare beroende på var användaren finns, eftersom användare över hela världen måste ha åtkomst till Application Proxy-instans i USA. Det är värt att nämna att en proxy-lösning har liknande egenskaper för användare att sprida ut globalt.

![Diagram som visar som användare sprids globalt, men proxy-, anslutnings- och app finns i USA](./media/application-proxy-network-topologies/application-proxy-pattern2.png)

### <a name="use-case-3"></a>Användningsfall 3

**Scenario:** appen är i en organisations nätverk i USA. ExpressRoute med offentlig peering finns mellan Azure och företagets nätverk.

**Rekommendation:** följer mönster 1 och 2, som beskrivs i föregående avsnitt.

Placera först anslutaren så nära som möjligt till appen. Systemet använder sedan automatiskt ExpressRoute för hopp 2. 

Om länken ExpressRoute använder offentlig peering, överlappar trafiken mellan proxyservern och connector länken. Hopp 2 har optimerats svarstid.

![Diagram över ExpressRoute mellan proxyservern och connector](./media/application-proxy-network-topologies/application-proxy-pattern3.png)

### <a name="use-case-4"></a>Användningsfall 4

**Scenario:** appen är i en organisations nätverk i USA. ExpressRoute med privat peering finns mellan Azure och företagets nätverk.

**Rekommendation:** följer mönstret 3, som beskrivs i föregående avsnitt.

Placera kopplingen i Azure-datacentret som är ansluten till företagsnätverket via privata ExpressRoute-peering. 

Kopplingen kan placeras i Azure-datacenter. Eftersom anslutningen har ändå en fri programmet och datacenter via det privata nätverket, förblir hopp 3 optimerad. Dessutom kan optimeras hopp 2 ytterligare.

![Diagram över kopplingen i ett Azure-datacenter och ExpressRoute mellan koppling och appen](./media/application-proxy-network-topologies/application-proxy-pattern4.png)

### <a name="use-case-5"></a>Användningsfall 5

**Scenario:** appen är i en organisations nätverk i Europa med Application Proxy-instans och de flesta användare i USA.

**Rekommendation:** placera kopplingen nära appen. Eftersom USA användarna kommer åt en Application Proxy-instans som befinner sig i samma region, är hopp 1 inte för dyr. Hopp 3 är optimerad. Överväg att använda ExpressRoute för att optimera hopp 2. 

![Diagram över användare och proxy i USA med kopplingen och appen i EU](./media/application-proxy-network-topologies/application-proxy-pattern5b.png)

Du kan också använda en variant i den här situationen. Om de flesta användare i organisationen finns i USA, så risken som utökar ditt nätverk till USA samt. Placera kopplingen i USA och använda dedicerade interna företagsnätverket raden till programmet i EU. Det här sättet hopp 2 och 3 är optimerad.

![Diagram över användare och proxy connector i USA-app i EU](./media/application-proxy-network-topologies/application-proxy-pattern5c.png)

## <a name="next-steps"></a>Nästa steg

- [Aktivera Application Proxy](active-directory-application-proxy-enable.md)
- [Aktivera enkel inloggning](active-directory-application-proxy-sso-using-kcd.md)
- [Aktivera villkorlig åtkomst](active-directory-application-proxy-conditional-access.md)
- [Felsökning av problem med Application Proxy](active-directory-application-proxy-troubleshoot.md)
