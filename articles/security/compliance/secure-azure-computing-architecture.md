---
title: Skydda Azures databehandlingsarkitektur
description: Den här referens arkitekturen för en DMZ-arkitektur på företags nivå använder virtuella nätverks apparater och andra verktyg. Den här arkitekturen har utformats för att möta försvars departementets säkra funktions krav för molnbaserad data behandling i molnet. Den kan också användas för alla organisationer. Den här referensen innehåller två automatiserade alternativ som använder Citrix eller F5-enheter.
author: jahender
ms.author: jahender
ms.date: 4/9/2019
ms.topic: article
ms.service: security
ms.openlocfilehash: a12f5643c96b855d07bd038fcc96100a87f1252d
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001866"
---
# <a name="secure-azure-computing-architecture"></a>Skydda Azures databehandlingsarkitektur

Amerikansk DoD-kunder (Department of försvar) som distribuerar arbets belastningar till Azure har bett om rikt linjer för att konfigurera säkra virtuella nätverk och konfigurera de säkerhets verktyg och tjänster som definieras av DoD-standarder och-praxis. 

Försvars information system Agency (DISA) publicerade [SCCA-dokumentet (Secure Cloud Computing Architecture) (FRD)](https://dl.dod.cyber.mil/wp-content/uploads/cloud/pdf/SCCA_FRD_v2-9.pdf) i 2017. SCCA beskriver de funktionella målen för att skydda försvars information system nätverkets (DISN) och anslutnings punkter för kommersiella moln leverantörer. SCCA beskriver också hur uppdrags ägare skyddar moln program på anslutnings gränser. Varje DoD-entitet som ansluter till det kommersiella molnet måste följa rikt linjerna som anges i SCCA-FRD.
 
SCCA har fyra komponenter:
 
- Åtkomst punkt för begränsande moln (BCAP)
- Säkerhets stack för virtuella Data Center (VDSS)
- Hanterings tjänst för virtuella Data Center (VDMS)
- Trusted Cloud Credential Manager (TCCM) 

Microsoft har utvecklat en lösning som uppfyller SCCA-kraven för både IL4-och IL5-arbetsbelastningar som körs i Azure. Den här Azure-/regionsspecifika lösningen kallas för SACA (Secure Azure Computing Architecture). Kunder som distribuerar SACA är kompatibla med SCCA-FRD. De kan göra det möjligt för DoD-kunder att flytta arbets belastningar till Azure när de är anslutna.

SCCA-vägledning och arkitekturer är speciella för DoD-kunder, men de senaste revisionerna till SACA hjälper civila kunder att följa vägledningen för Trusted Internet Connection (luffarschack). De senaste revideringarna hjälper även kommersiella kunder som vill implementera en säker DMZ för att skydda sina Azure-miljöer.


## <a name="secure-cloud-computing-architecture-components"></a>Skydda arkitektur komponenter för molnbaserad data behandling

### <a name="bcap"></a>BCAP

Syftet med BCAP är att skydda DISN från attacker som har sitt ursprung i moln miljön. BCAP utför intrångs identifiering och skydd. den filtrerar också bort obehörig trafik. Den här komponenten kan samplaceras med andra komponenter i SCCA. Vi rekommenderar att du distribuerar den här komponenten med hjälp av fysisk maskin vara. BCAP säkerhets krav anges i följande tabell.

#### <a name="bcap-security-requirements"></a>BCAP säkerhets krav

![BCAP krav mat ris](media/bcapreqs.png)


### <a name="vdss"></a>VDSS

Syftet med VDSS är att skydda DoD uppdrags ägar program som finns i Azure. VDSS utför Mass säkerhets åtgärder i SCCA. Den genomför trafik inspektionen för att skydda de program som körs i Azure. Den här komponenten kan anges i din Azure-miljö.

#### <a name="vdss-security-requirements"></a>VDSS säkerhets krav

![VDSS krav mat ris](media/vdssreqs.png)

### <a name="vdms"></a>VDMS

Syftet med VDMS är att tillhandahålla värd säkerhet och delade data center tjänster. Funktionerna i VDMS kan antingen köras i hubben för din SCCA eller uppdrags ägaren kan distribuera delar av den i sin egen specifika Azure-prenumeration. Den här komponenten kan anges i din Azure-miljö.

#### <a name="vdms-security-requirements"></a>VDMS säkerhets krav

![VDMS krav mat ris](media/vdmsreqs.png)


### <a name="tccm"></a>TCCM

TCCM är en affärs roll. Den här personen ansvarar för att hantera SCCA. Deras uppgifter är att: 

- Upprätta planer och principer för konto åtkomst till moln miljön. 
- Se till att identitets-och åtkomst hantering fungerar korrekt. 
- Underhåll planen för hantering av moln referenser. 

Den här personen utses av den officiella auktorisationen. BCAP, VDSS och VDMS tillhandahåller de funktioner som TCCM behöver för att utföra sitt jobb.

#### <a name="tccm-security-requirements"></a>TCCM säkerhets krav

![TCCM krav mat ris](media/tccmreqs.png) 

## <a name="saca-components-and-planning-considerations"></a>SACA-komponenter och överväganden vid planering 

SACA-referens arkitekturen är utformad för att distribuera VDSS-och VDMS-komponenterna i Azure och för att aktivera TCCM. Den här arkitekturen är modulär. Alla delar av VDSS och VDMS kan leva i ett centraliserat nav. Vissa av kontrollerna kan uppfyllas i uppdrags ägar utrymmet eller till och med lokalt. Microsoft rekommenderar att du samplacerar VDSS-och VDMS-komponenterna i ett centralt virtuellt nätverk som alla uppdrags ägare kan ansluta till. Följande diagram visar den här arkitekturen: 


![SACA referens arkitektur diagram](media/sacav2generic.png)

När du planerar din SCCA kompatibilitet-strategi och teknisk arkitektur bör du tänka på följande avsnitt från början eftersom de påverkar varje kund. Följande problem har inträffat i DoD-kunder och tenderar att minska planeringen och körningen. 

#### <a name="which-bcap-will-your-organization-use"></a>Vilken BCAP kommer din organisation använda?
   - DISA BCAP:
        - DISA har två operativa BCAPs på femhörning och på Camp Roberts, CA. En tredje plan planeras att bli online snart. 
        - DISA-BCAPs har alla Azure ExpressRoute-kretsar till Azure, som kan användas av DoD-kunder för anslutning. 
        - DISA har en Microsoft-peering-session på företags nivå för DoD-kunder som vill prenumerera på Microsoft-program som tjänst (SaaS) verktyg, till exempel Office 365. Genom att använda DISA-BCAP kan du aktivera anslutning och peering till SACA-instansen. 
    - Bygg dina egna BCAP:
        - Det här alternativet kräver att du lånar utrymme i ett Samplacerat Data Center och konfigurerar en ExpressRoute-krets till Azure. 
        - Det här alternativet kräver ytterligare godkännande. 
        - På grund av det ytterligare godkännandet och en fysisk utbyggnad tar det här alternativet den senaste gången. 
    - Vi rekommenderar att du använder DISA-BCAP. Det här alternativet är enkelt att komma åt, har inbyggd redundans och har kunder som arbetar på den idag i produktionen.
- DoD dirigerbart IP-utrymme:
    - Du måste använda DoD-dirigerbart IP-utrymme på din gräns. Alternativet att använda NAT för att ansluta dessa utrymmen till det privata IP-utrymmet i Azure är tillgängligt.
    - Kontakta DoD Network Information Center (NIC) för att få IP-utrymme. Du behöver det som en del av ditt system/nätverks godkännande process (SNAP) med DISA. 
    - Om du planerar att använda NAT för att ansluta privat adress utrymme i Azure, behöver du minst ett/24-undernät för adress utrymmet som tilldelas från NÄTVERKSKORTet för varje region där du planerar att distribuera SACA.
- Redundans:
    - Distribuera en SACA-instans till minst två regioner. I DoD-molnet distribuerar du det till båda tillgängliga DoD-regionerna.
    - Anslut till minst två BCAPs via separata ExpressRoute-kretsar. Båda ExpressRoute-anslutningarna kan sedan länkas till varje regions SACA-instans. 
- DoD-särskilda krav för komponenten:
    - Har din organisation några särskilda krav utanför SCCA-kraven? Vissa organisationer har särskilda krav på IP-adresser.
- SACA är en modulär arkitektur:
    - Använd bara de komponenter som du behöver för din miljö. 
        - Distribuera virtuella nätverks enheter på en enda nivå eller flera nivåer.
        - Använd integrerade IP-adresser eller ta egna IP-adresser.
- DoD effekt nivå för dina program och data:
    - Om det finns någon möjlighet att köra program i Microsoft IL5-regioner skapar du SACA-instansen i IL5. Instansen kan användas framför IL4-program och IL5. En IL4 SACA-instans framför ett IL5-program kommer troligen inte att få någon ackreditering.

#### <a name="which-network-virtual-appliance-vendor-will-you-use-for-vdss"></a>Vilken nätverks leverantör av virtuella enheter kommer du att använda för VDSS?
Som tidigare nämnts kan du bygga den här SACA-referensen med hjälp av en mängd olika apparater och Azure-tjänster. Microsoft har automatiserade lösnings mallar för att distribuera SACA-arkitekturen med både F5 och Citrix. Dessa lösningar beskrivs i följande avsnitt.

#### <a name="which-azure-services-will-you-use"></a>Vilka Azure-tjänster kommer du att använda?
- Det finns Azure-tjänster som kan uppfylla kraven för Log Analytics, värdbaserade skydd och ID-funktioner. Det är möjligt att vissa tjänster inte är allmänt tillgängliga i Microsoft IL5-regioner. I så fall kan du behöva använda verktyg från tredje part om dessa Azure-tjänster inte uppfyller dina krav. Titta på de verktyg du känner till och möjligheten att använda Azures inbyggda verktyg.
- Vi rekommenderar att du använder så många inbyggda Azure-verktyg som möjligt. De har skapats med moln säkerhet i åtanke och integreras sömlöst med resten av Azure-plattformen. Använd de inbyggda Azure-verktygen i följande lista för att uppfylla olika krav för SCCA:

    - [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview )
    - [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) 
    - [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 
    - [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) 
    - [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
    - [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/overview)
    - [Azure Firewall](https://docs.microsoft.com/azure/firewall/overview) 
    - [Azure Front Door](https://docs.microsoft.com/azure/frontdoor/front-door-overview)
    - [Azure-säkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/security-overview)
    - [Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)
    - [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/overview)
- Storleksändring
    - En storleks övning måste utföras. Titta på antalet samtidiga anslutningar som du kan ha via SACA-instansen och kraven på nätverks data flöde. 
    - Det här steget är kritiskt. Den hjälper till att ändra storlek på de virtuella datorerna och identifiera de licenser som krävs från de olika leverantörer som du använder i din SACA-instans. 
    - Det går inte att utföra en klar kostnads analys utan denna storleks övning. Korrekt storlek ger också bästa möjliga prestanda. 


## <a name="most-common-deployment-scenario"></a>Vanligaste distributions scenariot

 Många Microsoft-kunder har genomgått en fullständig distribution eller minst planerings faserna i sina SACA-miljöer. Deras upplevelser visar inblick i det vanligaste distributions scenariot. Följande diagram visar den vanligaste arkitekturen: 


![SACA referens arkitektur diagram](media/sacav2commonscenario.png) 


Som du kan se i diagrammet prenumererar DoD kunder vanligt vis på två av DISA-BCAPs. En av dessa liv på västkusten och den andra bor på östra kusten. En privat ExpressRoute-peer är aktive rad för Azure på varje DISA BCAP-plats. Dessa ExpressRoute-peer-datorer länkas sedan till den virtuella Nätverksgatewayen i DoD öst-och DoD-centrala Azure-regioner. En SACA-instans distribueras i DoD öst-och DoD-centrala Azure-regioner. All ingående och utgående trafik flödar genom den till och från ExpressRoute-anslutningen till DISA-BCAP.

Program för uppdrags ägare väljer sedan de Azure-regioner där de planerar att distribuera sina program. De använder peering för virtuella nätverk för att ansluta sina programs virtuella nätverk till det virtuella SACA-nätverket. Därefter tvingar de tunnel trafik genom VDSS-instansen.

Vi rekommenderar den här arkitekturen eftersom den uppfyller SCCA-krav. Den är hög tillgänglig och lätt att skala. Det fören klar också distribution och hantering.

## <a name="automated-saca-deployment-options"></a>Automatiserade SACA distributions alternativ

 Som tidigare nämnts har Microsoft samarbetat med två leverantörer för att skapa en automatiserad infrastruktur mal len SACA. Båda mallarna distribuerar följande Azure-komponenter: 

- SACA virtuellt nätverk
    - Hanterings under nät
        - Det här under nätet är den plats där hantering av virtuella datorer och tjänster distribueras, även kallat hopp rutor.
        - VDMS-undernät
            - Det här under nätet är där virtuella datorer och tjänster som används för VDMS distribueras.
        - Ej betrodda och betrodda undernät
            - Dessa undernät är där virtuella enheter distribueras.
        - Gatewayundernät
            - Det här under nätet är där ExpressRoute-gatewayen distribueras.
- Hanterings rutan för virtuella datorer i hanterings hopp
    - De används för out-of-band-hantering av miljön.
- Virtuella nätverksinstallationer
    - Du använder antingen Citrix eller F5 baserat på vilken mall du distribuerar.
- Offentliga IP-adresser
    - De används för klient delen tills ExpressRoute är online. De här IP-adresserna översätts till det privata Azure-adressutrymmet.
- Routningstabeller 
    - Dessa Route-tabeller tillämpas under automatiseringen och tvingar all trafik via den virtuella installationen.
- Azure load BALANCERS – standard-SKU
    - De används för att belastningsutjämna trafik mellan de olika enheterna.
- Nätverkssäkerhetsgrupper
    - De används för att styra vilka typer av trafik som kan passera till vissa slut punkter.


### <a name="citrix-saca-deployment"></a>Citrix SACA-distribution

En mall för att distribuera Citrix distribuerar två lager med ADC-enheter med hög tillgänglighet. Den här arkitekturen uppfyller kraven i VDSS. 

![Citrix SACA-diagram](media/citrixsaca.png)


För Citrix-dokumentationen och distributions skriptet, se [den här github-länken](https://github.com/citrix/netscaler-azure-templates/tree/master/templates/saca).


 ### <a name="f5-saca-deployment"></a>F5 SACA-distribution

Två separata F5-mallar behandlar två olika arkitekturer. Den första mallen har bara ett lager med F5-enheter i en aktiv-aktiv konfiguration med hög tillgänglighet. Den här arkitekturen uppfyller kraven för VDSS. Den andra mallen lägger till ett andra lager av aktiva, hög tillgängliga F5s. Det här andra lagret låter kunder lägga till sina egna IP-adresser åtskilda från F5 mellan F5-lagren. Alla DoD-komponenter har inte särskilda IP-adresser som krävs för användning. Om så är fallet fungerar det enda lagret med F5-enheter för de flesta eftersom den arkitekturen innehåller IP-adresser på F5-enheterna.

![F5 SACA-diagram](media/f5saca.png)

För F5-dokumentationen och distributions skriptet, se [den här github-länken](https://github.com/f5devcentral/f5-azure-saca).












