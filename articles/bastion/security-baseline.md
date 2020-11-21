---
title: Azures säkerhets bas linje för Azure skydds
description: Azure skydds Security-bas linjen ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: bastion
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 007b82b4466dfc2fbfc9c11340583175da45d92e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025854"
---
# <a name="azure-security-baseline-for-azure-bastion"></a>Azures säkerhets bas linje för Azure skydds

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark-version 2,0](../security/benchmarks/overview.md) till Azure skydds. Azures säkerhets benchmark ger rekommendationer om hur du kan skydda dina moln lösningar på Azure. Innehållet grupperas efter de **säkerhets kontroller** som definieras av Azures säkerhets benchmark och relaterade rikt linjer för Azure skydds. **Kontroller** som inte gäller för Azure-skydds har uteslutits.

Om du vill se hur Azure-skydds helt mappar till Azures säkerhets benchmark kan du läsa mer i den [fullständiga mappnings filen för Azure skydds Security](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets principen för Azure-säkerhet: nätverks säkerhet](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementera säkerhet för intern trafik

**Vägledning**: när du distribuerar Azure skydds-resurser måste du skapa eller använda ett befintligt virtuellt nätverk. Se till att alla virtuella Azure-nätverk följer en företags segment princip som passar affärs riskerna. Alla system som kan ådra sig högre risk för organisationen bör isoleras i ett eget virtuellt nätverk och tillräckligt säkra med en nätverks säkerhets grupp (NSG).

Azure skydds-tjänsten kräver att följande portar måste vara öppna för att tjänsten ska fungera korrekt:

- Ingress trafik:
   - Ingress trafik från offentlig Internet: Azure-skydds skapar en offentlig IP-adress som behöver port 443 aktiverat på den offentliga IP-adressen för inkommande trafik. Port 3389/22 behöver inte öppnas på AzureBastionSubnet. 

   - Ingress trafik från Azure skydds Control plan: för kontroll Plans anslutning aktiverar du port 443 inkommande från GatewayManager service tag. Detta gör det möjligt för kontroll planet, det vill säga att Gateway Manager kan kommunicera med Azure-skydds.

- Utgående trafik:

   - Utgående trafik till virtuella datorer (VM: ar): Azure-skydds kommer att nå de virtuella mål datorerna via privat IP. NSG: er måste tillåta utgående trafik till andra mål-VM-undernät för port 3389 och 22.

   - Utgående trafik till andra offentliga slut punkter i Azure: Azure-skydds måste kunna ansluta till olika offentliga slut punkter i Azure (till exempel för att lagra diagnostikloggar och avläsnings loggar). Av den anledningen behöver Azure skydds utgående till 443 till AzureCloud service tag.

Anslutning till Gateway Manager och Azure Service tag är skyddat (låst) av Azure-certifikat. Externa entiteter, inklusive konsumenter av dessa resurser, kan inte kommunicera med dessa slut punkter. 

- [Så här skapar du en nätverks säkerhets grupp med säkerhets regler](../virtual-network/tutorial-filter-network-traffic.md)

- [Du kan lära dig mer om skydds NSG-krav här](bastion-nsg.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="ns-2-connect-private-networks-together"></a>NS-2: Anslut privata nätverk till varandra

**Vägledning**: Azure skydds visar inte några slut punkter som kan nås via ett privat nätverk. Azure skydds stöder distribution till ett peer-kopplat nätverk för att centralisera din skydds-distribution och möjliggör anslutning mellan nätverk.

- [Azure-skydds och peering för virtuella nätverk](vnet-peering.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="identity-management"></a>Identitetshantering

*Mer information finns i [Azure Security benchmark: Identity Management](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: standardisera Azure Active Directory som system för central identitets-och autentisering

**Vägledning**: Azure skydds är integrerat med Azure Active Directory (Azure AD) som är Azures standard tjänst för identitets-och åtkomst hantering. Användare kan komma åt Azure Portal med Azure AD-autentisering för att hantera Azure skydds-tjänsten (skapa, uppdatera och ta bort skydds-resurser).

Att ansluta till virtuella datorer med Azure skydds är beroende av antingen en SSH-nyckel eller användar namn/lösen ord, och stöder för närvarande inte användning av autentiseringsuppgifter för Azure AD.

Förutom en SSH-nyckel eller användar namn/lösen ord när du ansluter till virtuella datorer med Azure skydds behöver du följande roll tilldelningar:
- Läsar roll på den virtuella mål datorn
- Rollen läsare på NÄTVERKSKORTet med den privata IP-adressen för den virtuella mål datorn
- Läsarroll på Azure Bastion-resursen

Mer information finns i följande referenser:

- [Ansluta till en virtuell Linux-dator med Azure skydds](bastion-connect-vm-ssh.md)

- [Ansluta till en virtuell Windows-dator med Azure skydds](bastion-connect-vm-rdp.md)

- [Inbyggda roller i Azure](../role-based-access-control/built-in-roles.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Använd enkel inloggning för Azure AD (SSO) för program åtkomst

**Vägledning**: Azure skydds stöder inte SSO för autentisering vid autentisering till virtuella dator resurser, endast SSH eller username/Password stöds. Azure skydds använder dock Azure Active Directory (Azure AD) för att tillhandahålla identitets-och åtkomst hantering för den övergripande tjänsten. Användare kan autentisera sig på Azure AD för att komma åt och hantera sina Azure skydds-resurser och få en sömlös enkel inloggning med sina egna synkroniserade företags identiteter via Azure AD Connect. 

- [Förstå Application SSO med Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Azure AD Connect](../active-directory/hybrid/whatis-azure-ad-connect.md)

- [Ansluta till en virtuell Linux-dator med Azure skydds](bastion-connect-vm-ssh.md)

- [Ansluta till en virtuell Windows-dator med Azure skydds](bastion-connect-vm-rdp.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Använd starka verifierings kontroller för alla Azure Active Directory-baserade åtkomst

**Vägledning**: Azure skydds är integrerat med Azure Active Directory (Azure AD) för åtkomst och hantering av tjänsten. Konfigurera Azure-Multi-Factor Authentication för din Azure AD-klient. Azure AD stöder starka verifierings kontroller med Multi-Factor Authentication (MFA) och starka metoder för lösen ords kryptering.  
- Multi-Factor Authentication: Aktivera Azure AD MFA och följ Azure Security Center rekommendationer för identitets-och åtkomst hantering för MFA-installationen. MFA kan tillämpas på alla användare, välja användare eller på nivån per användare baserat på inloggnings villkor och riskfaktorer. 

- Lösen ords kryptering: tre alternativ för lösen ords kryptering är tillgängliga: Windows Hello för företag, Microsoft Authenticator app och lokala autentiseringsmetoder som smartkort. 

- [Så här aktiverar du MFA i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Introduktion till alternativ för lösen ords lös autentisering för Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: övervaka och Varna vid konto avvikelser

**Vägledning**: Aktivera diagnostikloggar för Azure skydds-fjärrsessioner och Använd dessa loggar för att visa vilka användare som är anslutna till vilka arbets belastningar, vid vilken tidpunkt, och annan relevant loggnings information. Skapa aviseringar för vissa loggade skydds-sessioner som använder Azure Monitor för att meddelas när det finns avvikelser som upptäckts i loggarna.

- [Du hittar mer information om hur du aktiverar diagnostikloggning här](diagnostic-logs.md)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: begränsa åtkomsten till Azure-resursen baserat på villkor

**Vägledning**: du kan endast få åtkomst till Azure skydds-tjänsten via Azure Portal. åtkomst till Azure Portal kan begränsas med hjälp av Azure Active Directory (Azure AD) villkorlig åtkomst. Använd villkorlig åtkomst i Azure AD för mer detaljerad åtkomst kontroll baserat på användardefinierade villkor, till exempel att kräva användar inloggningar från vissa IP-intervall för att använda MFA.

Kunden kan också använda olika rollbaserade principer för åtkomst kontroll på domänanslutna virtuella datorer för att ytterligare begränsa åtkomsten till den virtuella datorn.

- [Du kan läsa mer om villkorlig åtkomst för Azure AD här](../active-directory/conditional-access/overview.md)

- [Översikt över villkorlig Azure-åtkomst](../active-directory/conditional-access/overview.md)

- [Vanliga principer för villkorlig åtkomst](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Konfigurera hantering av autentisering med villkorlig åtkomst](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="privileged-access"></a>Privilegie rad åtkomst

*Mer information finns i [Azure Security benchmark: privilegie rad åtkomst](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: begränsa administrativ åtkomst till verksamhets kritiska system

**Vägledning**: Azure skydds använder Azures rollbaserad åtkomst kontroll (Azure RBAC) för att isolera åtkomst till affärs kritiska system genom att begränsa vilka konton som beviljas åtkomst för att ansluta till vissa virtuella datorer. Se till att du följer principen om minsta behörighet så att användarna bara har de behörigheter som krävs för att utföra sina uppgifter.

Se till att du också begränsar åtkomsten till hanterings-, identitets-och säkerhets system som har administrativ åtkomst till din affärs kritiska åtkomst, till exempel Active Directory-domän controllers (DCs), säkerhets verktyg och system hanterings verktyg med agenter som är installerade på affärs kritiska system. Angripare som angriper dessa hanterings-och säkerhets system kan omedelbart weaponize dem för att kompromettera affärs kritiska till gångar.

Alla typer av åtkomst kontroller bör justeras mot din strategi för företags segmentering för att säkerställa en konsekvent åtkomst kontroll.

- [Roller som krävs för att få åtkomst till en virtuell dator med Azure skydds](bastion-faq.md#roles)

- [Azure-komponenter och referens modell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Åtkomst till hanterings grupp](../governance/management-groups/overview.md#management-group-access)

- [Administratörer för Azure-prenumeration](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: granska och Stäm av användar åtkomst regelbundet

**Vägledning**: Azure skydds använder Azure Active Directory-konton (Azure AD) och Azure RBAC för att hantera sina resurser. Granska användar konton och åtkomst tilldelningen regelbundet för att säkerställa att kontona och deras åtkomst är giltiga. Du kan använda åtkomst granskningar i Azure AD för att granska grupp medlemskap, åtkomst till företags program och roll tilldelningar. Azure AD repor ting kan tillhandahålla loggar för att identifiera inaktuella konton. Du kan också använda Azure AD Privileged Identity Management för att skapa åtkomst gransknings rapportens arbets flöde för att under lätta gransknings processen.

Dessutom kan Azure Privileged Identity Management också konfigureras för att varna när ett högt antal administratörs konton skapas och för att identifiera administratörs konton som är inaktuella eller felaktigt konfigurerade.

- [Skapa en åtkomst granskning av Azure Resource roles i Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Ta bort åtkomst till en delegering](../lighthouse/how-to/remove-delegation.md)

- [Använda Azure AD-identitets-och åtkomst granskningar](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Konfigurera nöd åtkomst i Azure AD

**Vägledning**: Azure skydds är integrerat med Azure Active Directory och Azure RBAC för att hantera sina resurser. För att förhindra oavsiktligt låst av din Azure AD-organisation konfigurerar du ett konto för nöd åtkomst för åtkomst när normala administrativa konton inte kan användas. Konton för nöd åtkomst är ofta mycket privilegierade och de bör inte tilldelas vissa individer. Konton för nöd åtkomst är begränsade till nöd situationer eller "Break glas"-scenarier där normala administrativa konton inte kan användas.

Du bör se till att autentiseringsuppgifterna (till exempel lösen ord, certifikat eller smartkort) för konton för nöd åtkomst hålls skyddade och endast kända för personer som har behörighet att använda dem endast i nödfall.

- [Hantera konton för nöd åtkomst i Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatisera rättighets hantering 

**Vägledning**: Azure skydds är integrerat med Azure Active Directory (Azure AD) och Azure RBAC för att hantera sina resurser. Använd funktioner för hantering av Azure AD-rättighet för att automatisera åtkomst till begär ande arbets flöden, inklusive åtkomst tilldelningar, recensioner och förfallo datum. Det finns också stöd för godkännande med dubbla eller flera steg.

- [Vad är åtkomst granskningar för Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Vad är hantering av Azure AD-berättigande](../active-directory/governance/entitlement-management-overview.md)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Använd arbets stationer med privilegie rad åtkomst

**Vägledning**: skyddade, isolerade arbets stationer är mycket viktiga för att skydda känsliga roller som administratörer, utvecklare och kritiska tjänst operatörer. Beroende på dina behov kan du använda mycket säkra användar arbets stationer för att utföra administrativa hanterings uppgifter med dina Azure skydds-resurser i produktions miljöer. Använd Azure Active Directory, Microsoft Defender Avancerat skydd (ATP) och/eller Microsoft Intune för att distribuera en säker och hanterad användar arbets station för administrativa uppgifter. De skyddade arbets stationerna kan hanteras centralt för att upprätthålla säker konfiguration, inklusive stark autentisering, program vara och maskin varu bas linjer och begränsad logisk och nätverks åtkomst. 

- [Förstå arbets stationer med privilegie rad åtkomst](../active-directory/devices/concept-azure-managed-workstation.md)

- [Distribuera en privilegie rad åtkomst arbets Station](../active-directory/devices/howto-azure-managed-workstation.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Följ bara tillräckligt med administration (princip för minsta behörighet) 

**Vägledning**: Azure skydds är integrerat med rollbaserad åtkomst kontroll (RBAC) i Azure för att hantera dess resurser. Med Azure RBAC kan du hantera åtkomst till Azure-resurser via roll tilldelningar. Du kan tilldela de här inbyggda rollerna till användare, grupper, tjänstens huvud namn och hanterade identiteter. Det finns fördefinierade inbyggda roller för vissa resurser och dessa roller kan inventeras eller frågas via verktyg som Azure CLI, Azure PowerShell eller Azure Portal. De behörigheter som du tilldelar resurser via Azure RBAC bör alltid vara begränsade till vad som krävs av rollerna. Detta kompletterar JIT-metoden (just in Time) för Azure AD Privileged Identity Management (PIM) och bör granskas regelbundet. Använd inbyggda roller för att allokera behörighet och endast skapa anpassade roller vid behov.

När du ansluter till virtuella datorer med Azure skydds behöver du följande roll tilldelningar:
- Läsar roll på den virtuella mål datorn
- Rollen läsare på NÄTVERKSKORTet med den privata IP-adressen för den virtuella mål datorn
- Läsarroll på Azure Bastion-resursen

Mer information finns i följande referenser:

- [Ansluta till en virtuell Linux-dator med Azure skydds](bastion-connect-vm-ssh.md)

- [Ansluta till en virtuell Windows-dator med Azure skydds](bastion-connect-vm-rdp.md)

- [Inbyggda roller i Azure](../role-based-access-control/built-in-roles.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="asset-management"></a>Tillgångshantering

*Mer information finns i [Azure Security benchmark: Asset Management](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: se till att säkerhets teamet har insyn i risker för till gångar

**Vägledning**: se till att säkerhets team beviljas behörigheter för säkerhets läsare i din Azure-klient och prenumerationer så att de kan övervaka säkerhets risker med hjälp av Azure Security Center. 

Beroende på hur säkerhets teamets ansvars områden är strukturerade kan övervakning av säkerhets risker vara ansvaret för en central säkerhets grupp eller ett lokalt team. Detta innebär att säkerhets insikter och risker alltid måste aggregeras centralt inom en organisation. 

Behörigheter för säkerhets läsare kan tillämpas i stort sett till en hel klient organisation (rot hanterings grupp) eller omfattas av hanterings grupper eller vissa prenumerationer. 

Obs: ytterligare behörigheter kan krävas för att få insyn i arbets belastningar och tjänster. 

- [Översikt över rollen säkerhets läsare](../role-based-access-control/built-in-roles.md#security-reader)

- [Översikt över Azure Hanteringsgrupper](../governance/management-groups/overview.md)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: se till att säkerhets teamet har åtkomst till till gångs inventering och metadata

**Vägledning**: Använd taggar för dina Azure skydds-resurser, resurs grupper och prenumerationer för att logiskt organisera dem i en taxonomi. Varje tagg består av ett namn och ett värde-par. Du kan till exempel använda namnet ”Miljö” och värdet ”Produktion” för alla resurser i produktionsmiljön. Se till att säkerhets teamen har åtkomst till en kontinuerligt uppdaterad inventering av till gångar på Azure. De kan använda Azure Resource Graph för att fråga och identifiera alla resurser i dina prenumerationer, inklusive Azure-tjänster, program och nätverks resurser.

- [Så här skapar du frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [Azure Security Center till gångs lager hantering](../security-center/asset-inventory.md)

- [Mer information om att tagga till gångar finns i besluts guiden för resurs namn givning och taggning](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att granska och begränsa vilka tjänster som användare kan etablera i din miljö, däribland att kunna tillåta eller neka distributioner av Azure skydds-resurser. Använd Azure Resource Graph för att fråga efter och identifiera resurser i prenumerationerna. Du kan också använda Azure Monitor för att skapa regler för att utlösa aviseringar när en icke-godkänd tjänst upptäcks.

- [Så här konfigurerar och hanterar du Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en speciell resurs typ med Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Så här skapar du frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: se till att till gångens livs cykel hantering är säker

**Vägledning**: ta bort åtkomst till Azure skydds-resurser som har distribuerats när de inte längre behövs för att minimera attack ytan. Användare kan hantera sina Azure skydds-resurser via Azure Portal-, CLI-eller REST-API: er. Du kan också ta bort eller tvinga bort en pågående fjärrsession om den inte längre behövs eller identifieras som ett potentiellt hot.

- [Ta bort tvångs anslutning till en fjärrsession](session-monitoring.md#view)

- [Azure Network CLI](https://docs.microsoft.com/powershell/module/az.network/?view=azps-5.1.0#networking&amp;preserve-view=true)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Azure skydds är integrerat med Azure Active Directory (Azure AD) för identitet och autentisering. Du kan använda villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azures resurser Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azures resurs hanterare](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

## <a name="logging-and-threat-detection"></a>Loggning och hot identifiering

*Mer information finns i [säkerhets benchmark för Azure: loggning och hot identifiering](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Aktivera hot identifiering för Azure Identity and Access Management

**Vägledning**: Azure skydds integreras med Azure Active Directory (Azure AD) och tjänsten nås via Azure Portal. Som standard registreras hanterings åtgärder för tjänsten (till exempel skapa, uppdatera och ta bort) via Azure aktivitets loggen. Användarna bör också aktivera resurs loggar för Azure skydds, till exempel session BastionAuditLogs, för att spåra skydds-sessioner.

Azure AD innehåller följande användar loggar som kan visas i Azure AD repor ting eller integreras med Azure Monitor, Azure Sentinel eller andra SIEM/övervaknings verktyg för mer avancerade övervaknings-och analys användnings fall: 
-   Inloggningar – inloggnings rapporten innehåller information om användningen av hanterade program och användar inloggnings aktiviteter.

-   Granskningsloggar – Ger spårbarhet via loggar för alla ändringar som gjorts via olika funktioner i Azure AD. Exempel på gransknings loggar är ändringar som har gjorts i alla resurser i Azure AD, till exempel att lägga till eller ta bort användare, appar, grupper, roller och principer.

-   Riskfyllda inloggningar – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare.

-   Användare som har flaggats för risk – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats.

Azure Security Center kan även Varna vid vissa misstänkta aktiviteter, till exempel ett högt antal misslyckade autentiseringsförsök och föråldrade konton i prenumerationen. Utöver den grundläggande övervakningen av säkerhetsövervakning kan Azure Security Centerens skydds modul även samla in mer djupgående säkerhets aviseringar från enskilda Azure Compute-resurser (till exempel virtuella datorer, behållare, App Service), data resurser (till exempel SQL DB och lagring) och Azure Service-lager. Med den här funktionen kan du Visa konto avvikelser i de enskilda resurserna.

- [Resurs loggar för Azure skydds](diagnostic-logs.md)

- [Granska aktivitets rapporter i Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Aktivera Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Skydd mot hot i Azure Security Center](/azure/security-center/threat-protection)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Aktivera loggning för Azure nätverks aktiviteter

**Vägledning**: Aktivera resurs loggar för Azure-skydds, Använd dessa diagnostikloggar för att visa vilka användare som är anslutna till vilka arbets belastningar, vid vilken tidpunkt, och annan relevant loggnings information. Användare kan konfigurera dessa loggar så att de skickas till ett lagrings konto för långsiktig kvarhållning och granskning.

Aktivera och samla in resurs loggar för nätverks säkerhets grupper (NSG) och NSG flödes loggar i de nätverks säkerhets grupper som tillämpas på de virtuella nätverken som du har distribuerat din Azure skydds-resurs. Dessa loggar kan sedan användas för att analysera nätverks säkerheten och för att stödja incident undersökningar, hot på jakt och generering av säkerhets aviseringar. Du kan skicka flödes loggarna till en Azure Monitor Log Analytics arbets yta och sedan använda Trafikanalys för att ge insikter.

- [Aktivera och arbeta med Azure skydds-loggar](diagnostic-logs.md)

- [Så här aktiverar du flödes loggar för nätverks säkerhets grupper](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Azure Firewall-loggar och mått](../firewall/logs-and-metrics.md)

- [Så här aktiverar och använder du Trafikanalys](../network-watcher/traffic-analytics.md)

- [Övervakning med Network Watcher](../network-watcher/network-watcher-monitoring-overview.md)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Aktivera loggning för Azure-resurser

**Vägledning**: aktivitets loggar, som är automatiskt tillgängliga, innehåller alla Skriv åtgärder (placera, skicka, ta bort) för dina Azure skydds-resurser förutom Läs åtgärder (get). Aktivitets loggar kan användas för att hitta ett fel när du felsöker eller övervakar hur en användare i organisationen ändrade en resurs.

- [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Förstå loggning och olika logg typer i Azure](../azure-monitor/platform/platform-logs-overview.md)

- [Aktivera Azures resurs loggar för Azure skydds ](diagnostic-logs.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: centralisera hantering av säkerhets loggar och analys

**Vägledning**: centralisera loggning av lagring och analys för att aktivera korrelation. För varje logg källa ser du till att du har tilldelat en data ägare, åtkomst vägledning, lagrings plats, vilka verktyg som används för att bearbeta och komma åt data samt krav för data lagring.

Se till att du integrerar Azures aktivitets loggar i den centrala loggningen. Mata in loggar via Azure Monitor för att samla in säkerhets data som genererats av slut punkts enheter, nätverks resurser och andra säkerhets system. I Azure Monitor använder du Log Analytics arbets ytor för att fråga och utföra analyser och använda Azure Storage konton för långsiktig lagring.

Du kan också aktivera och publicera data till Azure Sentinel eller en SIEM från tredje part.

Många organisationer väljer att använda Azure Sentinel för "heta" data som används ofta och Azure Storage för "kall" data som används mindre ofta.

- [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurera kvarhållning av logg lagring

**Vägledning**: kontrol lera att alla lagrings konton eller Log Analytics arbets ytor som används för att lagra Azure skydds-loggar har den logg kvarhållningsperiod som anges enligt organisationens regler för efterlevnad.

I Azure Monitor kan du Log Analytics ange arbets ytans lagrings period enligt organisationens regler för efterlevnad.

- [Så här konfigurerar du Log Analytics bevarande period för arbets ytor](../azure-monitor/platform/manage-cost-storage.md)

- [Lagra resurs loggar i ett Azure Storage konto](/azure/azure-monitor/platform/resource-logs-collect-storage)

- [Aktivera och arbeta med Azure topologier-loggar](diagnostic-logs.md)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security benchmark: incident svar](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: förberedelse – uppdatera incident svars process för Azure

**Vägledning**: se till att din organisation har processer som svarar på säkerhets incidenter, har uppdaterat de här processerna för Azure och utnyttjar dem regelbundet för att säkerställa beredskap.

- [Implementera säkerhet i företags miljön](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Referens guide för incident svar](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: förberedelse – installations incident meddelande

**Vägledning**: Konfigurera säkerhets incident kontakt information i Azure Security Center. Den här kontakt informationen används av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har öppnats av en olagligt eller obehörig part. Du har också alternativ för att anpassa incident aviseringar och meddelanden i olika Azure-tjänster baserat på dina incident svars behov. 

- [Så här ställer du in Azure Security Center säkerhets kontakt](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: identifiering och analys – skapa incidenter baserat på aviseringar med hög kvalitet

**Vägledning**: se till att du har en process för att skapa aviseringar med hög kvalitet och mäta kvaliteten på aviseringar. På så sätt kan du lära dig lektioner från tidigare incidenter och prioritera aviseringar för analytiker, så att de inte slösar tid på falska positiva identifieringar.

Aviseringar med hög kvalitet kan byggas utifrån erfarenhet från tidigare incidenter, validerade community-källor och verktyg som utformats för att skapa och rensa aviseringar från fusing och korrelera olika signal källor. 

Azure Security Center ger aviseringar med hög kvalitet över flera Azure-tillgångar. Du kan använda ASC data Connector för att strömma aviseringarna till Azure Sentinel. Med Azure Sentinel kan du skapa avancerade aviserings regler för att generera incidenter automatiskt för en undersökning. 

Exportera dina Azure Security Center-aviseringar och rekommendationer med hjälp av export funktionen för att identifiera risker med Azure-resurser. Exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö.

- [Så här konfigurerar du export](../security-center/continuous-export.md)

- [Strömma aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: identifiering och analys – Undersök en incident

**Vägledning**: se till att analytiker kan fråga och använda olika data källor när de undersöker potentiella incidenter, för att bygga en fullständig vy över vad som hände. Diverse loggar bör samlas in för att spåra aktiviteter för en potentiell angripare över Kill-kedjan för att undvika att fläckar.  Du bör också se till att insikter och kunskaper fångas upp för andra analytiker och för framtida historiska referenser.  

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

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: identifiering och analys – prioritera incidenter

**Vägledning**: Tillhandahåll sammanhang för att analysera vilka incidenter som ska fokusera på den första baserat på aviseringens allvarlighets grad och till gångens känslighet. 

Azure Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center är i sökningen eller analysen som används för att utfärda aviseringen, samt den konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Markera dessutom resurser med taggar och skapa ett namngivnings system för att identifiera och kategorisera Azure-resurser, särskilt för bearbetning av känsliga data.  Det är ditt ansvar att prioritera reparationen av aviseringar baserat på allvarlighets graden för de Azure-resurser och den miljö där incidenten inträffade.

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Använda taggar för att organisera dina Azure-resurser](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: inne slutning, utrotning och återställning – automatisera incident hanteringen

**Vägledning**: automatisera manuella återkommande uppgifter för att påskynda svars tiden och minska belastningen på analytiker. Manuella uppgifter tar längre tid att köra, vilket saktar ned varje incident och minskar hur många incidenter en analytiker kan hantera. Manuella uppgifter ökar också analytikerns utmattning, vilket ökar risken för mänskligt fel som orsakar fördröjningar och minskar risken för att analytikerna ska kunna fokusera på komplexa uppgifter på ett effektivt sätt. Använd automatiserings funktioner för arbets flöde i Azure Security Center och Azure Sentinel för att automatiskt utlösa åtgärder eller köra en Spelbok för att svara på inkommande säkerhets aviseringar. Spelbok tar åtgärder, till exempel att skicka meddelanden, inaktivera konton och isolera problematiska nätverk. 

- [Konfigurera automatisering av arbets flöde i Security Center](../security-center/workflow-automation.md)

- [Konfigurera automatiska hot svar i Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Konfigurera automatiska hot svar i Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

## <a name="posture-and-vulnerability-management"></a>Position och sårbarhets hantering

*Mer information finns i [Azure Security benchmark: position and sårbarhet Management](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: upprätta säkra konfigurationer för Azure-tjänster 

**Vägledning**: definiera och implementera standardinställda säkerhetskonfigurationer för Azure skydds med Azure policy. Använd Azure Policy alias i namn området "Microsoft. Network" för att skapa anpassade principer för att granska eller tillämpa nätverks konfigurationen för Azure-skydds. Kunder kan också upprätta säkra konfigurationer genom att använda Azure-ritningar eller ARM-mallar för att distribuera skydds-resurser på ett säkert och konsekvent sätt.

- [Visa tillgängliga Azure Policy alias](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Så här konfigurerar och hanterar du Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Lär dig mer om ARM-mallar](../azure-resource-manager/templates/overview.md)

- [Översikt om Azure-ritningar](../governance/blueprints/overview.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: upprätthålla säkra konfigurationer för Azure-tjänster

**Vägledning**: definiera och implementera standardinställda säkerhetskonfigurationer för Azure skydds med Azure policy. Använd Azure Policy alias i namn området "Microsoft. Network" för att skapa anpassade principer för att granska eller tillämpa nätverks konfigurationen för dina skydds-resurser.

- [Visa tillgängliga Azure Policy alias](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Så här konfigurerar och hanterar du Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: utför regelbunden attack simulering

**Vägledning**: vid behov kan du genomföra inträngande tester eller röda team aktiviteter på dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister.

Följ test reglerna för Microsoft Cloud inträngande för att se till att dina inträngande tester inte strider mot Microsofts principer. Använd Microsofts strategi och körning av röda team indelning och inträngande av direktsända webbplatser mot Microsoft-hanterad moln infrastruktur, tjänster och program.

- [Inträngande testning i Azure](../security/fundamentals/pen-testing.md)

- [Inträngande test regler för engagemang](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud röd team indelning](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: delat

## <a name="governance-and-strategy"></a>Styrning och strategi

*Mer information finns i [Azure Security benchmark: styrning och strategi](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: definiera strategi för till gångs hantering och data skydd 

**Vägledning**: se till att dokumentera och förmedla en tydlig strategi för kontinuerlig övervakning och skydd av system och data. Prioritera identifiering, utvärdering, skydd och övervakning av affärs kritiska data och system. 

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

-   Data klassificerings standard i enlighet med affärs riskerna

-   Insyn i säkerhets organisationen för risker och till gångs inventering 

-   Godkännande av säkerhets organisation för Azure-tjänster för användning 

-   Säkerhet för till gångar via deras livs cykel

-   Nödvändig åtkomst kontroll strategi i enlighet med organisationens data klassificering

-   Användning av data skydds funktioner i Azure Native och tredje part

-   Krav på data kryptering för överförings-och rest-användnings fall

-   Lämpliga kryptografiska standarder

Mer information finns i följande referenser:
- [Rekommendation för Azure Security Architecture – lagring, data och kryptering](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Grundläggande Azure-säkerhet – Azure Data Security, kryptering och lagring](../security/fundamentals/encryption-overview.md)

- [Ramverk för moln införande – metod tips för Azure Data Security och kryptering](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Hantering av Azure Security-inventarier](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Azure Security benchmark – data skydd](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: definiera strategi för företags segmentering 

**Vägledning**: upprätta en företagsomfattande strategi för att segmentera åtkomst till till gångar med en kombination av identitet, nätverk, program, prenumeration, hanterings grupp och andra kontroller.

Du bör noggrant balansera behovet av säkerhets separation med behovet av att möjliggöra daglig drift av de system som behöver kommunicera med varandra och komma åt data.

Se till att segmenterings strategin implementeras konsekvent över kontroll typer, inklusive nätverks säkerhet, identitets-och åtkomst modeller samt program behörighet/åtkomst modeller och kontroller för mänsklig process.

- [Vägledning om segment strategi i Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Vägledning om segment strategi i Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Justera nätverks segmentering med strategi för företags segmentering](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: definiera säkerhets position hanterings strategi

**Vägledning**: ständigt mäta och minimera risker för enskilda till gångar och miljön som de finns i. Prioritera höga värde till gångar och högexponerade attack ytor, till exempel publicerade program, ingångs-och utgångs punkter, användar-och administratörs slut punkter osv.

- [Azure Security benchmark – position och sårbarhets hantering](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: justera organisations roller, ansvars områden och Accountabilities

**Vägledning**: se till att dokumentera och förmedla en tydlig strategi för roller och ansvar i din säkerhets organisation. Prioritera att tillhandahålla tydliga ansvar för säkerhets beslut, utbilda alla på den delade ansvars modellen och utbilda tekniska team om teknik för att skydda molnet.

- [Bästa praxis för Azure-säkerhet 1 – personer: utbilda team om molnets säkerhets resa](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Bästa praxis för Azure-säkerhet 2 – personer: utbilda team om moln säkerhets teknik](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Bästa praxis för Azure Security 3 – process: tilldela ansvar för moln säkerhets beslut](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="gs-5-define-network-security-strategy"></a>GS-5: definiera nätverks säkerhets strategi

**Vägledning**: upprätta en Azure Network Security-metod som en del av din organisations övergripande strategi för säkerhets åtkomst kontroll.  

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

-   Centraliserad nätverks hantering och säkerhets ansvar

-   Modell för virtuella nätverks segment justerade med strategin för företags segmentering

-   Reparations strategi i olika hot och angrepps scenarier

-   Strategi för Internet Edge och inkommande och utgående

-   Hybrid moln och lokal strategi för samanslutning

-   Aktuella nätverks säkerhets artefakter (t. ex. nätverks diagram, referens nätverks arkitektur)

Mer information finns i följande referenser:
- [Azure-säkerhet Best Practice 11-Architecture. Enhetlig säkerhets strategi](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Säkerhet för Azure-säkerhet](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Översikt över Azure Network Security](../security/fundamentals/network-overview.md)

- [Arkitektur strategi för företags nätverk](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: definiera identitets-och privilegie rad åtkomst strategi

**Vägledning**: upprätta en Azure Identity-och Privileged Access-metod som en del av din organisations övergripande strategi för säkerhets åtkomst kontroll.  

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

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: definiera strategi för loggning och hot svar

**Vägledning**: upprätta en strategi för loggning och hot svar för att snabbt upptäcka och åtgärda hot samtidigt som de uppfyller kraven för efterlevnad. Prioritera att ge analytiker med högkvalitativa varningar och sömlösa upplevelser så att de kan fokusera på hot i stället för integrering och manuella steg. 

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

-   Organisationens roll och ansvars områden för säkerhets åtgärder (SecOps) 

-   En väldefinierad incident svars process som justeras med NIST eller något annat bransch ramverk 

-   Insamling och kvarhållning av loggar för att stödja hot identifiering, incident svar och krav på efterlevnad

-   Centraliserad synlighet och korrelations information om hot, med SIEM, interna Azure-funktioner och andra källor 

-   Kommunikation och meddelande plan med dina kunder, leverantörer och offentliga intressen

-   Användning av inbyggda Azure-och tredjeparts-plattformar för incident hantering, till exempel loggning och hot identifiering, data utredning och angrepp och utrotning

-   Processer för hantering av incidenter och aktiviteter efter incidenter, till exempel lektioner som lärts och underlags kvarhållning

Mer information finns i följande referenser:

- [Azure-säkerhet för benchmark-loggning och hot identifiering](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure-säkerhet benchmark – incident svar](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure Security Best Practice 4 – process. Uppdatera incident svars processer för molnet](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Besluts guide för Azure införande Framework, loggning och rapportering](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure Enterprise Scale, Management och Monitoring](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="next-steps"></a>Nästa steg

- Se [Översikt över Azure Security benchmark v2](/azure/security/benchmarks/overview)
- Läs mer om [Azures säkerhets bas linjer](/azure/security/benchmarks/security-baselines-overview)
