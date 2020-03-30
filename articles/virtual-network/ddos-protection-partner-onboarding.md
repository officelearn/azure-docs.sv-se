---
title: Samarbetar med Azure DDoS Protection Standard
description: Förstå partneringmöjligheter som aktiveras av Azure DDoS Protection Standard.
ms.service: virtual-network
documentationcenter: na
author: anupamvi
mms.devlang: na
ms.topic: article
ms.date: 01/28/2020
ms.author: kumud
ms.openlocfilehash: 39cb2f90b49725c78746df69505fad7160e1db92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76849687"
---
# <a name="partnering-with-azure-ddos-protection-standard"></a>Samarbetar med Azure DDoS Protection Standard
I den här artikeln beskrivs partneringmöjligheter som aktiveras av Azure DDoS Protection Standard. Den här artikeln är utformad för att hjälpa produktchefer och affärsutvecklingsroller att förstå investeringsvägarna och ge insikt i partnering-värdeerbjudandena.

## <a name="background"></a>Bakgrund
DDoS-attacker (Distributed Denial of Service) är en av de viktigaste tillgänglighets- och säkerhetsproblemen som visas av kunder som flyttar sina program till molnet. Med utpressning och hacktivism är de vanligaste motiven bakom DDoS-attacker, de har konsekvent ökat i typ, skala och förekomstfrekvens eftersom de är relativt enkla och billiga att lansera.

Azure DDoS Protection ger motåtgärder mot de mest sofistikerade DDoS-hoten, vilket utnyttjar den globala omfattningen av Azure-nätverk. Tjänsten ger förbättrade DDoS-begränsningsfunktioner för program och resurser som distribueras i virtuella nätverk.

Teknikpartner kan skydda sina kunders resurser internt med Azure DDoS Protection Standard för att hantera tillgänglighets- och tillförlitlighetsproblem på grund av DDoS-attacker.

## <a name="introduction-to-azure-ddos-protection-standard"></a>Introduktion till Azure DDoS Protection Standard
Azure DDoS Protection Standard ger förbättrade DDoS-begränsningsfunktioner mot Layer 3- och Layer 4 DDoS-attacker. Följande är de viktigaste funktionerna i DDoS Protection Standard-tjänsten.

### <a name="adaptive-real-time-tuning"></a>Adaptiv justering i realtid
För alla skyddade program justerar Azure DDoS Protection Standard automatiskt DDoS-begränsningsprincipens tröskelvärden baserat på programmets trafikprofilmönster. Tjänsten utför den här anpassningen med hjälp av två insikter:

- Automatisk inlärning av trafikmönster per kund (per IP) för lager 3 och 4.
- Minimera falska positiva identifieringar, med tanke på att omfattningen av Azure gör det möjligt att absorbera en betydande mängd trafik.

![Adaptiv justering i realtid](./media/ddos-protection-partner-onboarding/real-time-tuning.png)

### <a name="attack-analytics-telemetry-monitoring-and-alerting"></a>Attackanalys, telemetri, övervakning och aviseringar
Azure DDoS Protection identifierar och minskar DDoS-attacker utan några åtgärder från användaren.

- Om den skyddade resursen finns i prenumerationen som omfattas av Azure Security Center skickar DDoS Protection Standard automatiskt en avisering till Security Center när en DDoS-attack identifieras och mildras mot det skyddade programmet.
- Alternativt, för att få ett meddelande när det finns en aktiv begränsning för en skyddad offentlig IP, kan du [konfigurera en avisering](manage-ddos-protection.md#configure-alerts-for-ddos-protection-metrics) om måttet under DDoS-attack eller inte.
- Du kan dessutom välja att skapa aviseringar för andra DDoS-mått och [konfigurera attackanalys](manage-ddos-protection.md#configure-ddos-attack-analytics) för att förstå omfattningen av attacken, trafik som släpps, attackvektorer, de främsta bidragsgivarna och andra detaljer.

![DDoS-mått](./media/ddos-protection-partner-onboarding/ddos-metrics.png)

### <a name="ddos-rapid-response-drr"></a>DDoS snabbrespons (DRR)
DDoS Protection Standard-kunder har tillgång till [Rapid Response-teamet](https://azure.microsoft.com/blog/ddos-protection-attack-analytics-rapid-response/) under en aktiv attack. DRR kan hjälpa till med attackutredning, anpassade mildrande åtgärder under en attack och analys efter attacken.

### <a name="sla-guarantee-and-cost-protection"></a>SLA-garanti och kostnadsskydd
DDoS Protection Standard-tjänsten omfattas av ett serviceavtal på 99,99 % och kostnadsskydd ger resurskrediter för att skala ut under en dokumenterad attack. Mer information finns i [SLA för Azure DDoS Protection](https://azure.microsoft.com/support/legal/sla/ddos-protection/v1_0/).

## <a name="featured-partner-scenarios"></a>Utvalda partnerscenarier
Följande är viktiga fördelar som du kan härleda genom att integrera med Azure DDoS Protection Standard:

- Partners erbjudna tjänster (belastningsutjämnare, brandvägg för webbprogram, brandvägg osv.) till sina kunder skyddas automatiskt (vitmärkt) av Azure DDoS Protection Standard i den bakre delen.
- Partner har tillgång till Azure DDoS Protection Standard-attackanalys och telemetri som de kan integrera med sina egna produkter och erbjuder en enhetlig kundupplevelse.  
- Partner har tillgång till DDoS snabbresponssupport även i avsaknad av Azure-snabbsvar, för DDoS-relaterade problem.
- Partners skyddade program backas upp av en DDoS SLA-garanti och kostnadsskydd i händelse av DDoS-attacker.

## <a name="technical-integration-overview"></a>Översikt över teknisk integration
Partnermöjligheter för Azure DDoS Protection Standard görs tillgängliga via Azure Portal, API:er och CLI/PS.

### <a name="integrate-with-ddos-protection-standard"></a>Integrera med DDoS Protection Standard
Följande steg krävs för att partner ska konfigurera integrering med Azure DDoS Protection Standard:
1. Skapa en DDoS-skyddsplan i önskad (partner) prenumeration. Stegvisa instruktioner finns i [Skapa en DDoS-standardskyddsplan](manage-ddos-protection.md#create-a-ddos-protection-plan).
   > [!NOTE]
   > Endast 1 DDoS-skyddsplan behöver skapas för en viss klient. 
2. Distribuera en tjänst med offentliga slutpunkter i dina (partner)-prenumerationer, till exempel belastningsutjämnare, brandväggar och brandvägg för webbprogram. 
3. Aktivera Azure DDoS Protection Standard i det virtuella nätverket för tjänsten som har offentliga slutpunkter med DDoS-skyddsplan som skapats i det första steget. För stpe-by-steg-instruktioner finns i [Aktivera DDoS Standard Protection plan](manage-ddos-protection.md#enable-ddos-for-an-existing-virtual-network)
   > [!IMPORTANT] 
   > När Azure DDoS Protection Standard har aktiverats i ett virtuellt nätverk skyddas alla offentliga IP-adresser i det virtuella nätverket automatiskt. Ursprunget till dessa offentliga IPs kan vara antingen inom Azure (klientprenumeration) eller utanför Azure. 
4. Du kan också integrera Azure DDoS Protection Standard telemetri och attackanalys i din programspecifika kundinriktade instrumentpanel. Mer information om hur du använder telemetri finns i [Använda DDoS Protection telemetri](manage-ddos-protection.md#use-ddos-protection-telemetry). Mer information om hur du konfigurerar attackanalys finns i [Konfigurera DDoS-attackanalys](manage-ddos-protection.md#configure-ddos-attack-analytics)

### <a name="onboarding-guides-and-technical-documentation"></a>Introduktionsguider och teknisk dokumentation

- [Produktsida för Azure DDoS-skydd](https://azure.microsoft.com/services/ddos-protection/)
- [Azure DDoS-skyddsdokumentation](ddos-protection-overview.md)
- [Api-referens för Azure DDoS-skydd](https://docs.microsoft.com/rest/api/virtualnetwork/ddosprotectionplans)
- [Azure-referens för virtuellt nätverk](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks)

### <a name="get-help"></a>Få hjälp

- Om du har frågor om program-, tjänst- eller produktintegreringar med Azure DDoS Protection Standard kontaktar du [Azure-säkerhetsgruppen](https://techcommunity.microsoft.com/t5/security-identity/bd-p/Azure-Security).
- Följ diskussioner om [Stack Overflow](https://stackoverflow.com/tags/azure-ddos/).

### <a name="get-to-market"></a>Komma ut på marknaden

- Det primära programmet för samarbete med Microsoft är [Microsoft Partner Network](https://partner.microsoft.com/). – Microsoft Graph Security-integreringar hamnar i [ISV-spåret (MPN Independent Software Vendor).](https://partner.microsoft.com/saas-solution-guide)
- [Microsoft Intelligent Security Association](https://www.microsoft.com/security/business/intelligent-security-association?rtc=1) är det program som specifikt används för Microsoft Security Partners för att berika dina säkerhetsprodukter och förbättra kundernas upptäckbarhet för dina integrationer med Microsoft Security-produkter.

## <a name="next-steps"></a>Nästa steg
Visa befintliga partnerintegreringar:

- [Barracuda WAF-som-en-tjänst](https://www.barracuda.com/waf-as-a-service)
- [Azure Cloud WAF från Radware](https://www.radware.com/resources/microsoft-azure/)
