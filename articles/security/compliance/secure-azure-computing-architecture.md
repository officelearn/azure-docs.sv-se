---
title: Skydda Azures databehandlingsarkitektur
description: Den här referensarkitekturen för en företagsnivå DMZ-arkitektur använder virtuell nätverksutrustning och andra verktyg. Den här arkitekturen har utformats för att uppfylla Försvarsmyndighets skydda Cloud Computing arkitektur funktionskrav. Den ska också användas för alla organisationer. Den här referensen innehåller två alternativ för automatisk som använder Citrix eller F5 installationer.
author: jahender
ms.author: jahender
ms.date: 4/9/2019
ms.topic: article
ms.service: security
ms.openlocfilehash: 017a26d5672f666d4d8eaf629a0f53fe0cfe517f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65963228"
---
# <a name="secure-azure-computing-architecture"></a>Skydda Azures databehandlingsarkitektur

Amerikansk Department of Defense (DoD) kunder som distribuerar arbetsbelastningar till Azure har angett riktlinjer för att ställa in säkra virtuella nätverk och konfigurera säkerhetsverktyg och tjänster som anges av studiematerial och DoD-standarder. 

Defense Information System Agency (DISA) publicerat den [skydda Cloud Computing arkitektur (SCCA) funktionella krav dokumentet (FRD)](https://iasecontent.disa.mil/stigs/pdf/SCCA_FRD_v2-9.pdf) i 2017. SCCA beskriver funktionella målen för att säkra Defense Information System nätverkets (DISN) och kommersiella molnet anslutningspunkter för providern. SCCA beskriver också hur verksamhetskritiska ägare säkra molnprogram på gränsen för anslutningen. Varje DoD-entitet som ansluter till kommersiella molnet måste följa riktlinjerna i SCCA FRD.
 
SCCA har fyra komponenter:
 
- Boundary Cloud Access Point (BCAP)
- Virtuella Datacenter Security-stacken (VDSS)
- Virtuella Datacenter hanterad tjänst (VDM)
- Trusted Cloud Credential Manager (TCCM) 

Microsoft har utvecklat en lösning som uppfyller SCCA för både IL4 och IL5 arbetsbelastningar som körs i Azure. Den här lösningen för Azure-specifika kallas den säkra Azure databehandling arkitektur (SACA). Kunder som distribuerar SACA är kompatibla med SCCA FRD. De kan aktivera DoD-kunder att flytta arbetsbelastningar till Azure när de är anslutna.

SCCA vägledning och arkitekturer som är specifika för DoD kunder, men de senaste ändringarna till SACA hjälp federala kunder som är kompatibla med betrodda vägledning för internet-anslutning (ärende). De senaste ändringarna kan också hjälpa med kommersiella kunder som vill implementera en säker DMZ för att skydda sina Azure-miljöer.


## <a name="secure-cloud-computing-architecture-components"></a>Säker databehandling Molnarkitektur komponenter

### <a name="bcap"></a>BCAP

Syftet med BCAP är att skydda DISN från attacker som har sitt ursprung i molnmiljön. BCAP utför Intrångsdetektion och dataförlustskydd. även filtreras bort obehörig trafik. Den här komponenten kan samordnas med andra komponenter i SCCA. Vi rekommenderar att du distribuerar den här komponenten med hjälp av fysisk maskinvara. BCAP säkerhetskrav visas i följande tabell.

#### <a name="bcap-security-requirements"></a>BCAP säkerhetskrav

![BCAP krav matris](media/bcapreqs.png)


### <a name="vdss"></a>VDSS

Syftet med VDSS är att skydda DoD verksamhetskritiska ägar-program som finns i Azure. VDSS utför stora mängd du säkerhetsåtgärderna i SCCA. Den genomför trafikkontroll för att skydda program som körs i Azure. Den här komponenten kan anges i Azure-miljön.

#### <a name="vdss-security-requirements"></a>VDSS säkerhetskrav

![VDSS krav matris](media/vdssreqs.png)

### <a name="vdms"></a>VDMS

Syftet med VDM är att tillhandahålla värdsäkerheten och delade data center-tjänster. Funktioner i VDM kan antingen köra i hubben för din SCCA eller verksamhetskritiska ägare kan du distribuera delar av det i sina egna specifika Azure-prenumeration. Den här komponenten kan anges i Azure-miljön.

#### <a name="vdms-security-requirements"></a>VDM säkerhetskrav

![VDM krav matris](media/vdmsreqs.png)


### <a name="tccm"></a>TCCM

TCCM är en roll för företag. Administratören är ansvarig för att hantera SCCA. I ansvarsområdet är att: 

- Upprätta planer och principer för åtkomst till molnmiljön. 
- Se till att identitets- och åtkomsthantering fungerar korrekt. 
- Underhålla Plan för autentiseringsuppgifter i molnet. 

Den här personen är utse auktorisering officiella. Den BCAP och VDSS VDM innehåller funktioner som TCCM behöver för att utföra sitt arbete.

#### <a name="tccm-security-requirements"></a>TCCM säkerhetskrav

![TCCM krav matris](media/tccmreqs.png) 

## <a name="saca-components-and-planning-considerations"></a>SACA komponenter och överväganden 

Referensarkitektur SACA är utformad att distribuera komponenter för VDSS och VDM i Azure och aktivera TCCM. Den här arkitekturen är modulära. Alla delar av VDSS och VDM kan finnas i en centraliserad hubb. Vissa kontroller kan uppfyllas i det verksamhetskritiska ägar- eller lokalt. Microsoft rekommenderar att du samplacera VDSS och VDM komponenter till en central virtuella nätverk som alla verksamhetskritiska ägare kan ansluta till. Följande diagram visar den här arkitekturen: 


![Arkitekturdiagram för SACA referens](media/sacav2generic.png)

Tänk i följande avsnitt från början när du planerar din strategi för SCCA restriktioner och teknisk arkitektur, eftersom de påverkar alla kunder. Följande problem har aktiverats med DoD kunder och tenderar att sakta ned planering och körning. 

#### <a name="which-bcap-will-your-organization-use"></a>Vilka BCAP använder din organisation?
   - DISA BCAP:
        - DISA har två operativa BCAPs på femhörning och Camp Roberts, CA. Tredje planeras som snart blir online. 
        - DISA: s BCAPs alla har Azure ExpressRoute-kretsar till Azure, vilket kan användas för anslutning av DoD-kunder. 
        - DISA har en företagsnivå Microsoft peering-session för DoD-kunder som vill prenumerera på Microsoft-programvara som en tjänst (SaaS)-verktyg, till exempel Office 365. Du kan aktivera anslutning och peer-kopplingen till din SACA-instans med hjälp av DISA BCAP. 
    - Skapa din egen BCAP:
        - Det här alternativet måste du låna utrymme i ett samordnade datacenter och ställa in en ExpressRoute-krets till Azure. 
        - Det här alternativet kräver ytterligare godkännande. 
        - På grund av ytterligare godkännande och en fysisk version av tar det här alternativet längst tid. 
    - Vi rekommenderar att du använder DISA BCAP. Det här alternativet tillgängligt är, har inbyggd redundans och måste kunderna som fungerar på det idag i produktion.
- DoD dirigerbara IP-adressutrymme:
    - Du måste använda DoD dirigerbara IP-adressutrymme vid din nätverksgräns. Alternativet att använda NAT för att ansluta dessa blanksteg privata IP-adressutrymme i Azure är tillgängligt.
    - Kontakta DoD Network Information Center (NIC) för att erhålla IP-utrymme. Du behöver den som en del av ditt bidrag för System/Network godkännande Process (SNAPIN) med DISA. 
    - Om du planerar att använda NAT för att ansluta privata adressutrymmet i Azure, behöver du minst ett/24-undernät på adressutrymme som tilldelats från nätverkskortet för varje region där du planerar att distribuera SACA.
- Redundans:
    - Distribuera en SACA-instans till minst två regioner. I molnet DoD distribuera du den till båda tillgängliga DoD-regioner.
    - Ansluta till minst två BCAPs via separata ExpressRoute-kretsar. Båda ExpressRoute-anslutningar kan sedan kopplas till varje region SACA instans. 
- DoD komponent-specifika krav:
    - Har några särskilda krav utanför SCCA kraven i din organisation? Vissa organisationer har särskilda krav för IP-adresser.
- SACA är en modulär arkitektur:
    - Använd endast de komponenter som du behöver för din miljö. 
        - Distribuera virtuella nätverksinstallationer i en enda nivå eller flera nivåer.
        - Använd integrerad IP-adresser eller ta med egna IP-adresser.
- DoD effektnivå för dina program och data:
    - Om det finns risk att program som körs i Microsoft IL5 regioner måste du skapa din SACA-instans i IL5. Instansen kan användas framför IL4 program och IL5. En instans för IL4 SACA framför ett IL5 program som är mest sannolikt inte tar emot ackreditering.

#### <a name="which-network-virtual-appliance-vendor-will-you-use-for-vdss"></a>Vilken leverantör för virtuell installation av nätverket ska du använda för VDSS?
Som tidigare nämnts kan du skapa SACA referensen med hjälp av en mängd olika enheter och Azure-tjänster. Microsoft har en automatiserad lösningsmallar om du vill distribuera arkitekturen SACA med både F5 och Citrix. Dessa lösningar beskrivs i följande avsnitt.

#### <a name="which-azure-services-will-you-use"></a>Vilka Azure-tjänster ska du använda?
- Det finns Azure-tjänster som uppfyller kraven för logganalys, värdbaserad skydds- och ID: N funktioner. Det är möjligt att vissa tjänster inte är allmänt tillgängligt i Microsoft IL5 regioner. I det här fallet kan du behöva använda verktyg från tredje part om dessa Azure-tjänster inte kan uppfyller dina krav. Titta på de verktyg du är bekant med och möjligt att använda interna Azure-verktyg.
- Vi rekommenderar att du använder så många interna Azure-verktyg som möjligt. De är byggda med cloud security i åtanke och integrera sömlöst med resten av Azure-plattformen. Använda Azure inbyggda verktyg i listan nedan för att uppfylla olika krav SCCA:

    - [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview )
    - [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) 
    - [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 
    - [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) 
    - [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
    - [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/overview)
    - [Azure-brandväggen](https://docs.microsoft.com/azure/firewall/overview) 
    - [Azure Front Door](https://docs.microsoft.com/azure/frontdoor/front-door-overview)
    - [Azure-säkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/security-overview)
    - [Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)
    - [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/overview)
- Storleksändring
    - En storlek Övning måste utföras. Titta på antalet samtidiga anslutningar som du kanske via SACA-instans och nätverkskrav för dataflöde. 
    - Det här steget är viktigt. Det hjälper dig att ändra storlek på de virtuella datorerna och identifiera de licenser som krävs från olika leverantörer som du använder i din instans av SACA. 
    - En bra kostnadsanalys kan inte utföras utan den här övningen storlek. Det kan också rätt storlek för bästa prestanda. 


## <a name="most-common-deployment-scenario"></a>De vanligaste distributionsscenario

 Flera Microsoft-kunder har gått igenom fullständig distribution eller vid minst av planeringsfasen SACA miljö. Deras upplevelser framkommer insikt i de vanligaste distributionsscenariot. Följande diagram illustrerar de vanligaste arkitekturen: 


![Arkitekturdiagram för SACA referens](media/sacav2commonscenario.png) 


Som du ser i diagrammet, prenumerera DoD kunder vanligtvis på två av DISA BCAPs. En av dessa ligger på västkusten och andra liv på östkusten. En ExpressRoute privat peer-enhet aktiveras till Azure på varje DISA BCAP plats. Dessa ExpressRoute peer-datorer kopplas sedan till den virtuella nätverksgatewayen i DoD, östra och DoD centrala Azure-regioner. En SACA-instans distribueras i DoD, östra och DoD centrala Azure-regioner. Alla ingående och utgående trafik flödar genom det till och från ExpressRoute-anslutningen till DISA BCAP.

Ägare av verksamhetskritiska program väljer du de Azure-regioner där de planerar att distribuera sina program. De använder virtuell nätverkspeering för att ansluta virtuella nätverk i sitt program till det virtuella nätverket från SACA. Och sedan de Tvingad tunnel sin trafik genom VDSS-instans.

Vi rekommenderar den här arkitekturen eftersom de uppfyller kraven för SCCA. Det är med hög tillgänglighet och lätt att skala. Det förenklar distribution och hantering.

## <a name="automated-saca-deployment-options"></a>Automatiserad SACA-distributionsalternativ

 Som tidigare nämnts Microsoft samarbetar med två leverantörer för att skapa olika SACA infrastruktur mallar. Båda mallarna distribuerar följande Azure-komponenterna: 

- SACA virtuellt nätverk
    - Hanteringsundernätet
        - Det här undernätet är där hantering av virtuella datorer och tjänster har distribuerats, även känt som en jump-rutorna.
        - VDM undernät
            - Det här undernätet är där virtuella datorer och tjänster som används för VDM har distribuerats.
        - Obetrodda och betrodda undernät
            - Dessa undernät är där virtuella installationer distribueras.
        - Gateway-undernät
            - Det här undernätet är där ExpressRoute-gatewayen distribueras.
- Virtuella hanteringsdatorerna jump box
    - De används för out-of-band-hantering av miljön.
- Virtuella nätverksinstallationer
    - Du använder antingen Citrix eller F5 baserat på vilken mall som du distribuerar.
- Offentliga IP-adresser
    - De används för klientdelen tills ExpressRoute är online. Dessa IP-adresser översätts till serverdel privata adressutrymmet i Azure.
- Routningstabeller 
    - Tillämpas under automation, dirigera dessa tabeller tvinga tunneltrafik all trafik via den virtuella installationen.
- Azure-belastningsutjämnare – Standard-SKU
    - De används för att läsa in belastningsutjämna trafik mellan enheter.
- Nätverkssäkerhetsgrupper
    - De används för att styra vilka typer av trafik som kan bläddra till vissa slutpunkter.


### <a name="citrix-saca-deployment"></a>Citrix SACA distribution

En mall för distribution av Citrix distribuerar två skikt med hög tillgänglighet Citrix ADC-installationer. Den här arkitekturen uppfyller kraven för VDSS. 

![Citrix SACA diagram](media/citrixsaca.png)


Citrix-dokumentation och distributionsskriptet finns i [GitHub länken](https://github.com/citrix/netscaler-azure-templates/tree/master/templates/saca).


 ### <a name="f5-saca-deployment"></a>F5 SACA distribution

Två separata F5 mallar för distribution omfattar två olika arkitekturer. Den första mallen har bara ett lager med F5 installationer i en aktiv-aktiv-konfiguration med hög tillgänglighet. Den här arkitekturen uppfyller kraven för VDSS. Den andra mallen lägger till ett andra lager för aktiv-aktiv med hög tillgänglighet F5s. Den här andra lager kan kunderna lägga till egna IP-adresser som är separat från F5 mellan F5-lager. Inte alla DoD-komponenter har specifika IP-adresser som föreskrivs för användning. Om så är fallet, fungerar det enda lagret med F5 installationer för de flesta eftersom den arkitekturen innehåller IP-adresser på F5-enheter.

![F5 SACA diagram](media/f5saca.png)

F5-dokumentation och distributionsskriptet finns i [GitHub länken](https://github.com/f5devcentral/f5-azure-saca).












