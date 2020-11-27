---
title: Azures säkerhets bas linje för Azure Lighthouse
description: Azure Lighthouse Security-bas linjen ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: lighthouse
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: acc98cd2a724abc779954a5f22c73a5a7c6b9db4
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302443"
---
# <a name="azure-security-baseline-for-azure-lighthouse"></a>Azures säkerhets bas linje för Azure Lighthouse

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark-version 2,0](../security/benchmarks/overview.md) till Azure Lighthouse. Benchmark för Azure-säkerhet innehåller rekommendationer för hur du kan skydda dina molnlösningar på Azure. Innehållet grupperas efter de **säkerhets kontroller** som definieras av Azures säkerhets benchmark och relaterade rikt linjer för Azure Lighthouse. **Kontroller** som inte gäller för Azure-Lighthouse har uteslutits.

Om du vill se hur Azure-Lighthouse helt mappar till Azures säkerhets benchmark kan du läsa mer i den [fullständiga mappnings filen för Azure Lighthouse Security](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="identity-management"></a>Identitetshantering

*Mer information finns i [Benchmark för Azure-säkerhet: Identitetshantering](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardisera Azure Active Directory som centralt identitets- och autentiseringssystem

**Vägledning**: Azure Lighthouse använder Azure Active Directory (Azure AD) som standard tjänst för identitets-och åtkomst hantering. Standardisera Azure AD för att styra organisationens identitets-och åtkomst hantering i:
- Microsoft Cloud resurser, till exempel Azure Portal, Azure Storage, virtuell Azure-dator (Linux och Windows), Azure Key Vault, PaaS och SaaS program.
- Organisationens resurser, till exempel program i Azure eller företagets nätverksresurser.

Med Azure Lighthouse har angivna användare i en hanterings klient en inbyggd Azure-roll som gör att de kan komma åt delegerade prenumerationer och/eller resurs grupper i en kunds klient organisation. Alla inbyggda roller stöds för närvarande förutom ägare eller inbyggda roller med DataActions-behörighet. Rollen administratör för användar åtkomst stöds endast för begränsad användning i tilldela roller till hanterade identiteter. Administratörs roller för anpassade roller och klassiska prenumerationer stöds inte.

- [Innehav i Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Så skapar och konfigurerar du en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Använda externa identitets leverantörer för program](/azure/active-directory/b2b/identity-providers) 

- [Vad är säkerhets poängen för identiteter i Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: hantera program identiteter säkert och automatiskt

**Vägledning**: Azure Managed identiteter kan autentiseras för Azure-tjänster och-resurser som stöder Azure AD-autentisering. Autentisering aktive ras via fördefinierade regler för åtkomst beviljande, Undvik hårdkodade autentiseringsuppgifter i källkod eller konfigurationsfiler. Med Azure Lighthouse kan användare med rollen administratör för användar åtkomst på en kunds prenumeration skapa en hanterad identitet i den kundens klient organisation. Även om den här rollen inte i allmänhet stöds med Azure-Lighthouse, kan den användas i det här scenariot, så att användare med den här behörigheten kan tilldela en eller flera angivna inbyggda roller till hanterade identiteter.

För tjänster som inte har stöd för hanterade identiteter använder du Azure AD för att skapa ett huvud namn för tjänsten med begränsade behörigheter på resurs nivå i stället. Med Azure Lighthouse får tjänstens huvud namn åtkomst till kund resurser enligt de roller som de har beviljats under onboarding-processen. Vi rekommenderar att du konfigurerar tjänstens huvud namn med autentiseringsuppgifter för certifikatet och att de ska återgå till klient hemligheter. I båda fallen kan Azure Key Vault användas tillsammans med Azure Managed identiteter, så att körnings miljön (till exempel en Azure-funktion) kan hämta autentiseringsuppgiften från nyckel valvet.

- [Hanterade Azure-identiteter](../active-directory/managed-identities-azure-resources/overview.md)

- [Huvudnamn för Azure-tjänsten](/powershell/azure/create-azure-service-principal-azureps)

- [Använd Azure Key Vault för registrering av säkerhets objekt](../key-vault/general/authentication.md#authorize-a-security-principal-to-access-key-vault)

- [Skapa en användare som kan tilldela roller till en hanterad identitet i en kund klient organisation](how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Använda starka autentiseringskontroller för all Azure Active Directory-baserad åtkomst

**Vägledning**: Kräv Multi-Factor Authentication (MFA) för alla användare i hanterings klienten, inklusive användare som kommer att ha åtkomst till delegerade kund resurser. Vi rekommenderar att du ber dina kunder att implementera MFA i sina klienter också.

- [Så här aktiverar du MFA i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Övervaka och varna vid kontoavvikelser

**Vägledning**: Azure AD tillhandahåller följande data Källor: 

-   Inloggningsaktiviteter – Rapporten för inloggningsaktiviteter ger information om användningen av hanterade program och användares inloggningsaktiviteter.

-   Gransknings loggar – ger spårning genom loggar för alla ändringar som görs via olika funktioner i Azure AD. Exempel på gransknings loggar för loggade ändringar omfattar att lägga till eller ta bort användare, appar, grupper, roller och principer.

-   Riskfyllda inloggningar – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare.

-   Användare som har flaggats för risk – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats.

Dessa data källor kan integreras med Azure Monitor, Azure Sentinel eller SIEM system från tredje part.

Tjänst leverantörer som använder Azure Lighthouse kan vidarebefordra Azure AD-loggar till Azure Sentinel och Visa/ange aviseringar över klienter för att övervaka och Varna vid konto avvikelser.

- [Granska aktivitets rapporter i Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Så visar du riskfyllda inloggningar för Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Hantera Azure Sentinel-arbetsytor i skala](how-to/manage-sentinel-workspaces.md)

- [Anslut data från Azure AD till Azure Sentinel](../sentinel/connect-azure-active-directory.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: begränsa åtkomsten till Azure-resursen baserat på villkor

**Vägledning**: Azure Lighthouse har inte stöd för en villkorlig åtkomst funktion för delegerade kund resurser. I hantering av klient organisation använder du villkorlig åtkomst för Azure AD för mer detaljerad åtkomst kontroll baserat på användardefinierade villkor, t. ex. att kräva användar inloggningar från vissa IP-intervall för att använda Multi-Factor Authentication (MFA). En detaljerad hantering av autentiseringsbegäranden kan också användas via en princip för villkorlig åtkomst i Azure AD för olika användnings fall. 

Du bör kräva MFA för alla användare i din hanterings klient, inklusive användare som kommer att ha åtkomst till delegerade kund resurser. Vi rekommenderar att du ber dina kunder att implementera MFA i sina klienter också.

- [Översikt över villkorlig Azure-åtkomst](../active-directory/conditional-access/overview.md)

- [Vanliga principer för villkorlig åtkomst](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Konfigurera autentiseringsessionshantering med villkorsstyrd åtkomst](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

## <a name="privileged-access"></a>Privilegierad åtkomst

*Mer information finns i [Benchmark för Azure-säkerhet: Privilegierad åtkomst](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Skydda och begränsa användare med höga privilegier

**Vägledning**: begränsa antalet starkt privilegierade användar konton och skydda dessa konton på en utökad nivå. Det krävs inget globalt administratörs konto för att aktivera och använda Azure-Lighthouse.

För att få åtkomst till aktivitets logg data på klient nivå måste ett konto tilldelas den inbyggda rollen för övervaknings läsaren i rot omfånget (/). Eftersom övervaknings läsar rollen i rot omfånget är en bred åtkomst nivå rekommenderar vi att du tilldelar rollen till ett huvud konto för tjänsten i stället för till en enskild användare eller till en grupp. Den här tilldelningen måste utföras av en användare som har rollen global administratör med ytterligare utökad åtkomst. Den här utökade åtkomsten ska läggas till omedelbart innan du gör roll tilldelningen och sedan ta bort den när tilldelningen är klar.

- [Behörigheter för administratörsrollen i Azure AD](/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

- [Tilldela åtkomst för att övervaka aktivitets logg data på klient nivå](how-to/monitor-delegation-changes.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: begränsa administrativ åtkomst till verksamhets kritiska system

**Vägledning**: Azure Lighthouse använder Azures rollbaserad åtkomst kontroll (Azure RBAC) för att isolera åtkomst till affärs kritiska system genom att begränsa vilka konton som har privilegie rad åtkomst till de prenumerationer och hanterings grupper som de finns i.

Se till att du följer principen om minsta behörighet så att användarna bara har de behörigheter som krävs för att utföra sina uppgifter.

Se till att du också begränsar åtkomsten till hanterings-, identitets-och säkerhets system som har administrativ åtkomst till din affärs kritiska åtkomst, till exempel Active Directory-domän controllers (DCs), säkerhets verktyg och system hanterings verktyg med agenter som är installerade på affärs kritiska system. Angripare som angriper dessa hanterings-och säkerhets system kan omedelbart weaponize dem för att kompromettera affärs kritiska till gångar.

Alla typer av åtkomst kontroller bör justeras mot din strategi för företags segmentering för att säkerställa en konsekvent åtkomst kontroll.

- [Azure-komponenter och referens modell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Åtkomst till hanterings grupp](../governance/management-groups/overview.md#management-group-access)

- [Administratörer för Azure-prenumeration](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [Metod tips för att definiera användare och roller för Azure Lighthouse](concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Granska och stäm av användaråtkomsten regelbundet

**Vägledning**: Azure Lighthouse använder Azure Active Directory-konton (Azure AD) för att hantera sina resurser, granska användar konton och få åtkomst till tilldelning regelbundet för att säkerställa att kontona och deras åtkomst är giltiga. Du kan använda åtkomst granskningar i Azure AD för att granska grupp medlemskap, åtkomst till företags program och roll tilldelningar. Azure AD repor ting kan tillhandahålla loggar för att identifiera inaktuella konton. Du kan också använda Azure AD Privileged Identity Management för att skapa åtkomst gransknings rapportens arbets flöde för att under lätta gransknings processen.

Kunderna kan granska åtkomst nivån som beviljas användare i hanterings klienten via Azure Lighthouse i Azure Portal. De kan när som helst ta bort den här åtkomsten.

Dessutom kan Azure Privileged Identity Management också konfigureras för att varna när ett högt antal administratörs konton skapas och för att identifiera administratörs konton som är inaktuella eller felaktigt konfigurerade.

Observera: att vissa Azure-tjänster har stöd för lokala användare och roller som inte hanteras via Azure AD. Du måste hantera dessa användare separat.

- [Skapa en åtkomst granskning av Azure Resource roles i Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Ta bort åtkomst till en delegering](how-to/remove-delegation.md)

- [Så här använder du identitets- och åtkomstgranskningar i Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Visa sidan tjänst leverantörer i Azure Portal](how-to/view-manage-service-providers.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Konfigurera nöd åtkomst i Azure AD

**Vägledning**: Azure Lighthouse är integrerat med Azure Active Directory för att hantera sina resurser. För att förhindra oavsiktligt låst av din Azure AD-organisation konfigurerar du ett konto för nöd åtkomst för åtkomst när normala administrativa konton inte kan användas. Konton för nöd åtkomst är ofta mycket privilegierade och de bör inte tilldelas vissa individer. Konton för nöd åtkomst är begränsade till nöd situationer eller "Break glas"-scenarier där normala administrativa konton inte kan användas.

Du bör se till att autentiseringsuppgifterna (till exempel lösen ord, certifikat eller smartkort) för konton för nöd åtkomst hålls skyddade och endast kända för personer som har behörighet att använda dem endast i nödfall.

- [Hantera konton för nöd åtkomst i Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatisera rättighets hantering 

**Vägledning**: Azure Lighthouse är integrerat med Azure Active Directory (Azure AD) för att hantera sina resurser. Använd funktioner för hantering av Azure AD-rättighet för att automatisera åtkomst till begär ande arbets flöden, inklusive åtkomst tilldelningar, recensioner och förfallo datum. Det finns också stöd för godkännande med dubbla eller flera steg.

- [Vad är åtkomst granskningar för Azure AD](../active-directory/governance/access-reviews-overview.md) 

- [Vad är hantering av Azure AD-berättigande](../active-directory/governance/entitlement-management-overview.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Använd arbets stationer med privilegie rad åtkomst

**Vägledning**: skyddade, isolerade arbets stationer är mycket viktiga för att skydda känsliga roller som administratörer, utvecklare och kritiska tjänst operatörer. Beroende på dina behov kan du använda mycket säkra användar arbets stationer och/eller Azure-skydds för att utföra administrativa uppgifter med Azure Lighthouse i produktions miljöer. Använd Azure Active Directory, Microsoft Defender Avancerat skydd (ATP) och/eller Microsoft Intune för att distribuera en säker och hanterad användar arbets station för administrativa uppgifter. De skyddade arbets stationerna kan hanteras centralt för att upprätthålla säker konfiguration, inklusive stark autentisering, program vara och maskin varu bas linjer och begränsad logisk och nätverks åtkomst. 

- [Förstå arbets stationer med privilegie rad åtkomst](../active-directory/devices/concept-azure-managed-workstation.md)

- [Distribuera en privilegie rad åtkomst arbets Station](../active-directory/devices/howto-azure-managed-workstation.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Följ JEA (Just Enough Administration, precis tillräcklig administration) (principen om minsta behörighet) 

**Vägledning**: Azure Lighthouse är integrerat med rollbaserad åtkomst kontroll (RBAC) i Azure för att hantera dess resurser. Med Azure RBAC kan du hantera åtkomst till Azure-resurser via rolltilldelningar. Du kan tilldela de här inbyggda rollerna till användare, grupper, tjänstens huvud namn och hanterade identiteter. Det finns fördefinierade inbyggda roller för vissa resurser och dessa roller kan inventeras eller frågas via verktyg som Azure CLI, Azure PowerShell eller Azure-portalen. De behörigheter som du tilldelar resurser via Azure RBAC bör alltid vara begränsade till vad som krävs av rollerna. Det här är ett komplement till JIT-metoden (just in time) för Azure AD Privileged Identity Management (PIM) och bör granskas regelbundet. Använd inbyggda roller för att allokera behörighet och endast skapa anpassade roller vid behov.

Med Azure-Lighthouse får du till gång till delegerade kund resurser med hjälp av inbyggda Azure-roller. I de flesta fall vill du tilldela dessa roller till en grupp eller tjänstens huvud namn i stället för till många enskilda användar konton. På så sätt kan du lägga till eller ta bort åtkomst för enskilda användare utan att behöva uppdatera och publicera om planen när dina åtkomst krav ändras.

För att delegera kund resurser till en hanterings klient måste en distribution göras av ett konto som inte är gäst i kundens klient organisation som har den inbyggda rollen för den prenumeration som har registrerats (eller som innehåller de resurs grupper som har registrerats).

- [Förstå klienter, användare och roller i Azure Lighthouse](concepts/tenants-users-roles.md)

- [Så här tillämpar du principen om minsta behörighet för Azure-Lighthouse](concepts/recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege)

- [Vad är Azure rollbaserad åtkomst kontroll (Azure RBAC)](../role-based-access-control/overview.md) 

- [Så här använder du identitets- och åtkomstgranskningar i Azure AD](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="asset-management"></a>Tillgångshantering

*Mer information finns i [Benchmark för Azure-säkerhet: Tillgångshantering](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Se till att säkerhetsteamet har insyn i risker för tillgångar

**Riktlinjer**: Se till att säkerhetsteamen beviljas behörigheter för säkerhetsläsare i din Azure-klient och prenumerationer som gör att de kan övervaka säkerhetsrisker med hjälp av Azure Security Center. 

Beroende på hur säkerhetsteamets ansvarsområden är strukturerade kan ansvaret för övervakning av säkerhetsrisker ligga hos en central säkerhetsgrupp eller ett lokalt team. Detta innebär att säkerhetsinsikter och -risker alltid måste samlas centralt inom en organisation. 

Behörigheter för säkerhetsläsare kan tillämpas brett över en hel klientorganisation (rothanteringsgrupp) eller omfattas av hanteringsgrupper eller specifika prenumerationer. 

Obs! Ytterligare behörigheter kan krävas för att få insyn i arbetsbelastningar och tjänster. 

- [Översikt över säkerhetsläsarrollen](../role-based-access-control/built-in-roles.md#security-reader)

- [Översikt över Azure-hanteringsgrupper](../governance/management-groups/overview.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: se till att säkerhets teamet har åtkomst till till gångs inventering och metadata

**Vägledning**: kundernas säkerhets team kan granska aktivitets loggar för att se aktivitet som tas av tjänst leverantörer som använder Azure Lighthouse. 

Om en tjänst leverantör vill att deras säkerhets team ska kunna granska delegerade kund resurser bör säkerhets teamets auktorisering innehålla den inbyggda rollen läsare.

- [Hur en kund kan granska service providerns aktivitet](how-to/view-service-provider-activity.md)

- [Ange roller när du registrerar en kund i Azure Lighthouse](how-to/onboard-customer.md#define-roles-and-permissions)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att granska och begränsa vilka tjänster som användare kan etablera i din miljö. Använd Azure Resource Graph för att fråga efter och identifiera resurser i prenumerationerna. Du kan också använda Azure Monitor för att skapa regler för att utlösa aviseringar när en icke-godkänd tjänst upptäcks.

- [Så här konfigurerar och hanterar du Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Så här nekar du en speciell resurs typ med Azure Policy](../governance/policy/samples/built-in-policies.md#general) 

- [Så här skapar du frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Garantera säker livscykelhantering för tillgångar

**Vägledning**: ta bort åtkomst till resurser som har delegerats via Azure-Lighthouse när de inte längre behövs, så att tjänste leverantörer inte längre har åtkomst. Åtkomst kan tas bort av antingen kunden eller av tjänst leverantören. 

- [Ta bort åtkomst till en delegering](how-to/remove-delegation.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Azure Lighthouse är integrerat med Azure Active Directory (Azure AD) för identitet och autentisering. Du kan använda villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azures resurser Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azures resurs hanterare](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="logging-and-threat-detection"></a>Loggning och hotidentifiering

*Mer information finns i [Benchmark för Azure-säkerhet: Loggning och hotidentifiering](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Aktivera hot identifiering för Azure-resurser

**Vägledning**: via Azure Lighthouse kan du övervaka dina kunders Azure-resurser för potentiella hot och avvikelser. Fokusera på att få aviseringar med hög kvalitet för att minska antalet falska positiva identifieringar för analytiker som ska sorteras. Aviseringar kan hämtas från loggdata, agenter eller andra data.

Använd Azure Security Center inbyggd hot identifierings funktion, som baseras på övervakning av Azure-tjänsten telemetri och analys av tjänst loggar. Data samlas in med hjälp av Log Analytics agent, som läser olika säkerhetsrelaterade konfigurationer och händelse loggar från systemet och kopierar data till din arbets yta för analys. 

Dessutom kan du använda Azure Sentinel för att bygga analys regler, som rör hot som matchar specifika kriterier i kundens miljö. Reglerna genererar incidenter när villkoren matchas, så att du kan undersöka varje incident. Azure Sentinel kan också importera Hot information från tredje part för att förbättra sin funktion för hot identifiering. 

- [Skydd mot hot i Azure Security Center](/azure/security-center/threat-protection)

- [Referens guide för Azure Security Center säkerhets varningar](../security-center/alerts-reference.md)

- [Skapa anpassade analys regler för att identifiera hot](../sentinel/tutorial-detect-threats-custom.md)

- [Hantera Azure Sentinel-arbetsytor i skala](how-to/manage-sentinel-workspaces.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Aktivera hotidentifiering för identitets- och åtkomsthantering i Azure

**Vägledning**: via Azure Lighthouse kan du använda Azure Security Center för att varna om vissa misstänkta aktiviteter i de kund innehavare som du hanterar, till exempel ett högt antal misslyckade autentiseringsförsök och föråldrade konton i prenumerationen.

Azure Active Directory (Azure AD) innehåller följande användar loggar som kan visas i Azure AD repor ting eller integreras med Azure Monitor, Azure Sentinel eller andra SIEM/övervaknings verktyg för mer avancerade övervaknings-och analys användnings fall:
- Inloggning – inloggnings rapporten innehåller information om användningen av hanterade program och användar inloggnings aktiviteter.
- Granskningsloggar – Ger spårbarhet via loggar för alla ändringar som gjorts via olika funktioner i Azure AD. Exempel på granskningsloggar är de resursändringar som görs i Azure AD, som att lägga till eller ta bort användare, appar, grupper, roller och principer.
- Riskfylld inloggning – en riskfylld inloggning är en indikator för ett inloggnings försök som kan ha utförts av någon som inte är den legitima ägaren till ett användar konto.
- Användare som har flaggats för risk – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats.

Azure Security Center kan också avisera om vissa misstänkta aktiviteter, till exempel ett högt antal misslyckade autentiseringsförsök eller föråldrade konton i prenumerationen. Utöver den grundläggande övervakningen av säkerhetsrutiner kan hotskyddsmodulen i Azure Security Center även samla in mer detaljerade säkerhetsaviseringar från enskilda Azure Compute-resurser (virtuella datorer, containrar, App Service), data resurser (SQL DB och lagring) och lager i Azure-tjänsten. Den här funktionen ger insyn i konto avvikelser i de enskilda resurserna. 

- [Förbättrade tjänster och scenarier med Azure Lighthouse](concepts/cross-tenant-management-experience.md#enhanced-services-and-scenarios)

- [Granska aktivitetsrapporter i Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Aktivera Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) 

- [Skydd mot hot i Azure Security Center](/azure/security-center/threat-protection)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Aktivera loggning för Azure-resurser

**Vägledning**: aktivitets loggar, som är automatiskt tillgängliga, innehåller alla Skriv åtgärder (placera, skicka, ta bort) för dina Azure Lighthouse-resurser förutom Läs åtgärder (get). Aktivitets loggar kan användas för att hitta ett fel när du felsöker eller övervakar hur en användare i organisationen ändrade en resurs.

Med Azure Lighthouse kan du använda Azure Monitor loggar på ett skalbart sätt över de kund klienter som du hanterar. Skapa Log Analytics arbets ytor direkt i kundens klient organisation så att kunddata behålls i sina klienter i stället för att exporteras till dina kunder. Det möjliggör också centraliserad övervakning av resurser eller tjänster som stöds av Log Analytics, vilket ger dig mer flexibilitet för vilka typer av data du övervakar.

Kunder som har delegerade prenumerationer för Azure Lighthouse kan visa Azures aktivitets logg data för att se alla åtgärder som vidtagits. Detta ger kunderna full insyn i åtgärder som tjänst leverantörer utför, tillsammans med åtgärder som utförs av användare i kundens egna Azure Active Directory (Azure AD)-klient.

- [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Förstå loggning och olika logg typer i Azure](../azure-monitor/platform/platform-logs-overview.md)

- [Övervaka delegerade resurser i stor skala](how-to/monitor-at-scale.md)

- [Visa tjänstleverantörsaktivitet](how-to/view-service-provider-activity.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: centralisera hantering av säkerhets loggar och analys

**Vägledning**: centralisera loggning av lagring och analys för att aktivera korrelation. För varje logg källa ser du till att du har tilldelat en data ägare, åtkomst vägledning, lagrings plats, vilka verktyg som används för att bearbeta och komma åt data samt krav för data lagring.

Se till att du integrerar Azures aktivitets loggar i den centrala loggningen. Mata in loggar via Azure Monitor för att samla in säkerhets data som genererats av slut punkts enheter, nätverks resurser och andra säkerhets system. I Azure Monitor använder du Log Analytics arbets ytor för att fråga och utföra analyser och använda Azure Storage konton för långsiktig lagring.

Du kan också aktivera och publicera data till Azure Sentinel eller en SIEM från tredje part.

Med Azure Lighthouse kan du använda Azure Monitor loggar på ett skalbart sätt över de kund klienter som du hanterar. Skapa Log Analytics arbets ytor direkt i kundens klient organisation så att kunddata behålls i sina klienter i stället för att exporteras till dina kunder. Det möjliggör också centraliserad övervakning av resurser eller tjänster som stöds av Log Analytics, vilket ger dig mer flexibilitet för vilka typer av data du övervakar.

Kunder som har delegerade prenumerationer för Azure Lighthouse kan visa Azures aktivitets logg data för att se alla åtgärder som vidtagits. Detta ger kunderna full insyn i åtgärder som tjänst leverantörer utför, tillsammans med åtgärder som utförs av användare i kundens egna Azure Active Directory (Azure AD)-klient.

Många organisationer väljer att använda Azure Sentinel för "heta" data som används ofta och Azure Storage för "kall" data som används mindre ofta.

- [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Övervaka delegerade resurser i stor skala](how-to/monitor-at-scale.md)

- [Hur kunder kan visa Service Provider-aktivitet](how-to/view-service-provider-activity.md)

- [Hantera Azure Sentinel-arbetsytor i skala](how-to/manage-sentinel-workspaces.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurera kvarhållning av logg lagring

**Vägledning**: Azure Lighthouse genererar för närvarande inte några säkerhetsrelaterade loggar. Kunder som vill visa Service Provider-aktivitet kan konfigurera logg kvarhållning enligt efterlevnad, regel och affärs krav. 

I Azure Monitor kan du Log Analytics ange arbets ytans lagrings period enligt organisationens regler för efterlevnad. Använd Azure Storage-, Data Lake-eller Log Analytics arbets ytans konton för långsiktig lagring och arkivering.

- [Ändra data lagrings perioden i Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Konfigurera bevarande princip för Azure Storage konto loggar](../storage/common/storage-monitor-storage-account.md#configure-logging)

- [Azure Security Center aviseringar och rekommendationer exportera](../security-center/continuous-export.md) konfigurera och kunden kan inte ange någon logg kvarhållning.

- [Hur en kund kan granska aktivitets logg data för tjänst leverantörer](how-to/view-service-provider-activity.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Benchmark för Azure-säkerhet: Incidenthantering](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Förberedelse – uppdatera incidenthanteringsprocessen för Azure

**Riktlinjer**: Se till att din organisation har processer för att hantera säkerhetsincidenter, har uppdaterat dessa processer för Azure och utnyttjar dem regelbundet för att säkerställa beredskap.

- [Implementera säkerhet i företagsmiljön](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Referensguide för incidenthantering](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Förberedelser – konfigurera incidentmeddelande

**Riktlinjer**: Konfigurera kontaktinformation för säkerhetsincidenter i Azure Security Center. Microsoft använder kontaktinformationen för att kontakta dig om Microsoft Security Response Center (MRSC) upptäcker att en obehörig part har kommit åt dina data. Du har också alternativ för att anpassa incidentaviseringar och -meddelanden i olika Azure-tjänster baserat på dina behov av incidenthantering. 

- [Så här ställer du in säkerhetskontakt i Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Identifiering och analys – skapa incidenter baserat på aviseringar med hög kvalitet

**Vägledning**: se till att du har en process för att skapa aviseringar med hög kvalitet och mäta kvaliteten på aviseringar. På så sätt kan du dra slutsatser från tidigare incidenter och prioritera aviseringar för analytiker, så att de inte slösar tid på falska positiva identifieringar. 

Aviseringar med hög kvalitet kan byggas utifrån erfarenhet från tidigare incidenter, validerade community-källor och verktyg som utformats för att skapa och rensa aviseringar från fusing och korrelera olika signal källor. 

Azure Security Center ger aviseringar med hög kvalitet över flera Azure-tillgångar. Du kan använda anslutningsprogrammet för ASC-data för att strömma aviseringarna till Azure Sentinel. Med Azure Sentinel kan du skapa avancerade aviseringsregler för att generera incidenter automatiskt för en undersökning. 

Exportera dina Azure Security Center-aviseringar och -rekommendationer med hjälp av exportfunktionen för att identifiera risker med Azure-resurser. Exportera aviseringar och rekommendationer antingen manuellt eller löpande.

- [Så här konfigurerar du export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Identifiering och analys – undersöka en incident

**Riktlinjer**: Se till att analytiker kan fråga och använda olika datakällor när de undersöker potentiella incidenter, så att de kan skapa en fullständig överblick över vad som hände. Diverse loggar bör samlas in för att spåra en potentiell angripares aktiviteter över hela händelsekedjan för att undvika att blinda fläckar.  Du bör också se till att insikter och kunskaper fångas upp för andra analytiker och för framtida historiska referenser.  

Datakällorna för undersökningen innehåller de centraliserade loggningskällor som redan har samlats in från tjänster som omfattas och system som körs, men kan även innehålla:

- Nätverksdata – Använd nätverkssäkerhetsgruppers flödesloggar, Azure Network Watcher och Azure Monitor för att avbilda nätverksflödesloggar och annan analysinformation. 

- Ögonblicksbilder av system som körs: 

    - Använd funktionen för ögonblicksbilder på den virtuella Azure-datorn för att skapa en ögonblicksbild av det aktiva systemets disk. 

    - Använd operativsystemets interna minnesdumpningsfunktion för att skapa en ögonblicksbild av det aktiva systemets minne.

    - Använd funktionen för ögonblicksbilder i Azure-tjänsterna eller din programvaras egna funktion för att skapa ögonblicksbilder av de system som körs.

Azure Sentinel tillhandahåller omfattande dataanalyser i praktiskt taget alla loggkällor och en ärendehanteringsportal för att hantera hela livscykeln för incidenter. Affärsinformation under en undersökning kan associeras med en incident för spårning och rapportering. 

- [Ögonblicksbild av en Windows-dators disk](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Ögonblicksbild av en Linux-dators disk](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure har stöd för diagnostikinformation och insamling av minnesdumpar](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Undersöka incidenter med Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Identifiering och analys – prioritera incidenter

**Riktlinjer**: Tillhandahålla en kontext till analytiker om vilka incidenter att fokusera på först baserat på aviseringens allvarlighetsgrad och tillgångens känslighet. 

Azure Security Center tilldelar en allvarlighetsgrad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighetsgraden baseras på hur tillförlitligt Security Center är i sökandet eller på den analys som användes för att utfärda aviseringen, samt konfidensnivån att det fanns en skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Markera även resurser med taggar och skapa ett namngivningssystem för att identifiera och kategorisera Azure-resurser, i synnerhet sådana som används för bearbetning av känsliga data.  Det är ditt ansvar att prioritera åtgärdandet av aviseringar baserat på allvarlighetsgraden för de Azure-resurser och den miljö där incidenten inträffade.

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Använda taggar för att organisera dina Azure-resurser](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Inneslutning, utrotning och återställning – automatisera incidenthanteringen

**Riktlinjer**: Automatisera manuella återkommande uppgifter för att förkorta svarstiden och minska belastningen på analytiker. Manuella uppgifter tar längre tid att köra, vilket saktar ned varje incident och minskar antalet incidenter som en analytiker kan hantera. Manuella uppgifter gör dessutom analytikern mer utmattad, vilket ökar risken för mänskliga fel som i sin tur orsakar fördröjningar, samt minskar analytikernas förmåga att fokusera på komplexa uppgifter på ett effektivt sätt. Använd automatiseringsfunktioner för arbetsflöde i Azure Security Center och Azure Sentinel för att automatiskt utlösa åtgärder eller köra en spelbok för att svara på inkommande säkerhetsaviseringar. Spelboken vidtar åtgärder som att skicka meddelanden, inaktivera konton och isolera problematiska nätverk. 

- [Konfigurera arbetsflödesautomation i Security Center](../security-center/workflow-automation.md)

- [Konfigurera automatiska svar på hot i Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Konfigurera automatiska svar på hot i Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="posture-and-vulnerability-management"></a>Status- och sårbarhetshantering

*Mer information finns i [Benchmark för Azure-säkerhet: Status- och sårbarhetshantering](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: upprätta säkra konfigurationer för Azure-tjänster 

**Vägledning**: Azure Lighthouse har stöd för följande tjänstspecifika principer som är tillgängliga i Azure Security Center att granska och tillämpa konfigurationer för dina Azure-resurser. Detta kan konfigureras i Azure Security Center eller Azure Policy initiativ.

- Tillåt hantering av klient-ID: n till onboarding via Azure Lighthouse

- Granska delegering av omfattningar till en hanterings klient

Du kan använda Azure-ritningar för att automatisera distribution och konfiguration av tjänster och program miljöer, inklusive Azure Resource Manager mallar, Azure RBAC-kontroller och-principer, i en enda skiss definition.

- [Azure Lighthouse-principer](samples/policy-reference.md)

- [Självstudie – Skapa och hantera principer för att genomdriva efterlevnad](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Blueprint](../governance/blueprints/overview.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: upprätthålla säkra konfigurationer för Azure-tjänster

**Vägledning**: Azure Lighthouse har stöd för följande tjänstspecifika principer som är tillgängliga i Azure Security Center att granska och tillämpa konfigurationer för dina Azure-resurser. Detta kan konfigureras i Azure Security Center eller Azure Policy initiativ.

- [Azure Lighthouse-principer](samples/policy-reference.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: upprätta säkra konfigurationer för beräknings resurser

**Vägledning**: Använd Azure Security Center och Azure policy för att upprätta säkra konfigurationer för alla beräknings resurser, inklusive virtuella datorer, behållare och andra.

- [Så här övervakar du Azure Security Center rekommendationer](../security-center/security-center-recommendations.md) 

- [Säkerhetsrekommendationer – en referensguide](../security-center/recommendations-reference.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Genomför regelbunden attacksimulering

**Riktlinjer**: Genomför vid behov genomslagstestning eller Red Team-aktiviteter på dina Azure-resurser och se till att alla kritiska säkerhetsbrister åtgärdas.
Följ Microsoft Clouds deltagarregler för genomslagstestning för att se till att genomslagstesterna inte strider mot Microsofts principer. Använd Microsofts strategi för och verkställande av Red Team-indelning och genomslagstester för live-webbplatser mot Microsoft-hanterad molninfrastruktur, tjänster och program.

- [Genomslagstestning i Azure](../security/fundamentals/pen-testing.md)

- [Deltagarregler för genomslagstestning](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Red Team-indelning i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Delad

## <a name="governance-and-strategy"></a>Styrning och strategi

*Mer information finns i [Benchmark för Azure-säkerhet: Styrning och strategi](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definiera strategin för tillgångshantering och dataskydd 

**Riktlinjer**: Se till att dokumentera och förmedla en tydlig strategi för kontinuerlig övervakning och skydd av system och data. Prioritera upptäckt, utvärdering, skydd och övervakning av affärskritiska data och system. 

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

-   Standard för klassificering av data i enlighet med affärsriskerna

-   Insyn för säkerhetsorganisationen i risker och tillgångsinventering 

-   Säkerhetsorganisations godkännande av Azure-tjänster att använda 

-   Säkerhet för tillgångar via deras livscykel

-   Strategi för nödvändig åtkomstkontroll i enlighet med organisationens dataklassificering

-   Användning av data skydds funktioner i Azure Native och tredje part

-   Krypteringskrav för användningsfall med data under transport och i vila

-   Lämpliga kryptografiska standarder

Mer information finns i följande resurser:
- [Rekommendation för Azure Security Architecture – lagring, data och kryptering](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Grundläggande Azure-säkerhet – säkerhet, kryptering och lagring av data i Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework – bästa praxis för datasäkerhet och kryptering i Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Benchmark för Azure-säkerhet – tillgångshantering](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Benchmark för Azure-säkerhet – dataskydd](/azure/security/benchmarks/security-controls-v2-data-protection)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definiera strategi för företagssegmentering 

**Riktlinjer**: Upprätta en företagsomfattande strategi för att segmentera åtkomst till tillgångar med en kombination av identitet, nätverk, program, prenumeration, hanteringsgrupp och andra kontroller.

Du bör noggrant balansera behovet av säkerhetsseparation med behovet av att möjliggöra daglig drift av de system som behöver kommunicera med varandra och komma åt data.

Se till att segmenteringsstrategin implementeras konsekvent över olika kontrolltyper, inklusive nätverkssäkerhet, identitets- och åtkomstmodeller samt modeller för programbehörighet/-åtkomst och kontroller för mänskliga processer.

- [Vägledning om segmenteringsstrategi i Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Vägledning om segmenteringsstrategi i Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Justera nätverkssegmentering med strategi för företagssegmentering](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definiera strategi för hantering av säkerhetsstatus

**Riktlinjer**: Mät och minimera kontinuerligt risker för enskilda tillgångar och den miljö som de finns i. Prioritera tillgångar med högt värde och högexponerade attackytor, till exempel publicerade program, ingångs- och utgångspunkter för nätverk, slutpunkter för användare och administratörer osv.

- [Benchmark för Azure-säkerhet – status- och sårbarhetshantering](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Justera roller, ansvarsområden och ansvar

**Riktlinjer**: Se till att dokumentera och förmedla en tydlig strategi för roller och ansvarsområden i din säkerhetsorganisation. Prioritera att presentera tydligt ansvar för säkerhetsbeslut, utbilda alla i den delade ansvarsmodellen och utbilda tekniska team om teknik för att skydda molnet.

- [Bästa praxis för Azure-säkerhet 1 – personer: Utbilda team i resan mot molnsäkerhet](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Bästa praxis för Azure-säkerhet 2 – personer: Utbilda team i teknik för molnsäkerhet](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Bästa praxis för Azure-säkerhet 3 – process: Tilldela ansvar för beslut kring molnsäkerhet](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definiera en strategi för nätverkssäkerhet

**Riktlinjer**: Upprätta en metod för Azure-nätverkssäkerhet som en del av din organisations övergripande strategi för säker åtkomstkontroll.  

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

-   Centraliserad nätverkshantering och säkerhetsansvar

-   Modell för virtuell nätverkssegmentering i linje med företagets segmenteringsstrategi

-   Åtgärdsstrategi i olika hot- och attackscenarier

-   /Azure/Security/benchmarks/Security-Controls-v2-Logging-Threat-Protection för Internet Edge och ingående och utgående strategi
-   Strategi för hybridmoln och lokala anslutningar

-   Aktuella artefakter för nätverkssäkerhet (t.ex. nätverksdiagram, arkitektur för referensnätverk)

Mer information finns i följande resurser:
- [Bästa praxis för Azure-säkerhet 11 – arkitektur. En enhetlig säkerhetsstrategi](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Benchmark för Azure-säkerhet – nätverkssäkerhet](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Översikt över Azure-nätverkssäkerhet](../security/fundamentals/network-overview.md)

- [Arkitekturstrategi för företagsnätverk](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definiera strategi för identiteter och privilegierad åtkomst

**Riktlinjer**: Upprätta en metod för Azure-identitet och priviligierad åtkomst som en del av organisationens övergripande strategi för säker åtkomstkontroll.  

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

-   Ett centraliserat identitets- och autentiseringssystem och dess anslutningar till andra interna och externa identitetssystem

-   Starka autentiseringsmetoder i olika användnings fall och förhållanden

-   Skydd av användare med höga privilegier

-   Övervakning och hantering av avvikande användaraktiviteter  

-   Process för granskning och avstämning av användaridentitet och -åtkomst

Mer information finns i följande resurser:

- [Benchmark för Azure-säkerhet – identitetshantering](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Benchmark för Azure-säkerhet – privilegierad åtkomst](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Bästa praxis för Azure-säkerhet 11 – arkitektur. En enhetlig säkerhetsstrategi](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Säkerhetsöversikt för identitetshantering i Azure](../security/fundamentals/identity-management-overview.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definiera strategi för loggning och hothantering

**Riktlinjer**: Upprätta en strategi för loggning och hothantering för att snabbt upptäcka och åtgärda hot och samtidigt uppfylla kraven för efterlevnad. Prioritera att ge analytiker med högkvalitativa varningar och sömlösa upplevelser så att de kan fokusera på hot i stället för integrering och manuella steg. 

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

-   SecOps-organisationens roll och ansvarsområden 

-   En väldefinierad process för incidenthantering i linje med NIST eller annat branschramverk 

-   Insamling och kvarhållning av loggar för att stödja hotidentifiering, incidentsvar och krav på efterlevnad

-   Centraliserad insyn i och korrelationsuppgifter om hot med hjälp av Säkerhetsinformation och händelsehantering, inbyggda Azure-funktioner och andra källor 

-   Kommunikations- och aviseringsplan för dina kunder, leverantörer och offentliga intressenter

-   Användning av inbyggda Azure- och tredjeparts-plattformar för incidenthantering, till exempel loggning och hotidentifiering, analys och attackåtgärder och -utrotning

-   Processer för hantering av incidenter och aktiviteter efter incidenter, till lärdomar och kvarhållning av bevis

Mer information finns i följande resurser:

- [Benchmark för Azure-säkerhet – loggning och hotidentifiering](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Benchmark för Azure-säkerhet – incidenthantering](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Bästa praxis för Azure-säkerhet 4 – process. Uppdatera incidenthanteringsprocesser för molnet](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Beslutsguide för ramverk för Azure-införande, loggning och rapportering](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure i företagsskala, hantering och övervakning](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

## <a name="next-steps"></a>Nästa steg

- Se [Översikt över Benchmark för Azure-säkerhet V2](/azure/security/benchmarks/overview)
- Läs mer om [säkerhetsbaslinjer för Azure](/azure/security/benchmarks/security-baselines-overview)
