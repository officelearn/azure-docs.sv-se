---
title: Referens guide för Azure Active Directory hanterings åtgärder för autentisering
description: Den här åtgärds hand boken beskriver de kontroller och åtgärder som du bör vidta för att skydda autentiseringen
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: bc5824fcb62477d4e6dc6c2b7390b1bfa916094f
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368057"
---
# <a name="azure-active-directory-authentication-management-operations-reference-guide"></a>Referens guide för Azure Active Directory hanterings åtgärder för autentisering

Det här avsnittet i [hand boken för Azure AD-åtgärder](active-directory-ops-guide-intro.md) beskriver de kontroller och åtgärder som du bör vidta för att skydda och hantera autentiseringsuppgifter, definiera autentisering, delegera tilldelning, mäta användning och definiera åtkomst principer baserat på position för företags säkerhet.

> [!NOTE]
> Dessa rekommendationer är aktuella vid publicerings datumet, men kan ändras med tiden. Organisationer bör kontinuerligt utvärdera sina identitets metoder när Microsofts produkter och tjänster utvecklas med tiden.

## <a name="key-operational-processes"></a>Viktiga operativa processer

### <a name="assign-owners-to-key-tasks"></a>Tilldela ägare till viktiga uppgifter

Hantering av Azure Active Directory kräver kontinuerlig körning av viktiga operativa uppgifter och processer, som kanske inte ingår i ett lanserings projekt. Det är fortfarande viktigt att du konfigurerar dessa uppgifter för att optimera din miljö. De viktigaste uppgifterna och deras rekommenderade ägare är:

| Aktivitet | Ägare |
| :- | :- |
| Hantera konfiguration av enkel inloggning (SSO) i Azure AD | IAM-åtgärds team |
| Design principer för villkorlig åtkomst för Azure AD-program | Arkitektur team för informations säkerhet |
| Arkivera inloggnings aktivitet i ett SIEM-system | Åtgärds team för informations säkerhet |
| Arkivera risk händelser i ett SIEM-system | Åtgärds team för informations säkerhet |
| Prioritering och undersök säkerhets rapporter | Åtgärds team för informations säkerhet |
| Prioritering och undersök risk händelser | Åtgärds team för informations säkerhet |
| Prioritering och undersök användare som har flaggats för risk-och sårbarhets rapporter från Azure AD Identity Protection | Åtgärds team för informations säkerhet |

> [!NOTE]
> Azure AD Identity Protection kräver en Azure AD Premium P2-licens. Information om rätt licens för dina krav finns i [jämföra allmänt tillgängliga funktioner i Azure AD Free-och Azure AD Premium-versionerna](https://azure.microsoft.com/pricing/details/active-directory/).

När du granskar listan kanske du måste tilldela en ägare för aktiviteter som saknar ägare eller justera ägarskapet för aktiviteter med ägare som inte är justerade enligt rekommendationerna ovan.

#### <a name="owner-recommended-reading"></a>Rekommenderad läsning av ägare

- [Tilldela administratörsroller i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Styrning i Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="credentials-management"></a>Hantering av autentiseringsuppgifter

### <a name="password-policies"></a>Lösenordsprinciper

Att hantera lösen ord säkert är en av de viktigaste delarna av identitets-och åtkomst hantering och ofta det största målet för angrepp. Azure AD stöder flera funktioner som kan hjälpa till att förhindra att en attack lyckas.

Använd tabellen nedan för att hitta den rekommenderade lösningen för att åtgärda problemet som måste åtgärdas:

| Problem | Rekommendation |
| :- | :- |
| Ingen mekanism för att skydda mot svaga lösen ord | Aktivera återställning av [lösen ord för självbetjäning i Azure AD (SSPR)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks) och [lösen ords skydd](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises) |
| Ingen mekanism för att identifiera läckta lösen ord | Aktivera PHS ( [Password hash Sync](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) ) för att få insikter |
| Använda AD FS och inte flytta till hanterad autentisering | Aktivera [AD FS extra näts Smart utelåsning](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) och/eller [Azure AD Smart utelåsning](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout) |
| Lösen ords principen använder komplexitets regler som längd, flera teckenuppsättningar eller förfallo datum | Fundera över fördelarna med [Microsofts rekommenderade metoder](https://aka.ms/passwordguidance) och växla din metod till lösen ords hantering och distribuera [Azure AD Password Protection](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad). |
| Användare har inte registrerats för användning av Multi-Factor Authentication (MFA) | [Registrera all användares säkerhets information](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-mfa-policy) så att den kan användas som en mekanism för att verifiera användarens identitet tillsammans med lösen ordet |
| Det finns inget återkallande av lösen ord baserat på användar risk | Distribuera [användar risk principer](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy) i Azure AD Identity Protection för att framtvinga lösen ords ändringar för läckta AUTENTISERINGSUPPGIFTER med SSPR |
| Det finns ingen Smart utelåsnings funktion för att skydda skadlig autentisering från felaktiga aktörer som kommer från identifierade IP-adresser | Distribuera molnbaserad autentisering med antingen Password-hash-synkronisering eller [direktautentisering](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) (PTA) |

#### <a name="password-policies-recommended-reading"></a>Lösen ords principer rekommenderas att läsa

- [Azure AD och AD FS bästa praxis: försvara mot angrepp vid lösen ords attacker – Enterprise Mobility + Security](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

### <a name="enable-self-service-password-reset-and-password-protection"></a>Aktivera självbetjäning för återställning av lösen ord och lösen ords skydd

Användare som behöver ändra eller återställa sina lösen ord är en av de största volym källorna och kostnader för support samtal. Förutom kostnaden är det grundläggande att ändra lösen ordet som ett verktyg för att minimera en användar risk är ett grundläggande steg i att förbättra din organisations säkerhets position.

Vi rekommenderar minst att du distribuerar Azure AD självbetjäning för [återställning av lösen ord](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks) (SSPR) och lokalt [lösen ords skydd](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy) för att utföra:

- Avhjälpa support samtal.
- Ersätt användningen av tillfälliga lösen ord.
- Ersätt eventuella befintliga självbetjänings lösningar för lösen ords hantering som förlitar sig på en lokal lösning.
- [Eliminera svaga lösen ord](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad) i din organisation.

> [!NOTE]
> För organisationer med en Azure AD Premium P2-prenumeration rekommenderar vi att du distribuerar SSPR och använder den som en del av en [identitets skydds princip för användar risker](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy).

### <a name="strong-credential-management"></a>Hantering av starka autentiseringsuppgifter

Lösen orden själva är inte tillräckligt säkra för att förhindra att dåliga aktörer får åtkomst till din miljö. Som minst måste alla användare med ett privilegierat konto aktive ras för Multi-Factor Authentication (MFA). Vi rekommenderar att du aktiverar [kombinerad registrering](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined) och kräver att alla användare registrerar sig för MFA och SSPR med hjälp av den [kombinerade registrerings upplevelsen](https://docs.microsoft.com/azure/active-directory/user-help/user-help-security-info-overview). Slutligen rekommenderar vi att du använder en strategi för att [ge återhämtning](https://docs.microsoft.com/azure/active-directory/authentication/concept-resilient-controls) för att minska risken för utelåsning på grund av oförutsedda omständigheter.

![Kombinerat användar upplevelse flöde](./media/active-directory-ops-guide/active-directory-ops-img4.png)

### <a name="on-premises-outage-authentication-resiliency"></a>Återhämtnings förmåga för autentisering på lokalt avbrott

Förutom fördelarna med enkelhet och aktivering av läckta autentiseringsuppgifter för identifiering av autentiseringsuppgifter, tillåter Azure AD Password hash Sync (PHS) och Azure MFA att användare kan komma åt SaaS-program och Office 365 trots lokala avbrott på grund av cyberattacker, till exempel [NotPetya](https://www.microsoft.com/security/blog/2018/02/05/overview-of-petya-a-rapid-cyberattack/). Det är också möjligt att aktivera PHS i samband med Federation. Genom att aktivera PHS får du en återställning av autentisering när Federations tjänsterna inte är tillgängliga.

Om din lokala organisation saknar återhämtnings strategi för avbrott eller har en som inte är integrerad med Azure AD, bör du Distribuera Azure AD PHS och definiera en katastrof återställnings plan som innehåller PHS. Genom att aktivera Azure AD PHS kan användare autentisera sig mot Azure AD om din lokala Active Directory inte är tillgänglig.

![flöde för synkronisering av lösen ords hash](./media/active-directory-ops-guide/active-directory-ops-img5.png)

För att bättre förstå dina autentiseringsalternativ, se [Välj rätt autentiseringsmetod för din Azure Active Directory hybrid identitets lösning](https://docs.microsoft.com/azure/security/azure-ad-choose-authn).

### <a name="programmatic-usage-of-credentials"></a>Användning av autentiseringsuppgifter för program mässig användning

Azure AD-skript med hjälp av PowerShell eller program som använder Microsoft Graph API kräver säker autentisering. Dålig hantering av autentiseringsuppgifter som kör dessa skript och verktyg ökar risken för stöld av autentiseringsuppgifter. Om du använder skript eller program som förlitar sig på hårdkodade lösen ord eller lösen ord, bör du först granska lösen ord i konfigurationsfiler eller käll koden och sedan ersätta dessa beroenden och använda Azure Managed Identities, integrerad Windows-autentisering eller [certifikat](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-access-api-with-certificates) närhelst det är möjligt. Överväg att använda [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)för program där tidigare lösningar inte är möjliga.

Om du fastställer att det finns tjänstens huvud namn med autentiseringsuppgifter för lösen ord och du är osäker på hur lösen ords uppgifterna skyddas av skript eller program, kan du kontakta programmets ägare för att bättre förstå användnings mönster.

Microsoft rekommenderar också att du kontaktar program ägare för att förstå användnings mönster om det finns tjänstens huvud namn med autentiseringsuppgifter för lösen ord.

## <a name="authentication-experience"></a>Autentisering

### <a name="on-premises-authentication"></a>Lokal autentisering

Federerad autentisering med integrerad Windows-autentisering (IWA) eller sömlös enkel inloggning (SSO) hanterad autentisering med lösen ords-hash-synkronisering eller direktautentisering är den bästa användar upplevelsen i företags nätverket med stegvisa insikter för lokala domänkontrollanter. Den minimerar risken för autentiseringsuppgifter för att minska risken för att användare som faller Prey till nätfiske-attacker minskar. Om du redan använder molnbaserad autentisering med PHS eller PTA, men användarna fortfarande behöver ange sina lösen ord vid autentisering lokalt, bör du omedelbart [distribuera sömlös SSO](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso). Å andra sidan bör du implementera sömlös SSO som en del av migreringsjobbet om du för närvarande är federerad med planer för att till sist migrera till molnbaserad autentisering.

### <a name="device-trust-access-policies"></a>Åtkomst principer för enhets förtroende

Som en användare i din organisation är en enhet en kärn identitet som du vill skydda. Du kan använda en enhets identitet för att skydda dina resurser när du vill och var som helst. Att autentisera enheten och redovisningen för dess förtroende typ ökar din säkerhets position och användbarhet genom att:

- Undvika friktion, till exempel med MFA, när enheten är betrodd
- Blockera åtkomst från ej betrodda enheter
- För Windows 10-enheter ger [enkel inloggning till lokala resurser sömlöst](https://docs.microsoft.com/azure/active-directory/devices/azuread-join-sso).

Du kan utföra det här målet genom att sätta enhets identiteter och hantera dem i Azure AD genom att använda någon av följande metoder:

- Organisationer kan använda [Microsoft Intune](https://docs.microsoft.com/intune/what-is-intune) för att hantera enheten och genomdriva efterlevnadsprinciper, intyga enhetens hälsa och ange principer för villkorlig åtkomst baserat på om enheten är kompatibel. Microsoft Intune kan hantera iOS-enheter, Mac-datorer (via JAMF-integrering), Windows-skrivbord (internt med hantering av mobila enheter för Windows 10 och samhantering med Microsoft Endpoint Configuration Manager) och mobila Android-enheter.
- [Hybrid Azure AD-anslutning](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-managed-domains) ger hantering med grup principer eller Microsoft Endpoint Configuration Manager i en miljö med Active Directory domänanslutna dator enheter. Organisationer kan distribuera en hanterad miljö antingen via PHS eller PTA med sömlös SSO. Genom att ta med dina enheter till Azure AD kan du maximera användar produktiviteten via SSO i molnet och lokala resurser samtidigt som du kan skydda åtkomsten till molnet och lokala resurser med för [villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) på samma gång.

Om du har domänanslutna Windows-enheter som inte är registrerade i molnet, eller domänanslutna Windows-enheter som är registrerade i molnet men utan principer för villkorlig åtkomst, bör du registrera de oregistrerade enheterna och i båda fallen [använda hybrid Azure AD Join som en kontroll](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) i dina principer för villkorlig åtkomst.

![En skärm bild av bidraget i principen för villkorlig åtkomst som kräver hybrid enhet](./media/active-directory-ops-guide/active-directory-ops-img6.png)

Om du hanterar enheter med MDM eller Microsoft Intune, men inte använder enhets kontroller i dina principer för villkorlig åtkomst, rekommenderar vi att du använder [Kräv att enheten är markerad som kompatibel](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices#require-device-to-be-marked-as-compliant) som en kontroll i dessa principer.

![En skärm bild av bidraget i principen för villkorlig åtkomst som kräver enhetens efterlevnad](./media/active-directory-ops-guide/active-directory-ops-img7.png)

#### <a name="device-trust-access-policies-recommended-reading"></a>Åtkomst principer för enhets förtroende rekommenderas läsning

- [Gör så här: planera din hybrid Azure Active Directory delta-implementering](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)
- [Konfigurationer för identitets-och enhets åtkomst](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

### <a name="windows-hello-for-business"></a>Windows Hello för företag

I Windows 10 ersätter [Windows Hello för företag](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification) lösen ord med stark tvåfaktorautentisering på datorer. Windows Hello för företag ger en mer strömlinjeformad MFA-upplevelse för användare och minskar beroendet av lösen ord. Om du inte har börjat installera Windows 10-enheter eller bara har delvis distribuerat dem, rekommenderar vi att du uppgraderar till Windows 10 och [aktiverar Windows Hello för företag](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) på alla enheter.

Om du vill veta mer om lösen ords lös autentisering kan du läsa mer om lösen ords lös autentisering i [en värld utan lösen ord med Azure Active Directory](https://aka.ms/passwordlessdoc).

## <a name="application-authentication-and-assignment"></a>Programautentisering och tilldelning

### <a name="single-sign-on-for-apps"></a>Enkel inloggning för appar

Att tillhandahålla en standardiserad mekanism för enkel inloggning till hela företaget är avgörande för bästa användar upplevelse, minskning av risk, rapportering och styrning. Om du använder program som har stöd för enkel inloggning med Azure AD men som för närvarande är konfigurerade för att använda lokala konton, bör du konfigurera om dessa program så att de använder SSO med Azure AD. Om du använder program som har stöd för enkel inloggning med Azure AD men använder en annan identitetsprovider, bör du även konfigurera om dessa program så att de använder enkel inloggning med Azure AD. För program som inte stöder Federations protokoll, men som stöder formulärbaserad autentisering, rekommenderar vi att du konfigurerar programmet att använda [lösen ords valv](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-password-vaulting) med Azure AD-programproxy.

![AppProxy-baserad inloggning](./media/active-directory-ops-guide/active-directory-ops-img8.png)

> [!NOTE]
> Om du inte har en mekanism för att identifiera ohanterade program i din organisation rekommenderar vi att du implementerar en identifierings process med hjälp av en lösning för CASB (Cloud Access Security Broker) som [Microsoft Cloud App Security](https://www.microsoft.com/enterprise-mobility-security/cloud-app-security).

Slutligen, om du har ett Azure AD App-galleri och använder program som har stöd för enkel inloggning med Azure AD, rekommenderar vi [att du visar programmet i app-galleriet](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing).

#### <a name="single-sign-on-recommended-reading"></a>Enkel inloggning, Rekommenderad läsning

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

### <a name="migration-of-ad-fs-applications-to-azure-ad"></a>Migrering av AD FS program till Azure AD

[Migrering av appar från AD FS till Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-apps-to-azure) ger ytterligare funktioner för säkerhet, mer konsekvent hanterbarhet och en bättre samarbets upplevelse. Om du har program som kon figurer ATS i AD FS som stöder SSO med Azure AD bör du konfigurera om dessa program så att de använder SSO med Azure AD. Om du har program som kon figurer ATS i AD FS med ovanliga konfigurationer som inte stöds av Azure AD, bör du kontakta appens ägare för att ta reda på om den särskilda konfigurationen är ett absolut krav för programmet. Om det inte behövs bör du konfigurera om programmet så att det använder SSO med Azure AD.

![Azure AD som primär identitets leverantör](./media/active-directory-ops-guide/active-directory-ops-img9.png)

> [!NOTE]
> [Azure AD Connect Health för ADFS](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs) kan användas för att samla in konfigurations information om varje program som eventuellt kan migreras till Azure AD.

### <a name="assign-users-to-applications"></a>Tilldela användare till program

[Att tilldela användare till program](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups) mappas bäst med hjälp av grupper eftersom de ger större flexibilitet och möjlighet att hantera i hög skala. Fördelarna med att använda grupper är [attribut-baserat dynamiskt grupp medlemskap](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership) och [delegering till app-ägare](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-accessmanagement-managing-group-owners). Om du redan använder och hanterar grupper rekommenderar vi därför att du vidtar följande åtgärder för att förbättra hanteringen i skala:

- Delegera grupp hantering och styrning till program ägare.
- Tillåt självbetjänings åtkomst till programmet.
- Definiera dynamiska grupper om användarattribut kan konsekvent bestämma åtkomst till program.
- Implementera attestering till grupper som används för program åtkomst med hjälp av [åtkomst granskningar i Azure AD](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview).

Om du däremot hittar program som har tilldelning till enskilda användare ska du se till att implementera [styrningar](https://docs.microsoft.com/azure/active-directory/governance/index) runt dessa program.

#### <a name="assign-users-to-applications-recommended-reading"></a>Tilldela användare till program som rekommenderas läsning

- [Tilldela användare och grupper till ett program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups)
- [Delegera registrerings behörigheter för app i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-delegate-app-roles)
- [Regler för dynamiskt medlemskap för grupper i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

## <a name="access-policies"></a>Åtkomstprinciper

### <a name="named-locations"></a>Namngivna platser

Med [namngivna platser](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations) i Azure AD kan du märka betrodda IP-adressintervall i din organisation. Azure AD använder namngivna platser för att:

- Förhindra falska positiva identifieringar i risk händelser. Om du loggar in från en betrodd nätverks plats sänker du en användares inloggnings risk.
- Konfigurera [plats baserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations).

![Namngivna platser](./media/active-directory-ops-guide/active-directory-ops-img10.png)

Baserat på prioritet använder du tabellen nedan för att hitta den rekommenderade lösning som bäst uppfyller organisationens behov:

| **Prioritet** | **Scenario** | **Rekommenderade** |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1 | Om du använder PHS eller PTA och namngivna platser inte har definierats | Definiera namngivna platser för att förbättra identifieringen av risk händelser |
| 2 | Om du är federerad och inte använder "insideCorporateNetwork"-anspråk och namngivna platser inte har definierats | Definiera namngivna platser för att förbättra identifieringen av risk händelser |
| 3 | Om du inte använder namngivna platser i principer för villkorlig åtkomst och det inte finns några risk-eller enhets kontroller i principer för villkorlig åtkomst | Konfigurera principen för villkorlig åtkomst så att den innehåller namngivna platser |
| 4 | Om du är federerad och använder "insideCorporateNetwork"-anspråk och namngivna platser inte har definierats | Definiera namngivna platser för att förbättra identifieringen av risk händelser |
| 5 | Om du använder betrodda IP-adresser med MFA i stället för namngivna platser och markerar dem som betrodda | Definiera namngivna platser och markera dem som betrodda för att förbättra identifieringen av risk händelser |

### <a name="risk-based-access-policies"></a>Riskfyllda åtkomst principer

Azure AD kan beräkna risken för varje inloggning och varje användare. Att använda risk som ett villkor i åtkomst principer kan ge en bättre användar upplevelse, till exempel färre autentiserings-prompter och bättre säkerhet, till exempel endast fråga användare när de behövs och automatisera svar och reparationer.

![Princip för inloggnings risk](./media/active-directory-ops-guide/active-directory-ops-img11.png)

Om du redan äger Azure AD Premium P2-licenser som stöder användning av risk i åtkomst principer, men inte används, rekommenderar vi starkt att du lägger till risker för din säkerhets position.

#### <a name="risk-based-access-policies-recommended-reading"></a>Vid riskfyllda åtkomst principer rekommenderas läsning

- [Gör så här: Konfigurera inloggnings risk principen](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-sign-in-risk-policy)
- [Gör så här: konfigurera användar risk principen](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)

### <a name="client-application-access-policies"></a>Åtkomst principer för klient program

Microsoft Intune Application Management (MAM) ger möjlighet att skicka data skydds kontroller, till exempel lagrings kryptering, PIN-kod, rensning av Fjärrlagring osv. till kompatibla klient mobil program som Outlook Mobile. Dessutom kan principer för villkorlig åtkomst skapas för att [begränsa åtkomsten](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) till moln tjänster, till exempel Exchange Online från godkända eller kompatibla appar.

Om dina anställda installerar MAM program som Office-mobilappar för att komma åt företagets resurser, till exempel Exchange Online eller SharePoint Online, och du också stöder BYOD (ta med din egen enhet) rekommenderar vi att du distribuerar program MAM-principer för att hantera program konfigurationen i personligt ägda enheter utan MDM-registrering och uppdaterar sedan dina principer för villkorlig åtkomst för att endast tillåta åtkomst från MAM-kompatibla klienter.

![Kontroll av villkorlig åtkomst beviljande](./media/active-directory-ops-guide/active-directory-ops-img12.png)

Om de anställda ska kunna installera MAM program mot företags resurser och åtkomst är begränsat på Intune-hanterade enheter bör du överväga att distribuera program MAM-principer för att hantera program konfigurationen för personliga enheter och uppdatera principer för villkorlig åtkomst för att endast tillåta åtkomst från MAM-kompatibla klienter.

### <a name="conditional-access-implementation"></a>Implementering av villkorlig åtkomst

Villkorlig åtkomst är ett viktigt verktyg för att förbättra din organisations säkerhets position. Därför är det viktigt att du följer dessa metod tips:

- Se till att alla SaaS-program har minst en princip tillämpad
- Undvik att kombinera filtret **alla appar** med **block** kontrollen för att undvika utelåsnings risk
- Undvik att använda **alla användare** som ett filter och oavsiktligt lägga till **gäster**
- **Migrera alla äldre principer till Azure Portal**
- Fånga alla kriterier för användare, enheter och program
- Använd principer för villkorlig åtkomst för att [implementera MFA](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access)i stället för att använda **MFA per användare**
- Ha en liten uppsättning kärn principer som kan tillämpas på flera program
- Definiera tomma undantags grupper och Lägg till dem i principerna för att få en undantags strategi
- Planera för [Bryt glas](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#break-glass-what-to-do-in-an-emergency) konton utan MFA-kontroller
- Säkerställ en konsekvent upplevelse för Office 365-klientprogram, till exempel team, OneDrive för företag, Outlook osv.) genom att implementera samma uppsättning kontroller för tjänster som Exchange Online och SharePoint Online
- Tilldelning till principer bör implementeras genom grupper, inte individer
- Gör regelbundna granskningar av undantags grupperna som används i principer för att begränsa den tid som användarna ligger utanför säkerhets position. Om du äger Azure AD P2 kan du använda åtkomst granskningar för att automatisera processen

#### <a name="conditional-access-recommended-reading"></a>Rekommenderad läsning för villkorlig åtkomst

- [Metod tips för villkorlig åtkomst i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices)
- [Konfigurationer för identitets-och enhets åtkomst](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Referens för Azure Active Directory villkorlig åtkomst inställningar](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference)
- [Vanliga principer för villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)

## <a name="access-surface-area"></a>Åtkomst till Surface-området

### <a name="legacy-authentication"></a>Äldre autentisering

Starka autentiseringsuppgifter, till exempel MFA, kan inte skydda appar med äldre autentiseringsprotokoll, vilket gör det till den önskade angrepps vektorn från skadliga aktörer. Att låsa äldre autentisering är avgörande för att förbättra åtkomst säkerhets position.

Äldre autentisering är en term som refererar till autentiseringsprotokoll som används av appar som:

- Äldre Office-klienter som inte använder modern autentisering (till exempel Office 2010-klient)
- Klienter som använder e-postprotokoll som IMAP/SMTP/POP

Angripare föredrar dessa protokoll – i själva verket är det nästan [100% av](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984) angrepps behållarna för att använda bakåtkompatibla autentiseringsprotokoll! Hackare använder äldre autentiseringsprotokoll, eftersom de inte stöder interaktiv inloggning, vilket krävs för ytterligare säkerhets utmaningar som multifaktorautentisering och enhetsautentisering.

Om äldre autentisering ofta används i din miljö bör du planera att migrera äldre klienter till klienter som stöder [modern autentisering](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) så snart som möjligt. Om du har vissa användare som redan använder modern autentisering i samma token, men andra som fortfarande använder äldre autentisering, bör du vidta följande steg för att låsa äldre autentiserings klienter:

1. Använd [rapporter om inloggnings aktiviteter](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) för att identifiera användare som fortfarande använder tidigare autentisering och prenumerations reparationer:

   a. Uppgradera till moderna klienter som kan påverka användare.
   
   b. Planera en start punkt-tidsram för att låsa upp enligt stegen nedan.
   
   c. Identifiera vilka äldre program som har ett hårt beroende av tidigare autentisering. Se steg 3 nedan.

2. Inaktivera äldre protokoll på källan (till exempel Exchange-postlåda) för användare som inte använder äldre autentisering för att undvika mer exponering.
3. För de återstående kontona (helst icke-mänskliga identiteter som tjänst konton) använder [du villkorlig åtkomst för att begränsa äldre protokoll](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Conditional-Access-support-for-blocking-legacy-auth-is/ba-p/245417) efter autentisering.

#### <a name="legacy-authentication-recommended-reading"></a>Tidigare autentisering, Rekommenderad läsning

- [Aktivera eller inaktivera POP3-eller IMAP4-åtkomst till post lådor i Exchange Server](https://docs.microsoft.com/exchange/clients/pop3-and-imap4/configure-mailbox-access?view=exchserver-2019)

### <a name="consent-grants"></a>Medgivande bidrag

Vid en olaglig godkännande attack skapar angriparen ett Azure AD-registrerat program som begär åtkomst till data, till exempel kontakt information, e-post eller dokument. Användare kan beviljas medgivande till skadliga program via nätfiske-attacker vid landning på skadliga webbplatser.

Nedan visas en lista över appar med behörigheter som du kanske vill granska för Microsofts moln tjänster:

- Appar med appar eller delegerade \*. ReadWrite-behörigheter
- Appar med delegerade behörigheter kan läsa, skicka eller hantera e-post för användarens räkning
- Appar som beviljas med följande behörigheter:

| Resurs | Behörighet |
| :- | :- |
| Office 365 Exchange Online | EA. AccessAsUser. all |
| | EWS. AccessAsUser. all |
| | E-post. Read |
| Microsoft Graph-API | E-post. Read |
| | Mail. Read. Shared |
| | Mail. ReadWrite |

- Appar har beviljat fullständig användar personifiering av den inloggade användaren. Exempel:

|Resurs | Behörighet |
| :- | :- |
| Microsoft Graph-API| Directory. AccessAsUser. all |
| Azure-REST API | user_impersonation |

För att undvika det här scenariot bör du läsa om att [identifiera och åtgärda illegala medgivande i Office 365](https://docs.microsoft.com/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) för att identifiera och åtgärda alla program med illegala bidrag eller program som har fler bidrag än vad som behövs. Ta sedan bort självbetjäningen [helt och hållet](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-user-consent) och [upprätta styrnings procedurer](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow). Slutligen kan du schemalägga regelbundna granskningar av app-behörigheter och ta bort dem när de inte behövs.

#### <a name="consent-grants-recommended-reading"></a>Medgivande ger Rekommenderad läsning

- [Microsoft Graph API-behörigheter](https://docs.microsoft.com/graph/permissions-reference)

### <a name="user-and-group-settings"></a>Användar-och grupp inställningar

Nedan visas de användar-och grupp inställningar som kan låsas ned om det inte finns något behov av affärs behov:

#### <a name="user-settings"></a>Användar inställningar

- **Externa användare** – externt samarbete kan ske ekologiskt i företaget med tjänster som team, Power BI, SharePoint Online och Azure information Protection. Om du har uttryckliga begränsningar för att styra det externa samarbetet som initieras av användaren, rekommenderar vi att du aktiverar externa användare med hjälp av [hantering av Azure AD-rättighet](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) eller en kontrollerad åtgärd, till exempel genom supportavdelningen. Om du inte vill tillåta ekologiskt externt samarbete för tjänster kan du [blockera medlemmar från att bjuda in externa användare fullständigt](https://docs.microsoft.com/azure/active-directory/b2b/delegate-invitations). Du kan också [tillåta eller blockera vissa domäner](https://docs.microsoft.com/azure/active-directory/b2b/allow-deny-list) i externa användar inbjudningar.
- **App-registreringar** – när Appregistreringar är aktiverade kan slutanvändare själva publicera program och ge åtkomst till sina data. Ett typiskt exempel på registrering av appar är användare som aktiverar Outlook-plugin-program eller röst assistenter som Alexa och Siri för att läsa e-post och kalender eller skicka e-post åt dig. Om kunden bestämmer sig för att inaktivera registrering av appar, måste informations-och IAM-teamen ingå i hanteringen av undantag (app-registreringar som behövs utifrån affärs kraven), eftersom de skulle behöva registrera programmen med ett administratörs konto. Vi behöver förmodligen utforma en process för att operationalisera processen.
- **Administrations portalen** – organisationer kan låsa Azure AD-bladet i Azure Portal så att icke-administratörer inte kan komma åt Azure AD-hanteringen i Azure Portal och få förvirrande. Begränsa åtkomsten genom att gå till användar inställningarna i hanterings portalen för Azure AD:

![Begränsad åtkomst till administrations Portal](./media/active-directory-ops-guide/active-directory-ops-img13.png)

> [!NOTE]
> Icke-administratörer kan fortfarande komma åt Azure AD-hanterings gränssnitt via kommando rads program och andra programmerings gränssnitt.

#### <a name="group-settings"></a>Grupp inställningar

**Grupp hantering via självbetjäning/användare kan skapa säkerhets grupper/O365-grupper.** Om det inte finns något aktuellt självbetjänings initiativ för grupper i molnet kan kunderna välja att inaktivera det tills de är redo att använda den här funktionen.

#### <a name="groups-recommended-reading"></a>Grupper som rekommenderar läsning

- [Vad är Azure Active Directory B2B-samarbete?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
- [Integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)
- [Appar, behörigheter och medgivande i Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)
- [Använd grupper för att hantera åtkomst till resurser i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)
- [Konfigurera åtkomst hantering för självbetjänings program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)

### <a name="traffic-from-unexpected-locations"></a>Trafik från oväntade platser

Angripare kommer från olika delar av världen. Hantera den här risken genom att använda principer för villkorlig åtkomst med plats som villkor. [Plats villkoret](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition) för en princip för villkorlig åtkomst gör att du kan blockera åtkomst för platser där det inte finns något affärs skäl att logga in från.

![Skapa en ny namngiven plats](./media/active-directory-ops-guide/active-directory-ops-img14.png)

Om det är tillgängligt använder du en SIEM-lösning (Security information and Event Management) för att analysera och hitta mönster för åtkomst över flera regioner. Om du inte använder en SIEM-produkt, eller om du inte matar in autentiseringsinformation från Azure AD, rekommenderar vi att du använder [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) för att identifiera åtkomst mönster över flera regioner.

## <a name="access-usage"></a>Åtkomst användning

### <a name="azure-ad-logs-archived-and-integrated-with-incident-response-plans"></a>Azure AD-loggar har arkiverats och integrerats med incident svars planer

För att få till gång till inloggnings aktivitet är granskningar och risk händelser för Azure AD avgörande för fel sökning, användnings analys och data utredning undersökningar. Azure AD ger åtkomst till dessa källor via REST-API: er som har en begränsad kvarhållningsperiod. Ett system för säkerhets informations-och händelse hantering (SIEM), eller motsvarande lagrings teknik, är nyckeln för långsiktig lagring av revisioner och support. Om du vill aktivera långsiktig lagring av Azure AD-loggar måste du antingen lägga till dem i din befintliga SIEM-lösning eller använda [Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor). Arkivera loggar som kan användas som en del av dina incident svars planer och undersökningar.

#### <a name="logs-recommended-reading"></a>Loggar Rekommenderad läsning

- [Azure Active Directory gransknings-API-referens](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference)
- [Rapport API-referens för Azure Active Directory inloggnings aktivitet](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-sign-in-activity-reference)
- [Hämta data med hjälp av Azure AD repor ting API med certifikat](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-with-certificates)
- [Microsoft Graph för Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)
- [API-referens för Office 365 Management Activity](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference)
- [Använda Azure Active Directory Power BI innehålls paketet](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

## <a name="summary"></a>Sammanfattning

Det finns 12 aspekter till en säker identitets infrastruktur. Med den här listan kan du skydda och hantera autentiseringsuppgifter ytterligare, definiera autentisering, delegera tilldelning, mäta användning och definiera åtkomst principer baserat på position för företags säkerhet.

- Tilldela ägare till viktiga uppgifter.
- Implementera lösningar för att identifiera svaga eller läcker lösen ord, förbättra lösen ords hantering och skydd och ytterligare säkra användar åtkomst till resurser.
- Hantera identiteten för enheter för att skydda dina resurser när som helst och var som helst.
- Implementera lösenordsbaserad autentisering.
- Tillhandahålla en standardiserad mekanism för enkel inloggning i hela organisationen.
- Migrera appar från AD FS till Azure AD för att ge bättre säkerhet och mer konsekvent hanterbarhet.
- Tilldela användare till program med hjälp av grupper för att ge större flexibilitet och möjlighet att hantera i stor skala.
- Konfigurera riskfyllda åtkomst principer.
- Lås bakåtkompatibla autentiseringsprotokoll.
- Identifiera och åtgärda illegala medgivande bidrag.
- Lås användar-och grupp inställningar.
- Aktivera långsiktig lagring av Azure AD-loggar för fel sökning, användnings analys och data utredning undersökningar.

## <a name="next-steps"></a>Nästa steg

Kom igång med [identitets styrningens operativa kontroller och åtgärder](active-directory-ops-guide-govern.md).
