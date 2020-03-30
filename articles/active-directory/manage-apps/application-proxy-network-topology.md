---
title: Nätverkstopologiöverväganden för Azure AD-programproxy
description: Täcker nätverkstopologiöverväganden när du använder Azure AD Application Proxy.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: eaceaf1f5e9b6e34ced5db39b61e607fffcb5953
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295142"
---
# <a name="network-topology-considerations-when-using-azure-active-directory-application-proxy"></a>Nätverkstopologiöverväganden när du använder Azure Active Directory Application Proxy

I den här artikeln beskrivs problem med nätverkstopologin när du använder Azure Active Directory (Azure AD) Application Proxy för publicering och åtkomst till dina program på distans.

## <a name="traffic-flow"></a>Trafikflöde

När ett program publiceras via Azure AD Application Proxy flödar trafiken från användarna till programmen via tre anslutningar:

1. Användaren ansluter till den offentliga slutpunkten för Azure AD-programproxytjänsten på Azure
1. Tjänsten Programproxy ansluter till Application Proxy-anslutningen
1. Application Proxy-anslutningen ansluter till målprogrammet

![Diagram som visar trafikflödet från användare till målprogram](./media/application-proxy-network-topology/application-proxy-three-hops.png)

## <a name="tenant-location-and-application-proxy-service"></a>Klientplats och programproxytjänst

När du registrerar dig för en Azure AD-klient bestäms regionen för din klient av det land/den region som du anger. När du aktiverar programproxy väljs eller skapas programproxy-tjänstinstanserna för din klientorganisation i samma region som din Azure AD-klientorganisation eller närmaste region.

Om din Azure AD-klients land eller region till exempel är Storbritannien använder alla application proxy-anslutningsappar tjänstinstanser i europeiska datacenter. När användarna får åtkomst till publicerade program går deras trafik via application proxy-tjänstinstanserna på den här platsen.

## <a name="considerations-for-reducing-latency"></a>Överväganden för att minska latensen

Alla proxylösningar introducerar svarstid i nätverksanslutningen. Oavsett vilken proxy- eller VPN-lösning du väljer som fjärråtkomstlösning innehåller den alltid en uppsättning servrar som gör det möjligt för anslutningen till ditt företagsnätverk.

Organisationer innehåller vanligtvis serverslutpunkter i sitt perimeternätverk. Med Azure AD Application Proxy flödar dock trafiken via proxytjänsten i molnet medan anslutningsapparna finns i företagets nätverk. Inget perimeternätverk krävs.

Nästa avsnitt innehåller ytterligare förslag som hjälper dig att minska svarstiden ytterligare. 

### <a name="connector-placement"></a>Placering av koppling

Programproxy väljer platsen för instanser för dig, baserat på din klientplats. Du får dock bestämma var kopplingen ska installeras, vilket ger dig möjlighet att definiera svarstidsegenskaperna för nätverkstrafiken.

Ställ in tjänsten Programproxy:

- Var finns appen?
- Var finns de flesta användare som kommer åt appen?
- Var finns application proxy-instansen?
- Har du redan konfigurerat en dedikerad nätverksanslutning till Azure-datacenter, till exempel Azure ExpressRoute eller ett liknande VPN?

Anslutningsappen måste kommunicera med både Azure och dina program (steg 2 och 3 i trafikflödesdiagrammet), så placeringen av kopplingen påverkar svarstiden för dessa två anslutningar. Tänk på följande när du utvärderar kopplingens placering:

- Om du vill använda Kerberos begränsad delegering (KCD) för enkel inloggning behöver kopplingen en siktlinje till ett datacenter. Dessutom måste anslutningsservern vara domänansluten.  
- Om du är osäker, installera kontakten närmare programmet.

### <a name="general-approach-to-minimize-latency"></a>Allmän metod för att minimera svarstid

Du kan minimera svarstiden för slut-till-slut-trafik genom att optimera varje nätverksanslutning. Varje anslutning kan optimeras genom:

- Minska avståndet mellan de två ändarna av hoppet.
- Välja rätt nätverk att passera. Det kan till exempel gå snabbare att gå igenom ett privat nätverk i stället för det offentliga Internet på grund av dedikerade länkar.

Om du har en dedikerad VPN- eller ExpressRoute-länk mellan Azure och ditt företagsnätverk kanske du vill använda den.

## <a name="focus-your-optimization-strategy"></a>Fokusera din optimeringsstrategi

Det finns lite du kan göra för att styra anslutningen mellan användarna och application proxy-tjänsten. Användare kan komma åt dina appar från ett hemnätverk, ett kafé eller ett annat land/en annan region. I stället kan du optimera anslutningarna från application proxy-tjänsten till Application Proxy-kopplingarna till apparna. Överväg att införliva följande mönster i din miljö.

### <a name="pattern-1-put-the-connector-close-to-the-application"></a>Mönster 1: Placera kontakten nära programmet

Placera kopplingen nära målprogrammet i kundnätverket. Den här konfigurationen minimerar steg 3 i topografidiagrammet, eftersom kopplingen och programmet är nära.

Om din anslutning behöver en siktlinje till domänkontrollanten är det här mönstret fördelaktigt. De flesta av våra kunder använder det här mönstret, eftersom det fungerar bra för de flesta scenarier. Detta mönster kan också kombineras med mönster 2 för att optimera trafiken mellan tjänsten och kontakten.

### <a name="pattern-2-take-advantage-of-expressroute-with-microsoft-peering"></a>Mönster 2: Dra nytta av ExpressRoute med Microsoft-peering

Om du har konfigurerat ExpressRoute med Microsoft-peering kan du använda den snabbare ExpressRoute-anslutningen för trafik mellan Programproxy och anslutningsappen. Anslutningen finns fortfarande i nätverket, nära appen.

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>Mönster 3: Dra nytta av ExpressRoute med privat peering

Om du har konfigurerat en dedikerad VPN eller ExpressRoute med privat peering mellan Azure och ditt företagsnätverk har du ett annat alternativ. I den här konfigurationen betraktas det virtuella nätverket i Azure vanligtvis som ett tillägg till företagsnätverket. Så du kan installera anslutningen i Azure-datacentret och ändå uppfylla de låga svarstidskraven för anslutnings-till-app-anslutningen.

Svarstiden äventyras inte eftersom trafiken flödar över en dedikerad anslutning. Du får också förbättrad svarstid för Application Proxy service-to-connector eftersom anslutningen är installerad i ett Azure-datacenter nära din Azure AD-klientplats.

![Diagram som visar anslutning installerad i ett Azure-datacenter](./media/application-proxy-network-topology/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>Andra tillvägagångssätt

Även om fokus för den här artikeln är anslutningsplacering kan du också ändra placeringen av programmet för att få bättre svarstidsegenskaper.

I allt högre grad flyttar organisationer sina nätverk till värdmiljöer. Detta gör det möjligt för dem att placera sina appar i en värdmiljö som också är en del av deras företagsnätverk och fortfarande vara inom domänen. I det här fallet kan de mönster som beskrivs i föregående avsnitt tillämpas på den nya programplatsen. Om du funderar på det här alternativet läser du [Azure AD Domain Services](../../active-directory-domain-services/overview.md).

Överväg dessutom att ordna anslutningsapparna med hjälp av [anslutningsgrupper](application-proxy-connector-groups.md) för att rikta appar som finns på olika platser och nätverk.

## <a name="common-use-cases"></a>Vanliga användarsituationer

I det här avsnittet går vi igenom några vanliga scenarier. Anta att Azure AD-klienten (och därmed proxytjänstens slutpunkt) finns i USA (USA). De överväganden som diskuteras i dessa användningsfall gäller även för andra regioner runt om i världen.

I de här scenarierna kallar vi varje anslutning för ett "hopp" och numrerar dem för enklare diskussion:

- **Hop 1**: Användare till tjänsten Programproxy
- **Hop 2:** Programproxy-tjänst till Application Proxy-anslutningen
- **Hop 3:** Application Proxy-anslutning till målprogrammet 

### <a name="use-case-1"></a>Användningsfall 1

**Scenario:** Appen finns i en organisations nätverk i USA, med användare i samma region. Det finns ingen ExpressRoute eller VPN mellan Azure-datacentret och företagsnätverket.

**Rekommendation:** Följ mönster 1, förklaras i föregående avsnitt. För förbättrad svarstid, överväg att använda ExpressRoute om det behövs.

Detta är ett enkelt mönster. Du optimerar hop 3 genom att placera kopplingen nära appen. Detta är också ett naturligt val, eftersom anslutningen vanligtvis installeras med siktlinje till appen och till datacentret för att utföra KCD-åtgärder.

![Diagram som visar användare, proxy, anslutning och app finns alla i USA](./media/application-proxy-network-topology/application-proxy-pattern1.png)

### <a name="use-case-2"></a>Användningsfall 2

**Scenario:** Appen finns i en organisations nätverk i USA, med användare utspridda globalt. Det finns ingen ExpressRoute eller VPN mellan Azure-datacentret och företagsnätverket.

**Rekommendation:** Följ mönster 1, förklaras i föregående avsnitt.

Återigen är det vanliga mönstret att optimera hop 3, där du placerar kopplingen nära appen. Hop 3 är vanligtvis inte dyrt, om allt är inom samma region. Dock kan hop 1 vara dyrare beroende på var användaren är, eftersom användare över hela världen måste komma åt Application Proxy-instansen i USA. Det är värt att notera att någon proxy lösning har liknande egenskaper när det gäller användare sprids ut globalt.

![Användare sprids globalt, men allt annat finns i USA](./media/application-proxy-network-topology/application-proxy-pattern2.png)

### <a name="use-case-3"></a>Användningsfall 3

**Scenario:** Appen finns i en organisations nätverk i USA. ExpressRoute med Microsoft-peering finns mellan Azure och företagsnätverket.

**Rekommendation:** Följ mönster 1 och 2, förklaras i föregående avsnitt.

Placera först kopplingen så nära appen som möjligt. Sedan använder systemet automatiskt ExpressRoute för hopp 2.

Om ExpressRoute-länken använder Microsoft-peering flödar trafiken mellan proxyn och kopplingen över den länken. Hop 2 har optimerad svarstid.

![Diagram som visar ExpressRoute mellan proxyn och kopplingen](./media/application-proxy-network-topology/application-proxy-pattern3.png)

### <a name="use-case-4"></a>Användningsfall 4

**Scenario:** Appen finns i en organisations nätverk i USA. ExpressRoute med privat peering finns mellan Azure och företagsnätverket.

**Rekommendation:** Följ mönster 3, förklaras i föregående avsnitt.

Placera anslutningen i Azure-datacentret som är anslutet till företagsnätverket via ExpressRoute-privat peering.

Anslutningen kan placeras i Azure-datacentret. Eftersom anslutningen fortfarande har en siktlinje till programmet och datacentret via det privata nätverket förblir hop 3 optimerad. Dessutom optimeras hop 2 ytterligare.

![Anslutning i Azure-datacenter, ExpressRoute mellan anslutning och app](./media/application-proxy-network-topology/application-proxy-pattern4.png)

### <a name="use-case-5"></a>Användningsfall 5

**Scenario:** Appen finns i en organisations nätverk i Europa, med Application Proxy-instansen och de flesta användare i USA.

**Rekommendation:** Placera kontakten nära appen. Eftersom användare i USA har åtkomst till en Application Proxy-instans som råkar vara i samma region är hop 1 inte för dyrt. Hop 3 är optimerad. Överväg att använda ExpressRoute för att optimera hop 2.

![Diagram visar användare och proxy i USA, anslutning och app i Europa](./media/application-proxy-network-topology/application-proxy-pattern5b.png)

Du kan också överväga att använda en annan variant i den här situationen. Om de flesta användare i organisationen är i USA, då är chansen att ditt nätverk sträcker sig till USA också. Placera anslutningen i USA och använd den dedikerade interna nätverkslinjen till programmet i Europa. På så sätt optimeras hopp 2 och 3.

![Diagram visar användare, proxy och anslutning i USA, app i Europa](./media/application-proxy-network-topology/application-proxy-pattern5c.png)

## <a name="next-steps"></a>Nästa steg

- [Aktivera programproxy](application-proxy-add-on-premises-application.md)
- [Aktivera enkel inloggning](application-proxy-configure-single-sign-on-with-kcd.md)
- [Aktivera villkorlig åtkomst](application-proxy-integrate-with-sharepoint-server.md)
- [Felsöka problem med Application Proxy](application-proxy-troubleshoot.md)
