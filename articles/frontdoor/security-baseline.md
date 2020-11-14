---
title: Azures säkerhets bas linje för Azures front dörr
description: Säkerhets bas linjen för Azures front dörr ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: frontdoor
ms.topic: conceptual
ms.date: 11/12/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 2b5995478d1c9e65916f76c70c8af374ce82ca54
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2020
ms.locfileid: "94631577"
---
# <a name="azure-security-baseline-for-azure-front-door"></a>Azures säkerhets bas linje för Azures front dörr

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark version 2,0](../security/benchmarks/overview.md) till Azures front dörr. Azures säkerhets benchmark ger rekommendationer om hur du kan skydda dina moln lösningar på Azure. Innehållet grupperas efter **säkerhets kontrollerna** som definieras av Azures säkerhets benchmark och relaterade rikt linjer som gäller för Azures front dörr. **Kontroller** som inte är tillämpliga på Azures front dörr har uteslutits.

Om du vill se hur Azures front dörr är helt mappad till Azures säkerhets benchmark, se den [fullständiga Azure-filen för säkerhets bas linje mappning](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets principen för Azure-säkerhet: nätverks säkerhet](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-2-connect-private-networks-together"></a>NS-2: Anslut privata nätverk till varandra

**Vägledning** : ej tillämpligt; Azures front dörr är inte utformad för att distribueras till eller vara skyddad till ett privat nätverk, den här kontrollen är avsedd att beskriva nätverks anslutningar och gäller inte.

**Azure Security Center övervakning** : inte tillämpligt

**Ansvars område** : unset. Ange ett värde i arbets uppgiften.

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: upprätta privat nätverks åtkomst till Azure-tjänster

**Vägledning** : inte tillämpligt, Azures front dörr är inte utformad för att distribueras till eller skyddas av ett virtuellt nätverk för privat nätverks åtkomst.

**Azure Security Center övervakning** : inte tillämpligt

**Ansvars område** : unset. Ange ett värde i arbets uppgiften.

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: skydda program och tjänster från externa nätverks attacker

**Vägledning** : Använd Azure PowerShell för att skapa en princip för geo-filtrering och koppla principen till din befintliga Azure frontend-frontend-värd. Denna princip för geo-filtrering blockerar förfrågningar från externa nätverk, till exempel de från andra länder eller regioner utom USA.

- [Självstudie – så här konfigurerar du en princip för geo-filtrering WAF för din front dörr](front-door-tutorial-geo-filtering.md)

**Azure Security Center övervakning** : Ja

**Ansvar** : kund

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: förenkla nätverks säkerhets regler

**Vägledning** : Använd Virtual Network Service-taggar för att definiera nätverks åtkomst kontroller för nätverks säkerhets grupper som kon figurer ATS för dina resurser i Azures front dörr. Service märken kan användas i stället för vissa IP-adresser när du skapar säkerhets regler. Genom att ange service tag-namnet (AzureFrontDoor. frontend, AzureFrontDoor. backend, AzureFrontDoor. FirstParty) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. 

Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

- [Förstå och använda service märken](../virtual-network/service-tags-overview.md)

**Azure Security Center övervakning** : inte tillämpligt

**Ansvar** : kund

## <a name="identity-management"></a>Identitetshantering

*Mer information finns i [Azure Security benchmark: Identity Management](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: begränsa åtkomsten till Azure-resursen baserat på villkor

**Vägledning** : som standard svarar Azures front dörr på alla användar förfrågningar oavsett var begäran kommer. Kunder kan också begränsa åtkomsten till webb programmet efter länder eller regioner. Brand Väggs tjänsten för webb program i Azure-frontend gör det möjligt för kunder att definiera en princip med hjälp av anpassade åtkomst regler, för en specifik sökväg på slut punkten, antingen tillåta eller blockera åtkomst från angivna länder eller regioner.

- [Självstudie – så här konfigurerar du en princip för geo-filtrering WAF för din Azure-front dörr](front-door-tutorial-geo-filtering.md)

**Azure Security Center övervakning** : för närvarande inte tillgängligt

**Ansvar** : kund

## <a name="privileged-access"></a>Privilegie rad åtkomst

*Mer information finns i [Azure Security benchmark: privilegie rad åtkomst](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: begränsa administrativ åtkomst till verksamhets kritiska system

**Vägledning** : Azure-frontend använder Azure-rollbaserad åtkomst kontroll (Azure RBAC) för att isolera åtkomst till verksamhets kritiska system. Använd Azure RBAC för att begränsa konton som beviljas privilegie rad åtkomst till prenumerationer och hanterings grupper, där de finns.

Säkerställ begränsad åtkomst till hanterings-, identitets-och säkerhets system som har administrativ åtkomst till affärs kritiska system, till exempel Active Directory-domän kontrollanter, säkerhets verktyg och system hanterings verktyg. Justera alla typer av åtkomst kontroller till din strategi för företags segmentering för kontinuerlig och konsekvent implementering.

- [Azure-komponenter och referens modell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Åtkomst till hanterings grupp](../governance/management-groups/overview.md#management-group-access) 

- [Administratörer för Azure-prenumeration](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Security Center övervakning** : Ja

**Ansvars område** : delat

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Använd arbets stationer med privilegie rad åtkomst

**Vägledning** : skyddade, isolerade arbets stationer är mycket viktiga för att skydda känsliga roller som administratörer, utvecklare och kritiska tjänst operatörer. 

Använd mycket säkra användar arbets stationer med Azure skydds för administrativa uppgifter. Välj Azure Active Directory (Azure AD), Microsoft Defender Avancerat skydd (ATP) och Microsoft Intune för att distribuera säkra och hanterade användar arbets stationer för administrativa uppgifter. De skyddade arbets stationerna måste hanteras centralt för att upprätthålla säker konfiguration, inklusive stark autentisering, program vara och maskin varu bas linjer, begränsad logisk och nätverks åtkomst.

- [Förstå arbets stationer med privilegie rad åtkomst](../active-directory/devices/concept-azure-managed-workstation.md) 

- [Distribuera en privilegie rad åtkomst arbets Station](../active-directory/devices/howto-azure-managed-workstation.md)

**Azure Security Center övervakning** : inte tillämpligt

**Ansvar** : kund

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Följ bara tillräckligt med administration (princip för minsta behörighet) 

**Vägledning** : Azure-front dörren är integrerad med rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att hantera dess resurser. Med Azure RBAC kan du hantera åtkomst till Azure-resurser via roll tilldelningar. Du kan tilldela dessa roller till användare, grupper tjänstens huvud namn och hanterade identiteter. Det finns fördefinierade inbyggda roller för vissa resurser och dessa roller kan inventeras eller frågas via verktyg som Azure CLI, Azure PowerShell eller Azure Portal. 

Följ den lägsta behörighets modellen för rollbaserade behörigheter som tilldelats resurser med Azure RBAC och se till att de baseras på affärs behov. Detta kompletterar JIT-metoden (just in Time) för Azure AD Privileged Identity Management (PIM) och bör granskas regelbundet.

Använd inbyggda roller för att allokera behörighet och endast skapa anpassade roller baserat på affärs krav.

- [Vad är Azure rollbaserad åtkomst kontroll (Azure RBAC)](../role-based-access-control/overview.md) 

- [Konfigurera RBAC i Azure](../role-based-access-control/role-assignments-portal.md) 

- [Använda Azure AD-identitets-och åtkomst granskningar](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center övervakning** : Ja

**Ansvars område** : delat

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security benchmark: Data Protection](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: kryptera känslig information under överföring

**Vägledning** : Använd kryptering för att skydda trafiken på externa och offentliga nätverk eftersom det är viktigt för data skydd. Dessutom:

- Använd åtkomst kontroller,

- skydda data under överföring mot "out of band"-attacker (till exempel trafik avbildning) med kryptering för att säkerställa att angripare inte kan läsa eller ändra data.
- Se till att alla klienter som ansluter till dina Azure-resurser för HTTP-trafik kan förhandla TLS v 1.2 eller senare.
- Använd SSH (för Linux) eller RDP/TLS (för Windows) för fjärrhantering, i stället för okrypterade protokoll

- Inaktivera inaktuella SSL/TLS/SSH-versioner, protokoll och svaga chiffer

Som standard tillhandahåller Azure Data överförings kryptering för data trafik mellan Azures Data Center. 

- [Självstudie – så här konfigurerar du HTTPS på en anpassad domän i en front dörr](front-door-custom-domain-https.md)

**Azure Security Center övervakning** : Ja

**Ansvars område** : delat

## <a name="asset-management"></a>Tillgångshantering

*Mer information finns i [Azure Security benchmark: Asset Management](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: se till att säkerhets teamet har insyn i risker för till gångar

**Vägledning** : se till att säkerhets team beviljas behörigheter för säkerhets läsare i din Azure-klient och prenumerationer för att övervaka säkerhets risker med hjälp av Azure Security Center. 

Beroende på hur säkerhets teamets ansvars områden är strukturerade kan övervakning av säkerhets risker vara ansvaret för en central säkerhets grupp eller ett lokalt team. Säkerhets insikter och risker måste dock alltid aggregeras centralt inom en organisation. 

Behörigheter för säkerhets läsare kan tillämpas i stort sett till en hel klient organisation (rot hanterings grupp) eller omfattas av hanterings grupper eller vissa prenumerationer. 

Obs! ytterligare behörigheter kan krävas för insyn i arbets belastningar och tjänster. 

- [Översikt över rollen säkerhets läsare](../role-based-access-control/built-in-roles.md#security-reader)

- [Översikt över Azure Hanteringsgrupper](../governance/management-groups/overview.md)

**Azure Security Center övervakning** : för närvarande inte tillgängligt

**Ansvar** : kund

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: se till att säkerhets teamet har åtkomst till till gångs inventering och metadata

**Vägledning** : Använd taggar för Azure-resurser, resurs grupper och prenumerationer för att logiskt organisera dem i en taxonomi. Varje tagg består av ett namn och ett värde-par. Du kan till exempel använda namnet "miljö" och värdet "produktion" för alla resurser i produktionen.

- [Så här skapar du frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md) 

- [Azure Security Center till gångs lager hantering](../security-center/asset-inventory.md) 

- [Beslutsguide för namngivning och taggning av resurser](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Azure Security Center övervakning** : Ja

**Ansvar** : kund

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Använd endast godkända Azure-tjänster

**Vägledning** : Använd Azure policy för att granska och begränsa vilka tjänster som användare kan etablera i din miljö. Fråga efter och identifiera resurser inom sina prenumerationer med Azure Resource Graph.

Använd Azure Monitor för att skapa regler för att utlösa aviseringar när en icke-godkänd tjänst upptäcks.

- [Så här konfigurerar och hanterar du Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Så här nekar du en speciell resurs typ med Azure Policy](../governance/policy/samples/built-in-policies.md#general) 

- [Så här skapar du frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Azure Security Center övervakning** : Ja

**Ansvar** : kund

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: se till att till gångens livs cykel hantering är säker

**Vägledning** : ej tillämpligt; Azures front dörr kan inte användas för att garantera säkerheten för till gångar i en livs cykel hanterings process. Det är kundens ansvar att underhålla attribut och nätverkskonfigurationer för till gångar som betraktas som en hög inverkan. 

Vi rekommenderar att kunden skapar en process för att avbilda attributet och ändringar i nätverks konfigurationen, mäter ändrings effekt och skapa reparations uppgifter, om det är tillämpligt.

**Azure Security Center övervakning** : inte tillämpligt

**Ansvar** : kund

## <a name="logging-and-threat-detection"></a>Loggning och hot identifiering

*Mer information finns i [säkerhets benchmark för Azure: loggning och hot identifiering](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Aktivera loggning för Azure nätverks aktiviteter

**Vägledning** : Azure klient-dörren är inte avsedd att distribueras till virtuella nätverk. på grund av att de här kunderna inte kan aktivera flödes loggning för nätverks säkerhets grupper, dirigera trafik via en brand vägg eller utför paket avbildningar.

Azures front dörr loggar all nätverks trafik som den bearbetar för kund åtkomst. Aktivera funktionen för nätverks flödes loggar och konfigurera loggarna så att de skickas till ett lagrings konto för långsiktig kvarhållning och granskning.

- [Självstudie – Hur du ställer in övervaknings mått och loggar i Azures front dörr](front-door-diagnostics.md)

**Azure Security Center övervakning** : för närvarande inte tillgängligt

**Ansvars område** : delat

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Aktivera loggning för Azure-resurser

**Vägledning** : aktivitets loggar, som är automatiskt tillgängliga, innehåller alla Skriv åtgärder (placera, skicka, ta bort) för dina Azures resurser för front dörren förutom Läs åtgärder (get). Aktivitets loggar kan användas för att hitta ett fel när du felsöker eller övervakar hur en användare i organisationen ändrade en resurs.

Aktivera Azures resurs loggar för Azures frontend-dörr. Du kan använda Azure Security Center och Azure Policy för att aktivera resurs loggar och insamling av logg data. Dessa loggar kan vara kritiska för senare utredning av säkerhets incidenter och utföra kriminal tekniska övningar.

- [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Förstå loggning och olika logg typer i Azure](../azure-monitor/platform/platform-logs-overview.md) 

- [Förstå Azure Security Center insamling av data](../security-center/security-center-enable-data-collection.md)

**Azure Security Center övervakning** : Ja

**Ansvars område** : delat

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security benchmark: incident svar](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: förberedelse – uppdatera incident svars process för Azure

**Vägledning** : se till att din organisation har definierat processer för att svara på säkerhets incidenter. Bekräfta att de här processerna har uppdaterats för Azure-resurser och testa dem regelbundet för att säkerställa beredskap.

- [Implementera säkerhet i företags miljön](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Referens guide för incident svar](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center övervakning** : inte tillämpligt

**Ansvar** : kund

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: förberedelse – installations incident meddelande

**Vägledning** : Konfigurera säkerhets incident kontakt information i Azure Security Center. Den här kontakt informationen används av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har öppnats av en olagligt eller obehörig part. Du har också alternativ för att anpassa incident aviseringar och meddelanden i olika Azure-tjänster baserat på dina incident svars behov. 

- [Så här ställer du in Azure Security Center säkerhets kontakt](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center övervakning** : Ja

**Ansvar** : kund

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: identifiering och analys – skapa incidenter baserat på aviseringar med hög kvalitet

**Vägledning** : se till att du har en process för att skapa aviseringar med hög kvalitet och mäta kvaliteten på aviseringar. På så sätt kan du lära dig lektioner från tidigare incidenter och prioritera aviseringar för analytiker, så att de inte slösar tid på falska positiva identifieringar. 

Aviseringar med hög kvalitet kan byggas utifrån erfarenhet från tidigare incidenter, validerade community-källor och verktyg som utformats för att generera och rensa aviseringar från fusing och korrelera olika signal källor. 

Azure Security Center ger aviseringar med hög kvalitet över flera Azure-tillgångar. Du kan använda ASC data Connector för att strömma aviseringarna till Azure Sentinel. Med Azure Sentinel kan du skapa avancerade aviserings regler för att generera incidenter automatiskt för en undersökning. 

Exportera dina Azure Security Center-aviseringar och rekommendationer med hjälp av export funktionen för att identifiera risker med Azure-resurser. Exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö.

- [Så här konfigurerar du export](../security-center/continuous-export.md)

- [Strömma aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center övervakning** : för närvarande inte tillgängligt

**Ansvar** : kund

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: identifiering och analys – Undersök en incident

**Vägledning** : se till att analytiker kan fråga och använda olika data källor när de undersöker potentiella incidenter för att bygga en fullständig bild av vad som hände. Olika logg typer bör samlas in för att spåra aktiviteter för en potentiell angripare över Kill-kedjan för att undvika eventuella blinda fläckar.  Se till att insikter och insikter samlas in för andra analytiker och för framtida historiska referenser.  

Data källorna för undersökningen innehåller de centraliserade loggnings källor som redan har samlats in från tjänster som omfattas och som kör system, men kan även innehålla:

- Nätverks data – Använd Nätverks säkerhets grupper flödes loggar, Azure Network Watcher och Azure Monitor för att avbilda nätverks flödes loggar och annan analys information. 

- Ögonblicks bilder av system som körs: 

    - Använd ögonblicks bild funktionen i Azure Virtual Machine för att skapa en ögonblicks bild av den aktiva system disken. 

    - Använd operativ systemets interna minnes dumpnings funktion för att skapa en ögonblicks bild av det aktiva systemets minne.

    - Använd ögonblicks bild funktionen i Azure-tjänsterna eller din program varu funktion för att skapa ögonblicks bilder av de system som körs.

Azure Sentinel tillhandahåller omfattande data analyser i praktiskt taget alla logg källor och en ärende hanterings Portal för att hantera den fullständiga livs cykeln för incidenter. Informations information under en undersökning kan associeras med en incident för spårning och rapportering. 

- [Ögonblicks bild av en Windows-dators disk](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Ögonblicks bild av en Linux-dators disk](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure stöd för diagnostikinformation och insamling av minnes dum par](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Undersök incidenter med Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Azure Security Center övervakning** : inte tillämpligt

**Ansvar** : kund

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: identifiering och analys – prioritera incidenter

**Vägledning** : Tillhandahåll sammanhang för att analysera vilka incidenter som ska fokusera på den första baserat på aviseringens allvarlighets grad och till gångens känslighet. 

Azure Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller analysera det som används för att utfärda aviseringen, samt den konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Markera dessutom resurser med taggar och skapa ett namngivnings system för att identifiera och kategorisera Azure-resurser, särskilt för bearbetning av känsliga data.  Det är ditt ansvar att prioritera reparationen av aviseringar baserat på allvarlighets graden för de Azure-resurser och den miljö där incidenten inträffade.

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Använda taggar för att organisera dina Azure-resurser](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center övervakning** : för närvarande inte tillgängligt

**Ansvar** : kund

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: inne slutning, utrotning och återställning – automatisera incident hanteringen

**Vägledning** : automatisera manuella återkommande uppgifter för att påskynda svars tiden och minska belastningen på analytiker. Manuella uppgifter tar längre tid att köra, vilket saktar ned varje incident och minskar hur många incidenter en analytiker kan hantera. Manuella uppgifter ökar också analytikerns utmattning, vilket ökar risken för mänskligt fel som orsakar fördröjningar och minskar risken för att analytikerna ska kunna fokusera på komplexa uppgifter på ett effektivt sätt. Använd automatiserings funktioner för arbets flöde i Azure Security Center och Azure Sentinel för att automatiskt utlösa åtgärder eller köra en Spelbok för att svara på inkommande säkerhets aviseringar. Spelbok tar åtgärder, till exempel att skicka meddelanden, inaktivera konton och isolera problematiska nätverk. 

- [Konfigurera automatisering av arbets flöde i Security Center](../security-center/workflow-automation.md)

- [Konfigurera automatiska hot svar i Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Konfigurera automatiska hot svar i Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Azure Security Center övervakning** : för närvarande inte tillgängligt

**Ansvar** : kund

## <a name="posture-and-vulnerability-management"></a>Position och sårbarhets hantering

*Mer information finns i [Azure Security benchmark: position and sårbarhet Management](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: upprätta säkra konfigurationer för beräknings resurser

**Vägledning** : Använd Azure Security Center och Azure policy för att upprätta säkra konfigurationer på alla beräknings resurser, inklusive Virtual Machines, behållare med mera.

- [Så här övervakar du Azure Security Center rekommendationer](../security-center/security-center-recommendations.md) 

- [Säkerhetsrekommendationer – en referensguide](../security-center/recommendations-reference.md)

**Azure Security Center övervakning** : för närvarande inte tillgängligt

**Ansvars område** : delat

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7: snabba och automatiskt åtgärdar sårbarheter i program vara

**Vägledning** : distribuera program uppdateringar snabbt för att åtgärda sårbarheter i operativ system och program.

Prioritera Använd ett gemensamt risk bedömnings program (t. ex. vanliga sårbarhets bedömnings system) eller standard risk klassificeringar som tillhandahålls av ett skannings verktyg från tredje part som du använder. och skräddarsy till din miljö med hjälp av ett sammanhang där programmen utgör en hög säkerhets risk och som kräver hög drift tid.

**Azure Security Center övervakning** : inte tillämpligt

**Ansvar** : kund

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: utför regelbunden attack simulering

**Vägledning** : vid behov kan du genomföra inträngande tester eller röda team aktiviteter på dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister.
Följ test reglerna för Microsoft Cloud inträngande för att se till att dina inträngande tester inte strider mot Microsofts principer. Använd Microsofts strategi och körning av röda team indelning och inträngande av direktsända webbplatser mot Microsoft-hanterad moln infrastruktur, tjänster och program.

- [Inträngande testning i Azure](../security/fundamentals/pen-testing.md)

- [Inträngande test regler för engagemang](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud röd team indelning](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center övervakning** : inte tillämpligt

**Ansvars område** : delat

## <a name="governance-and-strategy"></a>Styrning och strategi

*Mer information finns i [Azure Security benchmark: styrning och strategi](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: definiera strategi för till gångs hantering och data skydd 

**Vägledning** : se till att dokumentera och förmedla en tydlig strategi för kontinuerlig övervakning och skydd av system och data. Prioritera identifiering, utvärdering, skydd och övervakning av affärs kritiska data och system. 

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

-   Data klassificerings standard i enlighet med affärs riskerna

-   Insyn i säkerhets organisationen för risker och till gångs inventering 

-   Godkännande av säkerhets organisation för Azure-tjänster för användning 

-   Säkerhet för till gångar via deras livs cykel

-   Nödvändig åtkomst kontroll strategi i enlighet med organisationens data klassificering

-   Användning av data skydds funktioner i Azure Native och tredje part

-   Krav på data kryptering för överförings-och rest-användnings fall

-   Lämpliga kryptografiska standarder

Mer information finns på de länkar som refereras till.

- [Rekommendation för Azure Security Architecture – lagring, data och kryptering](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Grundläggande Azure-säkerhet – Azure Data Security, kryptering och lagring](../security/fundamentals/encryption-overview.md)

- [Azure Security benchmark – data skydd](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Azure Security Center övervakning** : inte tillämpligt

**Ansvar** : kund

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: definiera strategi för företags segmentering 

**Vägledning** : upprätta en företagsomfattande strategi för att segmentera åtkomst till till gångar med en kombination av identitet, nätverk, program, prenumeration, hanterings grupp och andra kontroller.

Du bör noggrant balansera behovet av säkerhets separation med behovet av att möjliggöra daglig drift av de system som behöver kommunicera med varandra och komma åt data.

Se till att segmenterings strategin implementeras konsekvent över kontroll typer, inklusive nätverks säkerhet, identitets-och åtkomst modeller samt program behörighet/åtkomst modeller och kontroller för mänsklig process.

- [Vägledning om segment strategi i Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Vägledning om segment strategi i Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Justera nätverks segmentering med strategi för företags segmentering](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center övervakning** : inte tillämpligt

**Ansvar** : kund

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: definiera säkerhets position hanterings strategi

**Vägledning** : ständigt mäta och minimera risker för enskilda till gångar och miljön som de finns i. Prioritera höga värde till gångar och högexponerade attack ytor, till exempel publicerade program, ingångs-och utgångs punkter, användar-och administratörs slut punkter och så vidare.

- [Azure Security benchmark – position och sårbarhets hantering](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Azure Security Center övervakning** : inte tillämpligt

**Ansvar** : kund

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: justera organisations roller, ansvars områden och Accountabilities

**Vägledning** : se till att dokumentera och förmedla en tydlig strategi för roller och ansvar i din säkerhets organisation. Prioritera att tillhandahålla tydliga ansvar för säkerhets beslut, utbilda alla på den delade ansvars modellen och utbilda tekniska team om teknik för att skydda molnet.

- [Bästa praxis för Azure-säkerhet 1 – personer: utbilda team om molnets säkerhets resa](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Bästa praxis för Azure-säkerhet 2 – personer: utbilda team om moln säkerhets teknik](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Bästa praxis för Azure Security 3 – process: tilldela ansvar för moln säkerhets beslut](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure Security Center övervakning** : inte tillämpligt

**Ansvar** : kund

### <a name="gs-5-define-network-security-strategy"></a>GS-5: definiera nätverks säkerhets strategi

**Vägledning** : upprätta en Azure Network Security-metod som en del av din organisations övergripande strategi för säkerhets åtkomst kontroll.  

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

-   Centraliserad nätverks hantering och säkerhets ansvar

-   Modell för virtuella nätverks segment justerade med strategin för företags segmentering

-   Reparations strategi i olika hot och angrepps scenarier

-   Strategi för Internet Edge och inkommande och utgående

-   Hybrid moln och lokal strategi för samanslutning

-   Aktuella nätverks säkerhets artefakter (t. ex. nätverks diagram, referens nätverks arkitektur)

Mer information finns på de länkar som refereras till.

- [Azure-säkerhet Best Practice 11-Architecture. Enhetlig säkerhets strategi](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Säkerhet för Azure-säkerhet](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Översikt över Azure Network Security](../security/fundamentals/network-overview.md)

**Azure Security Center övervakning** : inte tillämpligt

**Ansvar** : kund

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: definiera identitets-och privilegie rad åtkomst strategi

**Vägledning** : upprätta en Azure Identity-och Privileged Access-metod som en del av din organisations övergripande strategi för säkerhets åtkomst kontroll.  

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

-   Ett centraliserat identitets-och autentiserings system och dess samanslutning med andra interna och externa identitets system

-   Starka autentiseringsmetoder i olika användnings fall och villkor

-   Skydd av privilegierade användare

-   Avvikande användar aktiviteter, övervakning och hantering  

-   Process för användar identitets-och åtkomst granskning och avstämning

Mer information finns i följande referenser:

- [Azure-säkerhet för prestandatest – identitets hantering](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure Security benchmark – privilegie rad åtkomst](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Azure-säkerhet Best Practice 11-Architecture. Enhetlig säkerhets strategi](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Översikt över Azure Identity Management-säkerhet](../security/fundamentals/identity-management-overview.md)

**Azure Security Center övervakning** : inte tillämpligt

**Ansvar** : kund

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: definiera strategi för loggning och hot svar

**Vägledning** : upprätta en strategi för loggning och hot svar för att snabbt upptäcka och åtgärda hot samtidigt som de uppfyller kraven för efterlevnad. Prioritera att ge analytiker med hög kvalitets varningar och sömlösa upplevelser så att de kan fokusera på hot i stället för integrering och manuella steg. 

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

-   Organisationens roll och ansvars områden för säkerhets åtgärder (SecOps) 

-   En väldefinierad incident svars process som justeras med NIST eller något annat bransch ramverk 

-   Insamling och kvarhållning av loggar för att stödja hot identifiering, incident svar och krav på efterlevnad

-   Centraliserad synlighet och korrelations information om hot, med SIEM, interna Azure-funktioner och andra källor 

-   Kommunikation och meddelande plan med dina kunder, leverantörer och offentliga intressen

-   Användning av inbyggda Azure-och tredjeparts-plattformar för incident hantering, till exempel loggning och hot identifiering, data utredning och angrepp och utrotning

-   Processer för hantering av incidenter och aktiviteter efter incidenter, till exempel lektioner som lärts och underlags kvarhållning

Mer information finns på de länkar som refereras till.

- [Azure-säkerhet för benchmark-loggning och hot identifiering](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure-säkerhet benchmark – incident svar](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure Security Best Practice 4 – process. Uppdatera incident svars processer för molnet](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Besluts guide för Azure införande Framework, loggning och rapportering](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting)

**Azure Security Center övervakning** : inte tillämpligt

**Ansvar** : kund

## <a name="next-steps"></a>Nästa steg

- Se [Översikt över Azure Security benchmark v2](/azure/security/benchmarks/overview)
- Läs mer om [Azures säkerhets bas linjer](/azure/security/benchmarks/security-baselines-overview)
