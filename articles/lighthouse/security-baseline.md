---
title: Azures säkerhets bas linje för Azure Lighthouse
description: Azure Lighthouse Security-bas linjen ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: lighthouse
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 51c786d12c372276d1cb007cc5a929ab6a6302f7
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94974986"
---
# <a name="azure-security-baseline-for-azure-lighthouse"></a>Azures säkerhets bas linje för Azure Lighthouse

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark-version 2,0](../security/benchmarks/overview.md) till Azure Lighthouse. Azures säkerhets benchmark ger rekommendationer om hur du kan skydda dina moln lösningar på Azure. Innehållet grupperas efter de **säkerhets kontroller** som definieras av Azures säkerhets benchmark och relaterade rikt linjer för Azure Lighthouse. **Kontroller** som inte gäller för Azure-Lighthouse har uteslutits.

Om du vill se hur Azure-Lighthouse helt mappar till Azures säkerhets benchmark kan du läsa mer i den [fullständiga mappnings filen för Azure Lighthouse Security](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="identity-management"></a>Identitetshantering

*Mer information finns i [Azure Security benchmark: Identity Management](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: standardisera Azure Active Directory som system för central identitets-och autentisering

**Vägledning**: Azure Lighthouse använder Azure Active Directory (Azure AD) som standard tjänst för identitets-och åtkomst hantering. Standardisera Azure AD för att styra organisationens identitets-och åtkomst hantering i:
- Microsoft Cloud resurser, till exempel Azure Portal, Azure Storage, virtuell Azure-dator (Linux och Windows), Azure Key Vault, PaaS och SaaS program.
- Organisationens resurser, till exempel program i Azure eller företagets nätverks resurser.

Med Azure Lighthouse har angivna användare i en hanterings klient en inbyggd Azure-roll som gör att de kan komma åt delegerade prenumerationer och/eller resurs grupper i en kunds klient organisation. Alla inbyggda roller stöds för närvarande förutom ägare eller inbyggda roller med DataActions-behörighet. Rollen administratör för användar åtkomst stöds endast för begränsad användning i tilldela roller till hanterade identiteter. Administratörs roller för anpassade roller och klassiska prenumerationer stöds inte.

- [Innehav i Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Så här skapar och konfigurerar du en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Använda externa identitets leverantörer för program](/azure/active-directory/b2b/identity-providers) 

- [Vad är säkerhets poängen för identiteter i Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: hantera program identiteter säkert och automatiskt

**Vägledning**: Azure Managed identiteter kan autentiseras för Azure-tjänster och-resurser som stöder Azure AD-autentisering. Autentisering aktive ras via fördefinierade regler för åtkomst beviljande, Undvik hårdkodade autentiseringsuppgifter i källkod eller konfigurationsfiler. Med Azure Lighthouse kan användare med rollen administratör för användar åtkomst på en kunds prenumeration skapa en hanterad identitet i den kundens klient organisation. Även om den här rollen inte i allmänhet stöds med Azure-Lighthouse, kan den användas i det här scenariot, så att användare med den här behörigheten kan tilldela en eller flera angivna inbyggda roller till hanterade identiteter.

För tjänster som inte har stöd för hanterade identiteter använder du Azure AD för att skapa ett huvud namn för tjänsten med begränsade behörigheter på resurs nivå i stället. Med Azure Lighthouse får tjänstens huvud namn åtkomst till kund resurser enligt de roller som de har beviljats under onboarding-processen. Vi rekommenderar att du konfigurerar tjänstens huvud namn med autentiseringsuppgifter för certifikatet och att de ska återgå till klient hemligheter. I båda fallen kan Azure Key Vault användas tillsammans med Azure Managed identiteter, så att körnings miljön (till exempel en Azure-funktion) kan hämta autentiseringsuppgiften från nyckel valvet.

- [Hanterade Azure-identiteter](../active-directory/managed-identities-azure-resources/overview.md)

- [Huvudnamn för Azure-tjänsten](/powershell/azure/create-azure-service-principal-azureps)

- [Använd Azure Key Vault för registrering av säkerhets objekt](../key-vault/general/authentication.md#authorize-a-security-principal-to-access-key-vault)

- [Skapa en användare som kan tilldela roller till en hanterad identitet i en kund klient organisation](how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Använd starka verifierings kontroller för alla Azure Active Directory-baserade åtkomst

**Vägledning**: Kräv Multi-Factor Authentication (MFA) för alla användare i hanterings klienten, inklusive användare som kommer att ha åtkomst till delegerade kund resurser. Vi rekommenderar att du ber dina kunder att implementera MFA i sina klienter också.

- [Så här aktiverar du MFA i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: övervaka och Varna vid konto avvikelser

**Vägledning**: Azure AD tillhandahåller följande data Källor: 

-   Inloggningar – inloggnings rapporten innehåller information om användningen av hanterade program och användar inloggnings aktiviteter.

-   Gransknings loggar – ger spårning genom loggar för alla ändringar som görs via olika funktioner i Azure AD. Exempel på gransknings loggar för loggade ändringar omfattar att lägga till eller ta bort användare, appar, grupper, roller och principer.

-   Riskfyllda inloggningar – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare.

-   Användare som har flaggats för risk – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats.

Dessa data källor kan integreras med Azure Monitor, Azure Sentinel eller SIEM system från tredje part.

Tjänst leverantörer som använder Azure Lighthouse kan vidarebefordra Azure AD-loggar till Azure Sentinel och Visa/ange aviseringar över klienter för att övervaka och Varna vid konto avvikelser.

- [Granska aktivitets rapporter i Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Visa Azure AD-riskfyllda inloggningar](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Hantera Azure Sentinel-arbetsytor i skala](how-to/manage-sentinel-workspaces.md)

- [Anslut data från Azure AD till Azure Sentinel](../sentinel/connect-azure-active-directory.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: begränsa åtkomsten till Azure-resursen baserat på villkor

**Vägledning**: Azure Lighthouse har inte stöd för en villkorlig åtkomst funktion för delegerade kund resurser. I hantering av klient organisation använder du villkorlig åtkomst för Azure AD för mer detaljerad åtkomst kontroll baserat på användardefinierade villkor, t. ex. att kräva användar inloggningar från vissa IP-intervall för att använda Multi-Factor Authentication (MFA). En detaljerad hantering av autentiseringsbegäranden kan också användas via en princip för villkorlig åtkomst i Azure AD för olika användnings fall. 

Du bör kräva MFA för alla användare i din hanterings klient, inklusive användare som kommer att ha åtkomst till delegerade kund resurser. Vi rekommenderar att du ber dina kunder att implementera MFA i sina klienter också.

- [Översikt över villkorlig Azure-åtkomst](../active-directory/conditional-access/overview.md)

- [Vanliga principer för villkorlig åtkomst](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Konfigurera autentiseringsessionshantering med villkorsstyrd åtkomst](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="privileged-access"></a>Privilegie rad åtkomst

*Mer information finns i [Azure Security benchmark: privilegie rad åtkomst](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: skydda och begränsa privilegierade användare

**Vägledning**: begränsa antalet starkt privilegierade användar konton och skydda dessa konton på en utökad nivå. Det krävs inget globalt administratörs konto för att aktivera och använda Azure-Lighthouse.

För att få åtkomst till aktivitets logg data på klient nivå måste ett konto tilldelas den inbyggda rollen för övervaknings läsaren i rot omfånget (/). Eftersom övervaknings läsar rollen i rot omfånget är en bred åtkomst nivå rekommenderar vi att du tilldelar rollen till ett huvud konto för tjänsten i stället för till en enskild användare eller till en grupp. Den här tilldelningen måste utföras av en användare som har rollen global administratör med ytterligare utökad åtkomst. Den här utökade åtkomsten ska läggas till omedelbart innan du gör roll tilldelningen och sedan ta bort den när tilldelningen är klar.

- [Administratörs roll behörigheter i Azure AD](/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

- [Tilldela åtkomst för att övervaka aktivitets logg data på klient nivå](how-to/monitor-delegation-changes.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: begränsa administrativ åtkomst till verksamhets kritiska system

**Vägledning**: Azure Lighthouse använder Azures rollbaserad åtkomst kontroll (Azure RBAC) för att isolera åtkomst till affärs kritiska system genom att begränsa vilka konton som har privilegie rad åtkomst till de prenumerationer och hanterings grupper som de finns i.

Se till att du följer principen om minsta behörighet så att användarna bara har de behörigheter som krävs för att utföra sina uppgifter.

Se till att du också begränsar åtkomsten till hanterings-, identitets-och säkerhets system som har administrativ åtkomst till din affärs kritiska åtkomst, till exempel Active Directory-domän controllers (DCs), säkerhets verktyg och system hanterings verktyg med agenter som är installerade på affärs kritiska system. Angripare som angriper dessa hanterings-och säkerhets system kan omedelbart weaponize dem för att kompromettera affärs kritiska till gångar.

Alla typer av åtkomst kontroller bör justeras mot din strategi för företags segmentering för att säkerställa en konsekvent åtkomst kontroll.

- [Azure-komponenter och referens modell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Åtkomst till hanterings grupp](../governance/management-groups/overview.md#management-group-access)

- [Administratörer för Azure-prenumeration](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [Metod tips för att definiera användare och roller för Azure Lighthouse](concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: granska och Stäm av användar åtkomst regelbundet

**Vägledning**: Azure Lighthouse använder Azure Active Directory-konton (Azure AD) för att hantera sina resurser, granska användar konton och få åtkomst till tilldelning regelbundet för att säkerställa att kontona och deras åtkomst är giltiga. Du kan använda åtkomst granskningar i Azure AD för att granska grupp medlemskap, åtkomst till företags program och roll tilldelningar. Azure AD repor ting kan tillhandahålla loggar för att identifiera inaktuella konton. Du kan också använda Azure AD Privileged Identity Management för att skapa åtkomst gransknings rapportens arbets flöde för att under lätta gransknings processen.

Kunderna kan granska åtkomst nivån som beviljas användare i hanterings klienten via Azure Lighthouse i Azure Portal. De kan när som helst ta bort den här åtkomsten.

Dessutom kan Azure Privileged Identity Management också konfigureras för att varna när ett högt antal administratörs konton skapas och för att identifiera administratörs konton som är inaktuella eller felaktigt konfigurerade.

Observera: att vissa Azure-tjänster har stöd för lokala användare och roller som inte hanteras via Azure AD. Du måste hantera dessa användare separat.

- [Skapa en åtkomst granskning av Azure Resource roles i Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Ta bort åtkomst till en delegering](how-to/remove-delegation.md)

- [Använda Azure AD-identitets-och åtkomst granskningar](../active-directory/governance/access-reviews-overview.md)

- [Visa sidan tjänst leverantörer i Azure Portal](how-to/view-manage-service-providers.md)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Konfigurera nöd åtkomst i Azure AD

**Vägledning**: Azure Lighthouse är integrerat med Azure Active Directory för att hantera sina resurser. För att förhindra oavsiktligt låst av din Azure AD-organisation konfigurerar du ett konto för nöd åtkomst för åtkomst när normala administrativa konton inte kan användas. Konton för nöd åtkomst är ofta mycket privilegierade och de bör inte tilldelas vissa individer. Konton för nöd åtkomst är begränsade till nöd situationer eller "Break glas"-scenarier där normala administrativa konton inte kan användas.

Du bör se till att autentiseringsuppgifterna (till exempel lösen ord, certifikat eller smartkort) för konton för nöd åtkomst hålls skyddade och endast kända för personer som har behörighet att använda dem endast i nödfall.

- [Hantera konton för nöd åtkomst i Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatisera rättighets hantering 

**Vägledning**: Azure Lighthouse är integrerat med Azure Active Directory (Azure AD) för att hantera sina resurser. Använd funktioner för hantering av Azure AD-rättighet för att automatisera åtkomst till begär ande arbets flöden, inklusive åtkomst tilldelningar, recensioner och förfallo datum. Det finns också stöd för godkännande med dubbla eller flera steg.

- [Vad är åtkomst granskningar för Azure AD](../active-directory/governance/access-reviews-overview.md) 

- [Vad är hantering av Azure AD-berättigande](../active-directory/governance/entitlement-management-overview.md)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Använd arbets stationer med privilegie rad åtkomst

**Vägledning**: skyddade, isolerade arbets stationer är mycket viktiga för att skydda känsliga roller som administratörer, utvecklare och kritiska tjänst operatörer. Beroende på dina behov kan du använda mycket säkra användar arbets stationer och/eller Azure-skydds för att utföra administrativa uppgifter med Azure Lighthouse i produktions miljöer. Använd Azure Active Directory, Microsoft Defender Avancerat skydd (ATP) och/eller Microsoft Intune för att distribuera en säker och hanterad användar arbets station för administrativa uppgifter. De skyddade arbets stationerna kan hanteras centralt för att upprätthålla säker konfiguration, inklusive stark autentisering, program vara och maskin varu bas linjer och begränsad logisk och nätverks åtkomst. 

- [Förstå arbets stationer med privilegie rad åtkomst](../active-directory/devices/concept-azure-managed-workstation.md)

- [Distribuera en privilegie rad åtkomst arbets Station](../active-directory/devices/howto-azure-managed-workstation.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Följ bara tillräckligt med administration (princip för minsta behörighet) 

**Vägledning**: Azure Lighthouse är integrerat med rollbaserad åtkomst kontroll (RBAC) i Azure för att hantera dess resurser. Med Azure RBAC kan du hantera åtkomst till Azure-resurser via roll tilldelningar. Du kan tilldela de här inbyggda rollerna till användare, grupper, tjänstens huvud namn och hanterade identiteter. Det finns fördefinierade inbyggda roller för vissa resurser och dessa roller kan inventeras eller frågas via verktyg som Azure CLI, Azure PowerShell eller Azure Portal. De behörigheter som du tilldelar resurser via Azure RBAC bör alltid vara begränsade till vad som krävs av rollerna. Detta kompletterar JIT-metoden (just in Time) för Azure AD Privileged Identity Management (PIM) och bör granskas regelbundet. Använd inbyggda roller för att allokera behörighet och endast skapa anpassade roller vid behov.

Med Azure-Lighthouse får du till gång till delegerade kund resurser med hjälp av inbyggda Azure-roller. I de flesta fall vill du tilldela dessa roller till en grupp eller tjänstens huvud namn i stället för till många enskilda användar konton. På så sätt kan du lägga till eller ta bort åtkomst för enskilda användare utan att behöva uppdatera och publicera om planen när dina åtkomst krav ändras.

För att delegera kund resurser till en hanterings klient måste en distribution göras av ett konto som inte är gäst i kundens klient organisation som har den inbyggda rollen för den prenumeration som har registrerats (eller som innehåller de resurs grupper som har registrerats).

- [Förstå klienter, användare och roller i Azure Lighthouse](concepts/tenants-users-roles.md)

- [Så här tillämpar du principen om minsta behörighet för Azure-Lighthouse](concepts/recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege)

- [Vad är Azure rollbaserad åtkomst kontroll (Azure RBAC)](../role-based-access-control/overview.md) 

- [Använda Azure AD-identitets-och åtkomst granskningar](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center övervakning**: Ja

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

**Vägledning**: kundernas säkerhets team kan granska aktivitets loggar för att se aktivitet som tas av tjänst leverantörer som använder Azure Lighthouse. 

Om en tjänst leverantör vill att deras säkerhets team ska kunna granska delegerade kund resurser bör säkerhets teamets auktorisering innehålla den inbyggda rollen läsare.

- [Hur en kund kan granska service providerns aktivitet](how-to/view-service-provider-activity.md)

- [Ange roller när du registrerar en kund i Azure Lighthouse](how-to/onboard-customer.md#define-roles-and-permissions)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att granska och begränsa vilka tjänster som användare kan etablera i din miljö. Använd Azure Resource Graph för att fråga efter och identifiera resurser i prenumerationerna. Du kan också använda Azure Monitor för att skapa regler för att utlösa aviseringar när en icke-godkänd tjänst upptäcks.

- [Så här konfigurerar och hanterar du Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Så här nekar du en speciell resurs typ med Azure Policy](../governance/policy/samples/built-in-policies.md#general) 

- [Så här skapar du frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: se till att till gångens livs cykel hantering är säker

**Vägledning**: ta bort åtkomst till resurser som har delegerats via Azure-Lighthouse när de inte längre behövs, så att tjänste leverantörer inte längre har åtkomst. Åtkomst kan tas bort av antingen kunden eller av tjänst leverantören. 

- [Ta bort åtkomst till en delegering](how-to/remove-delegation.md)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Azure Lighthouse är integrerat med Azure Active Directory (Azure AD) för identitet och autentisering. Du kan använda villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azures resurser Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azures resurs hanterare](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

## <a name="logging-and-threat-detection"></a>Loggning och hot identifiering

*Mer information finns i [säkerhets benchmark för Azure: loggning och hot identifiering](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Aktivera hot identifiering för Azure-resurser

**Vägledning**: via Azure Lighthouse kan du övervaka dina kunders Azure-resurser för potentiella hot och avvikelser. Fokusera på att få aviseringar med hög kvalitet för att minska antalet falska positiva identifieringar för analytiker som ska sorteras. Aviseringar kan hämtas från loggdata, agenter eller andra data.

Använd Azure Security Center inbyggd hot identifierings funktion, som baseras på övervakning av Azure-tjänsten telemetri och analys av tjänst loggar. Data samlas in med hjälp av Log Analytics agent, som läser olika säkerhetsrelaterade konfigurationer och händelse loggar från systemet och kopierar data till din arbets yta för analys. 

Dessutom kan du använda Azure Sentinel för att bygga analys regler, som rör hot som matchar specifika kriterier i kundens miljö. Reglerna genererar incidenter när villkoren matchas, så att du kan undersöka varje incident. Azure Sentinel kan också importera Hot information från tredje part för att förbättra sin funktion för hot identifiering. 

- [Skydd mot hot i Azure Security Center](/azure/security-center/threat-protection)

- [Referens guide för Azure Security Center säkerhets varningar](../security-center/alerts-reference.md)

- [Skapa anpassade analys regler för att identifiera hot](../sentinel/tutorial-detect-threats-custom.md)

- [Hantera Azure Sentinel-arbetsytor i skala](how-to/manage-sentinel-workspaces.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Aktivera hot identifiering för Azure Identity and Access Management

**Vägledning**: via Azure Lighthouse kan du använda Azure Security Center för att varna om vissa misstänkta aktiviteter i de kund innehavare som du hanterar, till exempel ett högt antal misslyckade autentiseringsförsök och föråldrade konton i prenumerationen.

Azure Active Directory (Azure AD) innehåller följande användar loggar som kan visas i Azure AD repor ting eller integreras med Azure Monitor, Azure Sentinel eller andra SIEM/övervaknings verktyg för mer avancerade övervaknings-och analys användnings fall:
- Inloggning – inloggnings rapporten innehåller information om användningen av hanterade program och användar inloggnings aktiviteter.
- Granskningsloggar – Ger spårbarhet via loggar för alla ändringar som gjorts via olika funktioner i Azure AD. Exempel på gransknings loggar är ändringar som har gjorts i alla resurser i Azure AD, till exempel att lägga till eller ta bort användare, appar, grupper, roller och principer.
- Riskfylld inloggning – en riskfylld inloggning är en indikator för ett inloggnings försök som kan ha utförts av någon som inte är den legitima ägaren till ett användar konto.
- Användare som har flaggats för risk – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats.

Azure Security Center kan också Avisera om vissa misstänkta aktiviteter, till exempel ett högt antal misslyckade autentiseringsförsök, föråldrade konton i prenumerationen. Utöver den grundläggande övervakningen av säkerhetsövervakning kan Azure Security Centerens skydds modul även samla in mer detaljerade säkerhets aviseringar från enskilda Azure Compute-resurser (virtuella datorer, behållare, App Service), data resurser (SQL DB och lagring) och Azure Service-lager. Den här funktionen ger insyn i konto avvikelser i de enskilda resurserna. 

- [Förbättrade tjänster och scenarier med Azure Lighthouse](concepts/cross-tenant-management-experience.md#enhanced-services-and-scenarios)

- [Granska aktivitets rapporter i Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Aktivera Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) 

- [Skydd mot hot i Azure Security Center](/azure/security-center/threat-protection)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Aktivera loggning för Azure-resurser

**Vägledning**: aktivitets loggar, som är automatiskt tillgängliga, innehåller alla Skriv åtgärder (placera, skicka, ta bort) för dina Azure Lighthouse-resurser förutom Läs åtgärder (get). Aktivitets loggar kan användas för att hitta ett fel när du felsöker eller övervakar hur en användare i organisationen ändrade en resurs.

Med Azure Lighthouse kan du använda Azure Monitor loggar på ett skalbart sätt över de kund klienter som du hanterar. Skapa Log Analytics arbets ytor direkt i kundens klient organisation så att kunddata behålls i sina klienter i stället för att exporteras till dina kunder. Det möjliggör också centraliserad övervakning av resurser eller tjänster som stöds av Log Analytics, vilket ger dig mer flexibilitet för vilka typer av data du övervakar.

Kunder som har delegerade prenumerationer för Azure Lighthouse kan visa Azures aktivitets logg data för att se alla åtgärder som vidtagits. Detta ger kunderna full insyn i åtgärder som tjänst leverantörer utför, tillsammans med åtgärder som utförs av användare i kundens egna Azure Active Directory (Azure AD)-klient.

- [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Förstå loggning och olika logg typer i Azure](../azure-monitor/platform/platform-logs-overview.md)

- [Övervaka delegerade resurser i stor skala](how-to/monitor-at-scale.md)

- [Visa tjänstleverantörsaktivitet](how-to/view-service-provider-activity.md)

**Azure Security Center övervakning**: Ja

**Ansvars område**: delat

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

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurera kvarhållning av logg lagring

**Vägledning**: Azure Lighthouse genererar för närvarande inte några säkerhetsrelaterade loggar. Kunder som vill visa Service Provider-aktivitet kan konfigurera logg kvarhållning enligt efterlevnad, regel och affärs krav. 

I Azure Monitor kan du Log Analytics ange arbets ytans lagrings period enligt organisationens regler för efterlevnad. Använd Azure Storage-, Data Lake-eller Log Analytics arbets ytans konton för långsiktig lagring och arkivering.

- [Ändra data lagrings perioden i Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Konfigurera bevarande princip för Azure Storage konto loggar](../storage/common/storage-monitor-storage-account.md#configure-logging)

- [Azure Security Center aviseringar och rekommendationer exportera](../security-center/continuous-export.md) konfigurera och kunden kan inte ange någon logg kvarhållning.

- [Hur en kund kan granska aktivitets logg data för tjänst leverantörer](how-to/view-service-provider-activity.md)

**Azure Security Center övervakning**: inte tillämpligt

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

Azure Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller analysera det som används för att utfärda aviseringen, samt den konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

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

**Vägledning**: Azure Lighthouse har stöd för följande tjänstspecifika principer som är tillgängliga i Azure Security Center att granska och tillämpa konfigurationer för dina Azure-resurser. Detta kan konfigureras i Azure Security Center eller Azure Policy initiativ.

- Tillåt hantering av klient-ID: n till onboarding via Azure Lighthouse

- Granska delegering av omfattningar till en hanterings klient

Du kan använda Azure-ritningar för att automatisera distribution och konfiguration av tjänster och program miljöer, inklusive Azure Resource Manager mallar, Azure RBAC-kontroller och-principer, i en enda skiss definition.

- [Azure Lighthouse-principer](samples/policy-reference.md)

- [Självstudie – Skapa och hantera principer för att genomdriva efterlevnad](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Blueprint](../governance/blueprints/overview.md)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: upprätthålla säkra konfigurationer för Azure-tjänster

**Vägledning**: Azure Lighthouse har stöd för följande tjänstspecifika principer som är tillgängliga i Azure Security Center att granska och tillämpa konfigurationer för dina Azure-resurser. Detta kan konfigureras i Azure Security Center eller Azure Policy initiativ.

- [Azure Lighthouse-principer](samples/policy-reference.md)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: upprätta säkra konfigurationer för beräknings resurser

**Vägledning**: Använd Azure Security Center och Azure policy för att upprätta säkra konfigurationer för alla beräknings resurser, inklusive virtuella datorer, behållare och andra.

- [Så här övervakar du Azure Security Center rekommendationer](../security-center/security-center-recommendations.md) 

- [Säkerhetsrekommendationer – en referensguide](../security-center/recommendations-reference.md)

**Azure Security Center övervakning**: Ja

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
