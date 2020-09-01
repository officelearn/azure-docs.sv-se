---
title: Arkiv av nyheter i Azure Security Center
description: En beskrivning av vad som är nytt och ändrat i Azure Security Center från sex månader sedan och tidigare.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2020
ms.author: memildin
ms.openlocfilehash: d7ac2777ffd500082bd0b8a9700d53bffd9d6931
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2020
ms.locfileid: "89177320"
---
# <a name="archive-for-whats-new-in-azure-security-center"></a>Vill du arkivera vad som är nytt i Azure Security Center?

Den primära sidan [Nyheter i Azure Security Center?](release-notes.md) viktig information innehåller uppdateringar de senaste sex månaderna, medan den här sidan innehåller äldre objekt.

På den här sidan får du information om:

- Nya funktioner
- Felkorrigeringar
- Föråldrade funktioner


## <a name="february-2020"></a>Februari 2020

### <a name="fileless-attack-detection-for-linux-preview"></a>Filbaserad attack identifiering för Linux (för hands version)

När angripare ökar angreppet med stealthier-metoder för att undvika identifiering kan Azure Security Center utöka identifieringen av fil lös attacker för Linux, förutom Windows. Filbaserade attacker utnyttjar sårbarheter i program vara, injicera skadliga nytto laster i ofarliga system processer och Dölj i minnet. Dessa tekniker:

- minimera eller eliminera spår av skadlig kod på disk
- minska risken för identifiering med diskbaserade genomsöknings lösningar med skadlig kod

För att räkna ut det här hotet Azure Security Center lanserad fil lös attack identifiering för Windows i oktober 2018 och har nu utökad avkänning av fil lös attacker på Linux också. 



## <a name="january-2020"></a>Januari 2020

### <a name="enhanced-secure-score-preview"></a>Förbättrade säkra poäng (för hands version)

Nu finns en förbättrad version av den säkra poängen-funktionen i Azure Security Center tillgänglig i för hands versionen. I den här versionen är flera rekommendationer grupperade i säkerhets kontroller som bättre återspeglar dina sårbara attack ytor (till exempel begränsa åtkomst till hanterings portar).

Bekanta dig med de säkra Poäng ändringarna under förhands gransknings fasen och bestäm andra åtgärder som kan hjälpa dig att skydda din miljö ytterligare.

Läs mer om [förbättrade säkra poäng (för hands version)](secure-score-security-controls.md).



## <a name="november-2019"></a>November 2019

Uppdateringarna i november omfattar:
 - [Skydd mot hot för Azure Key Vault i Nordamerika regioner (för hands version)](#threat-protection-for-azure-key-vault-in-north-america-regions-preview)
 - [Skydd mot hot skydd för Azure Storage innehåller kontroll av ryktes rykte](#threat-protection-for-azure-storage-includes-malware-reputation-screening)
 - [Arbets flödes automatisering med Logic Apps (för hands version)](#workflow-automation-with-logic-apps-preview)
 - [Snabb korrigering för Mass resurser som är allmänt tillgängliga](#quick-fix-for-bulk-resources-generally-available)
 - [Skanna behållar avbildningar för sårbarheter (för hands version)](#scan-container-images-for-vulnerabilities-preview)
 - [Ytterligare normer för regelefterlevnad (för hands version)](#additional-regulatory-compliance-standards-preview)
 - [Skydd mot hot för Azure Kubernetes service (för hands version)](#threat-protection-for-azure-kubernetes-service-preview)
 - [Sårbarhets bedömning av virtuell dator (för hands version)](#virtual-machine-vulnerability-assessment-preview)
 - [Avancerad data säkerhet för SQL-servrar på Azure Virtual Machines (för hands version)](#advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview)
 - [Stöd för anpassade principer (förhands granskning)](#support-for-custom-policies-preview)
 - [Utöka Azure Security Center täckning med plattform för community och partner](#extending-azure-security-center-coverage-with-platform-for-community-and-partners)
 - [Avancerad integrering med export av rekommendationer och aviseringar (för hands version)](#advanced-integrations-with-export-of-recommendations-and-alerts-preview)
 - [Publicera på lokal-servrar för att Security Center från Windows administrations Center (för hands version)](#onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview)

### <a name="threat-protection-for-azure-key-vault-in-north-america-regions-preview"></a>Skydd mot hot för Azure Key Vault i Nordamerika regioner (för hands version)

Azure Key Vault är en viktig tjänst för att skydda data och förbättra prestanda för moln program genom att erbjuda möjligheten att centralt hantera nycklar, hemligheter, kryptografiska nycklar och principer i molnet. Eftersom Azure Key Vault lagrar känsliga och affärs kritiska data, krävs maximal säkerhet för nyckel valven och de data som lagras i dem.

Azure Security Center s support för skydd mot Azure Key Vault ger ytterligare ett lager med säkerhets information som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja nyckel valv. Det nya skydds lagret gör det möjligt för kunder att lösa sina nyckel valv utan att vara säkerhets expert eller hantera säkerhets övervaknings system. Funktionen finns i en allmänt tillgänglig för hands version i Nordamerika regioner.


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>Skydd mot hot skydd för Azure Storage innehåller kontroll av ryktes rykte

Skydd mot hot för Azure Storage erbjuder nya identifieringar som drivs av Microsoft Threat intelligence för att identifiera överbelastningar mot skadlig kod för att Azure Storage använda hash-ryktes analys och misstänkt åtkomst från en aktiv Tor-nod (en maskera proxy). Nu kan du Visa identifierad skadlig kod över lagrings konton med Azure Security Center.


### <a name="workflow-automation-with-logic-apps-preview"></a>Arbets flödes automatisering med Logic Apps (för hands version)

Organisationer med centralt hanterad säkerhet och IT/åtgärder implementerar interna arbets flödes processer för att köra nödvändig åtgärd i organisationen när avvikelser upptäcks i sina miljöer. I många fall är dessa arbets flöden upprepade processer och automatisering kan effektivisera processer i organisationen avsevärt.

Idag introducerar vi en ny funktion i Security Center som gör det möjligt för kunder att skapa automatiserings konfigurationer som använder Azure Logic Apps och för att skapa principer som automatiskt aktiverar dem baserat på vissa ASC-resultat som rekommendationer eller aviseringar. Azure Logic app kan konfigureras för att utföra en anpassad åtgärd som stöds av den stora communityn med Logic app-kopplingar, eller använda en av mallarna som tillhandahålls av Security Center, till exempel att skicka ett e-postmeddelande eller öppna en ServiceNow™-biljett.

Mer information om automatiska och manuella Security Center-funktioner för att köra arbets flöden finns i [arbets flödes automatisering](workflow-automation.md).

Information om hur du skapar Logic Apps finns i [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).


### <a name="quick-fix-for-bulk-resources-generally-available"></a>Snabb korrigering för Mass resurser som är allmänt tillgängliga

Med de många uppgifter som en användare har fått som en del av säkra Poäng kan möjligheten att effektivt åtgärda problem i en stor flottan bli en utmaning.

För att förenkla reparationen av felkonfigurationer av säkerhet och för att snabbt kunna åtgärda rekommendationer för en mängd resurser och förbättra dina säkra poäng, använder du snabb korrigerings reparation.

Med den här åtgärden kan du välja de resurser som du vill tillämpa reparationen på och starta en reparations åtgärd som konfigurerar inställningen för din räkning.

Snabb korrigering är allmänt tillgänglig idag-kunder som en del av sidan med Security Center rekommendationer.

Se vilka rekommendationer som har snabb korrigering aktive rad i [referens guiden för säkerhets rekommendationer](recommendations-reference.md).


### <a name="scan-container-images-for-vulnerabilities-preview"></a>Skanna behållar avbildningar för sårbarheter (för hands version)

Azure Security Center kan nu skanna behållar avbildningar i Azure Container Registry för sårbarheter.

Avbildnings genomsökningen fungerar genom att parsa behållar avbildnings filen och sedan kontrol lera om det finns några kända sårbarheter (drivs av Qualys).

Själva sökningen utlöses automatiskt när nya behållar avbildningar skickas till Azure Container Registry. Identifierade sårbarheter som Security Center rekommendationer och ingår i Azures säkra poäng tillsammans med information om hur de kan korrigeras för att minska angrepps ytan.


### <a name="additional-regulatory-compliance-standards-preview"></a>Ytterligare normer för regelefterlevnad (för hands version)

Instrument panelen för övervakning av efterlevnad ger insikter om position som baseras på Security Center bedömningar. Instrument panelen visar hur din miljö uppfyller de kontroller och krav som anges av särskilda regler och bransch riktmärken och ger rekommendationer för hur du kan åtgärda dessa krav.

Instrument panelen för kontroll av efterlevnad har därför stöd för fyra inbyggda standarder: Azure CIS-1.1.0, PCI-DSS, ISO 27001 och SOC-TSP. Vi presenterar nu den offentliga för hands versionen av ytterligare standarder som stöds: NIST SP 800-53 R4, SWIFT CSP CSCF v2020, Kanadas federala PBMM och Storbritannien officiellt tillsammans med UK NHS. Vi publicerar också en uppdaterad version av Azure CIS-1.1.0, som täcker fler kontroller från standard och utökad utökning.

[Läs mer om hur du anpassar uppsättningen med standarder på instrument panelen för](update-regulatory-compliance-packages.md)regelefterlevnad.


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>Skydd mot hot för Azure Kubernetes service (för hands version)

Kubernetes blir snabbt den nya standarden för att distribuera och hantera program vara i molnet. Några få personer har omfattande erfarenhet av Kubernetes och många fokuserar på allmän teknik och administration och förbise säkerhets aspekten. Kubernetes-miljön måste konfigureras försiktigt för att vara säker, och se till att ingen behållare som fokuserar på angrepps ytan inte är öppen är öppen för angripare. Security Center utökar sitt stöd i container utrymmet till en av de snabbast växande tjänsterna i Azure – Azure Kubernetes service (AKS).

De nya funktionerna i den här offentliga för hands versionen omfattar:

- **Identifiering & synlighet** -kontinuerlig identifiering av hanterade AKS-instanser i Security Center registrerade prenumerationer.
- **Säkra Poäng rekommendationer** – åtgärds bara objekt som hjälper kunder att följa rekommenderade säkerhets metoder i AKS som en del av kundens säkra poäng, till exempel "rollbaserad Access Control ska användas för att begränsa åtkomsten till ett Kubernetes service-kluster".
- **Hot identifiering** – värd-och klusterbaserade analyser, till exempel "en privilegie rad behållare identifierad".


### <a name="virtual-machine-vulnerability-assessment-preview"></a>Sårbarhets bedömning av virtuell dator (för hands version)

Program som installeras på virtuella datorer kan ofta ha sårbarheter som kan leda till en överträdelse av den virtuella datorn. Vi presenterar att Security Center standard nivån innehåller inbyggd sårbarhets bedömning för virtuella datorer utan extra avgift. Med en sårbarhets bedömning, som drivs av Qualys i den offentliga för hands versionen, kan du kontinuerligt genomsöka alla installerade program på en virtuell dator för att hitta sårbara program och visa resultaten i Security Center portalens upplevelse. Security Center tar hand om alla distributions åtgärder så att inget extra arbete krävs från användaren. Vi planerar att tillhandahålla alternativ för sårbarhets bedömning för att stödja kundernas unika affärs behov.

[Lär dig mer om sårbarhets bedömningar för Azure-Virtual Machines](deploy-vulnerability-assessment-vm.md).


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Avancerad data säkerhet för SQL-servrar på Azure Virtual Machines (för hands version)

Azure Security Center support för skydd mot hot och sårbarheter för SQL-databaser som körs på virtuella IaaS-datorer finns nu som för hands version.

[Sårbarhetsbedömning](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) är en tjänst som är enkel att konfigurera och som kan identifiera, spåra och hjälpa dig att åtgärda eventuella säkerhetsrisker i databasen. Den ger insyn i din säkerhets position som en del av Azures säkra poäng och innehåller stegen för att lösa säkerhets problem och förbättra din databas Fortifications.

[Avancerat skydd](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja din SQL Server. Den övervakar kontinuerligt databasen för misstänkta aktiviteter och tillhandahåller åtgärds säkerhets aviseringar om avvikande databas åtkomst mönster. De här aviseringarna ger information om misstänkta aktiviteter och rekommenderade åtgärder för att undersöka och minimera hotet.


### <a name="support-for-custom-policies-preview"></a>Stöd för anpassade principer (förhands granskning)

Azure Security Center stöder nu anpassade principer (i för hands versionen).

Våra kunder har kunnat utöka sin nuvarande skydds utvärderings omfattning i Security Center med sina egna säkerhets utvärderingar baserade på principer som de skapar i Azure Policy. Det är nu möjligt med stöd för anpassade principer.

Dessa nya principer kommer att ingå i Security Center rekommendationer, säkra poäng och instrument panelen för regler för efterlevnad. Med stöd för anpassade principer kan du nu skapa ett anpassat initiativ i Azure Policy och sedan lägga till det som en princip i Security Center och visualisera det som en rekommendation.


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>Utöka Azure Security Center täckning med plattform för community och partner

Använd Security Center för att få rekommendationer som inte bara kommer från Microsoft utan även från befintliga lösningar från partners som Check Point, Tenable och CyberArk med många fler integreringar.  Security Center det enkla onboarding-flödet kan ansluta dina befintliga lösningar till Security Center, så att du kan visa dina position-rekommendationer på en enda plats, köra enhetliga rapporter och utnyttja alla Security Centers funktioner mot både inbyggda och partner rekommendationer. Du kan också exportera Security Center rekommendationer till partner produkter.

[Läs mer om Microsoft Intelligent Security Association](https://www.microsoft.com/security/partnerships/intelligent-security-association).



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>Avancerad integrering med export av rekommendationer och aviseringar (för hands version)

För att aktivera scenarier på företags nivå ovanpå Security Center, är det nu möjligt att använda Security Center aviseringar och rekommendationer på ytterligare platser förutom Azure Portal eller API. Dessa kan exporteras direkt till en Event Hub och till Log Analytics arbets ytor. Här följer några arbets flöden som du kan skapa runt dessa nya funktioner:

- Med exportera till Log Analytics arbets yta kan du skapa anpassade instrument paneler med Power BI.
- Med exportera till Event Hub kan du exportera Security Center aviseringar och rekommendationer till Siem från tredje part, till en lösning i real tid eller Azure Datautforskaren.


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>Publicera på lokal-servrar för att Security Center från Windows administrations Center (för hands version)

Windows administrations Center är en hanterings Portal för Windows-servrar som inte distribueras i Azure och erbjuder dem flera hanterings funktioner för Azure, till exempel säkerhets kopierings-och system uppdateringar. Vi har nyligen lagt till en möjlighet att publicera dessa icke-Azure-servrar som ska skyddas av ASC direkt från Windows Admin Center-upplevelsen.

Med den här nya upplevelsen kommer användare att bli medlem i en WAC-Server för att Azure Security Center och kunna visa sina säkerhets aviseringar och rekommendationer direkt i Windows administrations Center.


## <a name="september-2019"></a>September 2019

Uppdateringar i september omfattar:

 - [Hantera regler med anpassningsbara program kontroller förbättringar](#managing-rules-with-adaptive-application-controls-improvements)
 - [Kontrol lera säkerhets rekommendationer för behållare med hjälp av Azure Policy](#control-container-security-recommendation-using-azure-policy)

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>Hantera regler med anpassningsbara program kontroller förbättringar

Erfarenheten av att hantera regler för virtuella datorer med anpassningsbara program kontroller har förbättrats. Azure Security Center adaptiva program kontroller hjälper dig att styra vilka program som kan köras på dina virtuella datorer. Förutom en allmän förbättring av regel hanteringen gör en ny förmån att du kan styra vilka filtyper som ska skyddas när du lägger till en ny regel.

[Läs mer om anpassningsbara program kontroller](security-center-adaptive-application.md).


### <a name="control-container-security-recommendation-using-azure-policy"></a>Kontrol lera säkerhets rekommendationer för behållare med hjälp av Azure Policy

Azure Security Center rekommendationen att åtgärda sårbarheter i behållar säkerhet kan nu aktive ras eller inaktive ras via Azure Policy.

Om du vill visa dina aktiverade säkerhets principer från Security Center öppnar du sidan säkerhets princip.


## <a name="august-2019"></a>Augusti 2019

Uppdateringarna i augusti inkluderar:

 - [Just-in-Time (JIT) VM-åtkomst för Azure-brandvägg](#just-in-time-jit-vm-access-for-azure-firewall)
 - [Reparation med enkel klickning för att öka din säkerhets position (för hands version)](#single-click-remediation-to-boost-your-security-posture-preview)
 - [Hantering av flera klienter](#cross-tenant-management)

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Just-in-Time (JIT) VM-åtkomst för Azure-brandvägg 

Just-in-Time (JIT) VM-åtkomst för Azure Firewall är nu allmänt tillgänglig. Använd den för att skydda dina Azure Firewall-skyddade miljöer förutom dina NSG-skyddade miljöer.

JIT VM-åtkomst minskar exponeringen för nätverks volym attacker genom att tillhandahålla kontrollerad åtkomst till virtuella datorer endast vid behov, med hjälp av NSG-och Azure brand Väggs regler.

När du aktiverar JIT för dina virtuella datorer skapar du en princip som avgör vilka portar som ska skyddas, hur länge portarna ska vara öppna och godkända IP-adresser från var dessa portar kan nås. Den här principen hjälper dig att hålla kontrollen över vad användarna kan göra när de begär åtkomst.

Begär Anden loggas i aktivitets loggen i Azure så att du enkelt kan övervaka och granska åtkomst. Sidan just-in-Time hjälper dig också att snabbt identifiera befintliga virtuella datorer med JIT aktiverat och virtuella datorer där JIT rekommenderas.

[Läs mer om Azure-brandväggen](https://docs.microsoft.com/azure/firewall/overview).


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>Reparation med enkel klickning för att öka din säkerhets position (för hands version)

Säkra poäng är ett verktyg som hjälper dig att utvärdera din position för arbets belastnings säkerhet. Den granskar dina säkerhets rekommendationer och prioriterar dem åt dig, så att du vet vilka rekommendationer som ska utföras först. Detta hjälper dig att hitta de säkerhets risker som är mest allvarliga för att prioritera undersökningen.

Vi har lagt till en ny funktion som gör det möjligt att reparera en rekommendation om en samling resurser i ett enda klick för att förenkla reparationen av felkonfigurationer av säkerhet och hjälpa dig att snabbt förbättra dina säkra poäng.

Med den här åtgärden kan du välja de resurser som du vill tillämpa reparationen på och starta en reparations åtgärd som konfigurerar inställningen för din räkning.

Se vilka rekommendationer som har snabb korrigering aktive rad i [referens guiden för säkerhets rekommendationer](recommendations-reference.md).


### <a name="cross-tenant-management"></a>Hantering av flera klienter

Security Center stöder nu scenarier för hantering av flera innehavare som en del av Azure-Lighthouse. På så sätt kan du få insyn och hantera säkerhets position för flera klienter i Security Center. 

[Läs mer om hanterings upplevelser mellan flera innehavare](security-center-cross-tenant-management.md).


## <a name="july-2019"></a>Juli 2019

### <a name="updates-to-network-recommendations"></a>Uppdateringar av nätverks rekommendationer

Azure Security Center (ASC) har lanserat nya nätverks rekommendationer och förbättrade några befintliga. Nu kan du med hjälp av Security Center se till att det är ännu större nätverks skyddet för dina resurser. 

[Lär dig mer om nätverks rekommendationer](recommendations-reference.md#recs-network).


## <a name="june-2019"></a>Juni 2019

### <a name="adaptive-network-hardening---generally-available"></a>Anpassad nätverks härdning – allmänt tillgänglig

En av de största angrepps ytorna för arbets belastningar som körs i det offentliga molnet är anslutningar till och från det offentliga Internet. Våra kunder tycker att det är svårt att veta vilka regler för nätverks säkerhets grupper (NSG) som ska vara på plats för att se till att Azure-arbetsbelastningar endast är tillgängliga för de käll intervall som krävs. Med den här funktionen kan Security Center lära sig nätverks trafik-och anslutnings mönster för Azure-arbetsbelastningar och ger rekommendationer för NSG-regler för virtuella datorer som är riktade mot Internet. Detta hjälper våra kunder att bättre konfigurera sina principer för nätverks åtkomst och begränsa deras exponering för attacker. 

[Lär dig mer om anpassad nätverks härdning](security-center-adaptive-network-hardening.md).
