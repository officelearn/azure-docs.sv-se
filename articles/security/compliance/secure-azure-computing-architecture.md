---
title: Skydda Azure databehandling arkitektur
description: Det här är en Referensarkitektur för en företagsnivå DMZ-arkitektur med virtuell nätverksutrustning och andra verktyg. Den här arkitekturen har utformats för att uppfylla Försvarsmyndighets skydda Cloud Computing arkitektur funktionskrav. Det kan dock användas för alla företag. Den här referensen innehåller två automatiserade alternativ med Citrix eller F5 installationer.
author: jahender
ms.author: jahender
ms.date: 4/9/2019
ms.topic: article
ms.service: security
ms.openlocfilehash: f2e3d72db3f29dbc6d03b3259acb18daf684fb12
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64917595"
---
# <a name="secure-azure-computing-architecture"></a>Skydda Azure databehandling arkitektur

En snabbt ökande antal DoD-kunder som distribuerar arbetsbelastningar till Azure har efterfrågat råd om hur du konfigurerar säkra virtuella nätverk och konfigurera säkerhetsverktygen och tjänsterna som fastställs av DoD-standarder och praxis. DISA publicerade den [skydda Cloud Computing arkitektur (SCCA) funktionella krav dokumentet](https://iasecontent.disa.mil/stigs/pdf/SCCA_FRD_v2-9.pdf) i 2017. SCCA beskriver funktionella målen för att säkra Defense Information System nätverkets (DISN) och kommersiella molnet provideranslutningen pekar och hur verksamhetskritiska ägare säkra molnprogram på gränsen för anslutningen. Den är obligatorisk att varje DoD-entitet som ansluter till kommersiella molnet följer riktlinjerna i SCCA FRD.
 
Det finns fyra komponenter i SCCA. Åtkomstpunkt för gräns Cloud (BCAP), virtuellt Datacenter Security-stacken (VDSS), Virtual Datacenter-hanterade tjänsterna (VDM) och betrodda Autentiseringshanteraren för molnet (TCCM). Microsoft har utvecklat en lösning som uppfyller kraven för SCCA för både IL4 och IL5 arbetsbelastningar i Azure. Den här Azure-specifik lösning kallas säkra Azure databehandling arkitektur (SACA). Kunder som distribuerar SACA är kompatibla med SCCA FRD och gör att DoD kunderna att flytta arbetsbelastningar till Azure via en anslutning en gång. 

SCCA vägledning och arkitekturer är specifika för DoD kunder, de senaste ändringarna till SACA kommer att hjälpa federala kunder att följa betrodda vägledning för internet-anslutning (RANKNINGSLISTA), samt kommersiella kunder som vill implementera en säker DMZ till skydda sina azure-miljöer.


## <a name="secure-cloud-computing-architecture-components"></a>Säker databehandling Molnarkitektur komponenter

**BCAP**

Syftet med BCAP är att skydda DISN från attacker med ursprung i molnmiljön. BCAP ska utföra intrångsidentifiering och förhindra samt filtrera bort obehörig trafik. Den här komponenten kan samordnas med andra komponenter i SCCA. Vi rekommenderar starkt att den här komponenten distribueras med hjälp av fysisk maskinvara. Nedan hittar du listan över BCAP säkerhetskrav.

***BCAP säkerhetskrav***

![BCAP krav matris](media/bcapreqs.png)


**VDSS**

Syftet med VDSS är att skydda DoD ägare för verksamhetskritiska program som finns i Azure. VDSS utför stora mängd du säkerhetsåtgärderna i SCCA. Det kommer göra trafikkontroll för att skydda de program som körs i Azure. Den här komponenten kan anges i Azure-miljön.

***VDSS säkerhetskrav***

![VDSS krav matris](media/vdssreqs.png)

**VDMS**

Syftet med VDM är att tillhandahålla värdsäkerheten samt delade data center-tjänster. Funktioner i VDM kan antingen köra i hubben för din SCCA eller verksamhetskritiska ägare kan du distribuera delar av det i sina egna specifika Azure-prenumeration. Den här komponenten kan anges i Azure-miljön.

***VDM säkerhetskrav***

![VDM krav matris](media/vdmsreqs.png)


**TCCM**

TCCM är en roll för företag. Den här personen är ansvarig för att hantera SCCA. Ansvarsområdet inkluderar upprätta planer och principer för åtkomst till molnmiljön säkerställer identitet och åtkomsthantering fungerar korrekt, och se till att planera för molnet Credential Management. Den här personen är utse auktorisera officiella. Den BCAP och VDSS VDM ger funktioner som behövs för TCCM att utföra arbetsuppgifter.

***TCCM säkerhetskrav***

![TCCM krav matris](media/tccmreqs.png) 

## <a name="saca-components-and-planning-considerations"></a>SACA komponenter och överväganden 

Referensarkitektur SACA har utformats för att distribuera VDSS och VDM komponenter i azure, samt aktivera TCCM. Den här arkitekturen är modulära, vilket innebär att alla delar av VDSS och VDM kan finnas i en centraliserad hubb eller vissa kontroller kan uppfyllas i verksamhetskritiska ägare utrymme eller lokalt. Rekommendation av våra Microsoft-teamet är att placera VDSS och VDM komponenter till en central virtuella Net alla verksamhetskritiska ägare kan ansluta via. Diagrammet nedan visar vår rekommenderade arkitektur. 


![Arkitekturdiagram för SACA referens](media/sacav2generic.png)

När du planerar din strategi för SCCA restriktioner och teknisk arkitektur, finns det många saker att tänka på. Det är viktigt att följande beaktas från början, eftersom alla kunder behöver att täcka dessa. I avsnitten nedan har problem som har aktiverats med verkliga DoD-kunder och tenderar att långsammare planering och genomförande. 

- Vilka BCAP använder din organisation?
    - DISA BCAP
        - DISA har två operativa BCAPs på femhörning och Camp Roberts Certifikatutfärdare med en tredje online kommer snart. 
        - DISA: s BCAPs alla har ExpressRoute-kretsar till Azure, vilket kan utnyttjas av DoD kunder för anslutning. 
        - DISA har redan en företagsnivå Microsoft-Peering-session för DoD-kunder som vill prenumerera på Microsoft SaaS-verktyg, till exempel Office 365. Du kan aktivera anslutning och peer-kopplingen till din SACA-instans med hjälp av DISA BCAP. 
    - Skapa din egen BCAP
        - Detta kräver att låna ut utrymme i ett samordnade datacenter och konfigurera en ExpressRoute-krets till Azure. 
        - Det här alternativet kräver ytterligare godkännande 
        - På grund av ytterligare godkännande och en fysisk version ut tar det här alternativet längst tid. 
    - Microsofts rekommendation är att använda DISA BCAP. Det här alternativet tillgängligt är, har inbyggd redundans och kunder som körs på det idag i produktion finns redan.
- DoD dirigerbara IP-adressutrymme
    - Du kommer att behöva använda DoD dirigerbara IP-adressutrymme vid din nätverksgräns. Alternativet att NAT de privata IP-adressutrymme i Azure är tillgängligt.  
    - Kontakta DoD NIC för att erhålla IP-adressutrymmet som krävs som en del av ditt SNAPIN-bidrag med DISA. 
    - Om du planerar att NAT till privata adressutrymmet i Azure behöver du minst ett/24-undernät på adressutrymme som tilldelats från nätverkskortet för varje region som du planerar att distribuera SACA. 
- Redundans 
    - Föreslår vi att du distribuerar en SACA-instans till minst två regioner. I DoD-molnet, skulle detta innebära att du distribuerar den till båda tillgängliga DoD-regioner. 
    - Vi rekommenderar också att du ansluter till minst två BCAPs via separata ExpressRoute-kretsar. Båda Express-vägar kan sedan kopplas till varje region SACA instans. 
- DoD komponent-specifika krav
    - Har några särskilda krav utanför SCCA kraven i din organisation? (Vissa organisationer har särskilda krav för IP-adresser)
- SACA är en modulär arkitektur  
    - Använd endast vilka komponenter som du behöver för din miljö. 
        - Distribuera nva: er i en enda nivå eller flera nivåer
        - Integrerad IP-adresser eller ta med dina egna IP-adresser
- DoD effektnivå för dina program och data
    - Om det finns risk att program som körs i alla våra regioner IL5, rekommenderas att du skapar din SACA-instans i IL5. Instansen kan användas framför IL4 tillämpningar, liksom IL5. Men en IL4 SACA instans framför ett IL5 program är förmodligen får inte ackreditering. 
- Vilken virtuell nätverksinstallation leverantör används för VDSS?
    - Såsom nämnts tidigare kan SACA referensen byggas med olika enheter och Azure-tjänster. Vi har dock automatiserad lösningsmallar som ska distribueras SACA arkitektur med både F5 och Citrix. Dessa lösningar beskrivs i detalj nedan. 
- Vilka Azure-tjänster ska du använda?
    - Det finns Azure-tjänster som uppfyller krav avseende logganalys-, värdbaserad skydds- och ID: N funktioner. Det är dock möjligt att vissa tjänster inte är allmänt tillgänglig i alla våra IL5 regioner. Detta kan leda till behovet av att använda vissa verktyg med 3 part om Azure-tjänsterna inte uppfyller dina behov. Du behöver att titta närmare på vilka verktyg du är bekant med och möjligt att använda interna Azure-verktyg. 
    - Det är Microsofts rekommendation är att du använder så många Azure inbyggda verktyg som möjligt när de skapas med molnsäkerhet i åtanke och integrera sömlöst med resten av Azure-plattformen. Nedan visas en lista över Azure inbyggda verktyg som kan användas för att uppfylla olika krav för SCCA. 
        - [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview )
        - [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) 
        - [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 
        - [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) 
        - [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
        - [Application Gateway](https://docs.microsoft.com/azure/application-gateway/overview)
        - [Azure-brandväggen](https://docs.microsoft.com/azure/firewall/overview) 
        - [Azure Front Door](https://docs.microsoft.com/azure/frontdoor/front-door-overview)
        - [Säkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/security-overview)
        - [Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)
        - [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/overview)
- Storlekar
    - En storlek Övning måste slutföras. Du behöver titta på antalet samtidiga anslutningar som du kan ha via SACA instans samt nätverkskraven för dataflöde. 
    - Detta är ett viktigt steg eftersom det kommer att ändra storlek på de virtuella datorerna, samt hjälper dig för att identifiera de licenser som krävs från olika leverantörer som du kommer att använda i din SACA-instans. 
    - Det går inte att göra en bra kostnadsanalys utan övningen storlek och det är också viktigt att se till att allt är har rätt storlek för att tillåta för bästa prestanda. 


## <a name="most-common-deployment-scenario"></a>De vanligaste distributionsscenario

Microsoft har flera kunder som redan har gått igenom hela distributionen eller minst planerar faser i sina miljöer SACA. Detta kan vi och få inblick i de vanligaste distributionsscenariot. Diagrammet nedan illustrerar de vanligaste arkitekturen. 


![Arkitekturdiagram för SACA referens](media/sacav2commonscenario.png) 


Som du ser i diagrammet, prenumerera DoD kunder vanligtvis på två av BCAPs DISA, en av dessa liv på västkusten och andra liv på östkusten. En ExpressRoute privat peer-enhet aktiveras till Azure på varje DISA BCAP plats. Dessa ExpressRoute peer-datorer länkas sedan till den virtuella Nätverksgatewayen i DoD, östra och DoD centrala Azure-regioner. En SACA-instans distribueras i DoD, östra och DoD centrala Azure-region och alla ingående och utgående trafik flödar genom den till och från Express Route-anslutningen till DISA BCAP. 

Är virtuellt nätverk till det virtuella nätverket SACA uppdragskritiska ägare program väljer sedan vilken Azure-region som de ska distribuera program i och använda Peering för virtuellt nätverk för att ansluta sina program. Tvingad tunneltrafik sin trafik genom VDSS-instans. 

Den här arkitekturen rekommenderas av Microsoft, som den uppfyller SCCA krav, det är mycket tillgänglig och skalbar och det förenklar distribution och hantering.

## <a name="automated-saca-deployment-options"></a>Automatiserad SACA-distributionsalternativ

 Vi nämnde tidigare att Microsoft har gått samman med två leverantörer för att skapa en automatisk SACA mall. Båda mallarna distribuerar följande Azure-komponenterna. 

- SACA virtuellt nätverk
    - Hanteringsundernätet
        - Där hantering av virtuella datorer och tjänster distribueras (hopprutorna)
        - VDM undernät
            - Där virtuella datorer och tjänster som används för VDM har distribuerats
        - Obetrodda och betrodda undernät 
            - Där virtuella installationer distribueras
        - Gateway-undernät
            - Där ExpressRoute-gatewayen ska distribueras
- Virtuella Hanteringsdatorerna Jump Box
    - Används för Out of band-hantering av miljön.
- Virtuella nätverksinstallationer
    - Citrix eller F5, beroende på vilken mall som du distribuerar.
- Offentliga IP-adresser
    - Används för klientdelen tills ExpressRoute är online. Dessa IP-adresser översätter till privata adressutrymmet i Azure-serverdelen
- Routningstabeller 
    - Tillämpas under automation, dessa vägen tabeller tvinga tunneltrafik all trafik via den virtuella installationen
- Azure-belastningsutjämnare – Standard-SKU
    - Dessa används för att belastningsutjämna trafik mellan enheter
- Nätverkssäkerhetsgrupper
    - Används för att styra vilka typer av trafik som kan bläddra till vissa slutpunkter


**Citrix SACA distribution**

Citrix har skapat en Distributionsmall som distribuerar två skikt med hög tillgänglighet Citrix ADC-installationer. Den här arkitekturen uppfyller kraven för VDSS. 

![Citrix SACA Diagram](media/citrixsaca.png)


Citrix-dokumentation och distribution skript finns [här.](https://github.com/citrix/netscaler-azure-templates/tree/master/templates/saca)


 **F5 SACA distribution**

F5 har skapat två separata distributionsmallar som omfattar två olika arkitekturer. Den första som har ett lager av F5 installationer i en aktiv-aktiv-konfiguration med hög tillgänglighet. Den här arkitekturen uppfyller kraven för VDSS. Andra lägger till ett andra lager för aktiv-aktiv med hög tillgänglighet F5s. Syftet med den här andra lager är så att kunderna lägga till egna IP-adresser som är separat från F5 mellan F5-lager. Inte alla DoD-komponenter har specifika IP-adresser som föreskrivs för användning. Om så är fallet, fungerar det enda lagret med F5 installationer för mest sedan att arkitektur innehåller IP-adresser på F5-enheter.  

![Citrix SACA Diagram](media/f5saca.png)

F5-dokumentation och distribution skript finns [här.](https://github.com/f5devcentral/f5-azure-saca) 












