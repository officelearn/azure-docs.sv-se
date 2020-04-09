---
title: Referensguide för Azure Active Directory-autentiseringsåtgärder
description: I referensguiden för åtgärder beskrivs de kontroller och åtgärder du bör vidta för att skydda autentiseringshantering
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
ms.openlocfilehash: f25abb70a95f559cf0cc14efa6cf9f0e81ec9ec0
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876300"
---
# <a name="azure-active-directory-authentication-management-operations-reference-guide"></a>Referensguide för Azure Active Directory-autentiseringsåtgärder

I det här avsnittet i [referensguiden för Azure AD-åtgärder](active-directory-ops-guide-intro.md) beskrivs de kontroller och åtgärder du bör vidta för att skydda och hantera autentiseringsuppgifter, definiera autentiseringsupplevelse, delegera tilldelning, mäta användning och definiera åtkomstprinciper baserat på företagets säkerhetsposition.

> [!NOTE]
> Dessa rekommendationer är aktuella från och med publiceringsdatumet men kan ändras med tiden. Organisationer bör kontinuerligt utvärdera sina identitetsmetoder när Microsofts produkter och tjänster utvecklas med tiden.

## <a name="key-operational-processes"></a>Viktiga operativa processer

### <a name="assign-owners-to-key-tasks"></a>Tilldela ägare till nyckeluppgifter

För att hantera Azure Active Directory krävs kontinuerlig körning av viktiga operativa uppgifter och processer, som kanske inte ingår i ett distributionsprojekt. Det är fortfarande viktigt att du ställer in dessa uppgifter för att optimera din miljö. De viktigaste uppgifterna och deras rekommenderade ägare är:

| Aktivitet | Ägare |
| :- | :- |
| Hantera livscykeln för SSO-konfiguration (Single Sign-on) i Azure AD | IAM Operations Team |
| Utforma principer för villkorlig åtkomst för Azure AD-program | InfoSec-arkitekturteam |
| Arkivera inloggningsaktivitet i ett SIEM-system | InfoSec operationsteam |
| Arkivera riskhändelser i ett SIEM-system | InfoSec operationsteam |
| Triage och undersöka säkerhetsrapporter | InfoSec operationsteam |
| Triage och undersöka riskhändelser | InfoSec operationsteam |
| Triage och undersöka användare som flaggats för risk- och sårbarhetsrapporter från Azure AD Identity Protection | InfoSec operationsteam |

> [!NOTE]
> Azure AD Identity Protection kräver en Azure AD Premium P2-licens. Information om hur du hittar rätt licens för dina behov finns i [Jämföra allmänt tillgängliga funktioner i Azure AD Free- och Azure AD Premium-utgåvorna](https://azure.microsoft.com/pricing/details/active-directory/).

När du granskar listan kan du behöva tilldela en ägare för uppgifter som saknar en ägare eller justera ägarskapet för uppgifter med ägare som inte är anpassade till rekommendationerna ovan.

#### <a name="owner-recommended-reading"></a>Ägaren rekommenderade läsning

- [Tilldela administratörsroller i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Styrning i Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="credentials-management"></a>Hantering av autentiseringsuppgifter

### <a name="password-policies"></a>Lösenordsprinciper

Att hantera lösenord på ett säkert sätt är en av de mest kritiska delarna av identitets- och åtkomsthantering och ofta det största målet för attacker. Azure AD stöder flera funktioner som kan förhindra att en attack lyckas.

Använd tabellen nedan för att hitta den rekommenderade lösningen för att minska problemet som måste åtgärdas:

| Problem | Rekommendation |
| :- | :- |
| Ingen mekanism för att skydda mot svaga lösenord | Aktivera [Azure AD-självbetjäningslösenordsåterställning (SSPR)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks) och [lösenordsskydd](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises) |
| Ingen mekanism för att upptäcka läckta lösenord | Aktivera [PHS (Password Hash Sync)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) för att få insikter |
| Använda AD FS och inte gå över till hanterad autentisering | Aktivera [AD FS Extranet Smart Lockout](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) och /eller Azure AD Smart [Lockout](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout) |
| Lösenordsprincipen använder komplexitetsbaserade regler som längd, flera teckenuppsättningar eller förfallodatum | Ompröva till förmån för [Microsofts rekommenderade metoder](https://www.microsoft.com/research/publication/password-guidance/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F265143%2Fmicrosoft_password_guidance.pdf) och byt metod till lösenordshantering och distribuera [Azure AD-lösenordsskydd](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad). |
| Användare är inte registrerade för att använda multifaktorautentisering (MFA) | [Registrera alla användares säkerhetsinformation](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-mfa-policy) så att den kan användas som en mekanism för att verifiera användarens identitet tillsammans med deras lösenord |
| Det finns ingen återkallande av lösenord baserat på användarrisk | Distribuera Azure AD [Identity Protection användarriskprinciper](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy) för att tvinga lösenordsändringar på läckta autentiseringsuppgifter med SSPR |
| Det finns ingen smart utelåsningsmekanism för att skydda skadlig autentisering från dåliga aktörer som kommer från identifierade IP-adresser | Distribuera molnhanterad autentisering med antingen lösenordshim-synkronisering eller [direktautentisering](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) (PTA) |

#### <a name="password-policies-recommended-reading"></a>Lösenordsprinciper rekommenderas läsning

- [Metodtips för Azure AD och AD FS: Försvara sig mot lösenordssprayattacker – Enterprise Mobility + Security](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

### <a name="enable-self-service-password-reset-and-password-protection"></a>Aktivera återställning av lösenord för självbetjäning och lösenordsskydd

Användare som behöver ändra eller återställa sina lösenord är en av de största källorna till volym och kostnad för helpdesk samtal. Förutom kostnad är det ett grundläggande steg att ändra lösenordet som ett verktyg för att minska en användarrisk för att förbättra säkerhetshållningen för din organisation.

Vi rekommenderar att du distribuerar Azure [AD-självbetjäningslösenordsåterställning](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks) (SSPR) och lokalt [lösenordsskydd](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy) för att uppnå:

- Avledning helpdesk samtal.
- Ersätt användningen av tillfälliga lösenord.
- Ersätt alla befintliga lösenordshanteringslösningar för självbetjäning som är beroende av en lokal lösning.
- [Eliminera svaga lösenord](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad) i organisationen.

> [!NOTE]
> För organisationer med en Azure AD Premium P2-prenumeration rekommenderas att distribuera SSPR och använda den som en del av en [användarriskprincip för identitetsskydd](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy).

### <a name="strong-credential-management"></a>Stark hantering av autentiseringsuppgifter

Lösenord i sig är inte tillräckligt säkra för att förhindra att dåliga aktörer får åtkomst till din miljö. Alla användare med ett privilegierat konto måste vara aktiverade för MFA (Multifaktorautentisering). Helst bör du aktivera [kombinerad registrering](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined) och kräva att alla användare registrerar sig för MFA och SSPR med hjälp av den [kombinerade registreringsupplevelsen](https://docs.microsoft.com/azure/active-directory/user-help/user-help-security-info-overview). Så småningom rekommenderar vi att du antar en strategi för att [ge motståndskraft](https://docs.microsoft.com/azure/active-directory/authentication/concept-resilient-controls) för att minska risken för lockout på grund av oförutsedda omständigheter.

![Kombinerat flöde för användarupplevelse](./media/active-directory-ops-guide/active-directory-ops-img4.png)

### <a name="on-premises-outage-authentication-resiliency"></a>Lokal återhämtning för avbrottsautentisering

Utöver fördelarna med enkelhet och möjliggör läckt identifiering av autentiseringsuppgifter tillåter Azure AD Password Hash Sync (PHS) och Azure MFA användare att komma åt SaaS-program och Office 365 trots lokala avbrott på grund av cyberattacker som [NotPetya](https://www.microsoft.com/security/blog/2018/02/05/overview-of-petya-a-rapid-cyberattack/). Det är också möjligt att aktivera PHS i samarbete med federationen. Om du aktiverar PHS kan en återgång till autentisering när federationstjänster inte är tillgängliga.

Om din lokala organisation saknar en strategi för återhämtning av avbrott eller har en som inte är integrerad med Azure AD, bör du distribuera Azure AD PHS och definiera en katastrofåterställningsplan som innehåller PHS. Om du aktiverar Azure AD PHS kan användare autentisera mot Azure AD om din lokala Active Directory inte är tillgänglig.

![synkroniseringsflöde för lösenord hash](./media/active-directory-ops-guide/active-directory-ops-img5.png)

Information om hur du bättre förstår dina autentiseringsalternativ finns i [Välja rätt autentiseringsmetod för azure Active Directory-hybrididentitetslösningen](../hybrid/choose-ad-authn.md).

### <a name="programmatic-usage-of-credentials"></a>Programmatisk användning av autentiseringsuppgifter

Azure AD-skript som använder PowerShell eller program som använder Microsoft Graph API kräver säker autentisering. Dålig autentiseringshantering som kör dessa skript och verktyg ökar risken för stöld av autentiseringsuppgifter. Om du använder skript eller program som är beroende av hårdkodade lösenord eller lösenordspromptar bör du först granska lösenord i konfigurationsfiler eller källkod, sedan ersätta dessa beroenden och använda Azure Managed Identities, Integrated-Windows Authentication eller [certifikat](../reports-monitoring/tutorial-access-api-with-certificates.md) när det är möjligt. För program där de tidigare lösningarna inte är möjliga kan du överväga att använda [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Om du upptäcker att det finns tjänsthuvudnamn med lösenordsautentiseringsuppgifter och du är osäker på hur dessa lösenordsuppgifter skyddas av skript eller program kontaktar du programmets ägare för att bättre förstå användningsmönster.

Microsoft rekommenderar också att du kontaktar programägare för att förstå användningsmönster om det finns tjänsthuvudnamn med lösenordsuppgifter.

## <a name="authentication-experience"></a>Autentiseringsupplevelse

### <a name="on-premises-authentication"></a>Lokal autentisering

Federerad autentisering med integrerad Windows-autentisering (IWA) eller SSO (Seamless Single Sign-On) hanterad autentisering med lösenordsh/H-synkronisering eller vidareströmningsautentisering är den bästa användarupplevelsen när du är inne i företagsnätverket med siktlinje till lokala domänkontrollanter. Det minimerar autentiseringsuppgifter snabb trötthet och minskar risken för användare faller offer för phishing-attacker. Om du redan använder molnhanterad autentisering med PHS eller PTA, men användarna fortfarande måste skriva in sitt lösenord när de autentiserar lokalt, bör du omedelbart [distribuera Seamless SSO](../hybrid/how-to-connect-sso.md). Å andra sidan, om du för närvarande är federerad med planer på att så småningom migrera till molnhanterad autentisering, bör du implementera Sömlös SSO som en del av migreringsprojektet.

### <a name="device-trust-access-policies"></a>Principer för åtkomst till enhetsförtroende

Precis som en användare i organisationen är en enhet en kärnidentitet som du vill skydda. Du kan använda en enhets identitet för att skydda dina resurser när som helst och från vilken plats som helst.Om du autentiserar enheten och står för dess förtroendetyp förbättras din säkerhetsposition och användbarhet genom att:

- Undvika friktion, till exempel med MFA, när enheten är betrodd
- Blockera åtkomst från ej betrodda enheter
- För Windows 10-enheter tillhandahåller [du enkel inloggning till lokala resurser sömlöst.](../devices/azuread-join-sso.md)

Du kan utföra det här målet genom att skapa enhetsidentiteter och hantera dem i Azure AD med någon av följande metoder:

- Organisationer kan använda [Microsoft Intune](https://docs.microsoft.com/intune/what-is-intune) för att hantera enheten och tillämpa efterlevnadsprinciper, intyga enhetens hälsotillstånd och ange principer för villkorlig åtkomst baserat på om enheten är kompatibel. Microsoft Intune kan hantera iOS-enheter, Mac-datorer (Via JAMF-integrering), Windows-datorer (inbyggt med Mobile Device Management för Windows 10 och samhantering med Microsoft Endpoint Configuration Manager) och Mobila Android-enheter.
- [Hybrid Azure AD-koppling](../devices/hybrid-azuread-join-managed-domains.md) ger hantering med grupprinciper eller Microsoft Endpoint Configuration Manager i en miljö med Active Directory-domänanslutna datorenheter. Organisationer kan distribuera en hanterad miljö antingen via PHS eller PTA med Sömlös SSO. Genom att föra dina enheter till Azure AD maximeras användarnas produktivitet via SSO i molnet och lokala resurser samtidigt som du kan skydda åtkomsten till molnet och lokala resurser med [villkorlig åtkomst](../conditional-access/overview.md) samtidigt.

Om du har domänanslutna Windows-enheter som inte är registrerade i molnet, eller domänanslutna Windows-enheter som är registrerade i molnet men utan principer för villkorlig åtkomst, bör du registrera de oregistrerade enheterna och i båda fallen [använda Hybrid Azure AD-koppling som en kontroll](../conditional-access/require-managed-devices.md) i dina principer för villkorlig åtkomst.

![En skärmbild av beviljande i principen för villkorlig åtkomst som kräver hybridenhet](./media/active-directory-ops-guide/active-directory-ops-img6.png)

Om du hanterar enheter med MDM eller Microsoft Intune, men inte använder enhetskontroller i dina principer för villkorlig åtkomst, rekommenderar vi att du använder [Krävenhet som ska markeras som kompatibel](../conditional-access/require-managed-devices.md#require-device-to-be-marked-as-compliant) som en kontroll i dessa principer.

![En skärmbild av beviljande i principen för villkorlig åtkomst som kräver enhetsefterlevnad](./media/active-directory-ops-guide/active-directory-ops-img7.png)

#### <a name="device-trust-access-policies-recommended-reading"></a>Principer för åtkomst till enhetsförtroende rekommenderas läsning

- [Så här planerar du implementering av hybrid-Azure Active Directory-anslutning](../devices/hybrid-azuread-join-plan.md)
- [Konfigurationer för identitets- och enhetsåtkomst](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

### <a name="windows-hello-for-business"></a>Windows Hello för företag

I Windows 10 ersätter [Windows Hello för företag](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification) lösenord med stark tvåfaktorsautentisering på datorer. Windows Hello för företag möjliggör en mer strömlinjeformad MFA-upplevelse för användarna och minskar ditt beroende av lösenord. Om du inte har börjat distribuera Windows 10-enheter, eller bara delvis har distribuerat dem, rekommenderar vi att du uppgraderar till Windows 10 och [aktiverar Windows Hello för företag](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) på alla enheter.

Om du vill veta mer om lösenordslös autentisering läser du [En värld utan lösenord med Azure Active Directory](../authentication/concept-authentication-passwordless.md).

## <a name="application-authentication-and-assignment"></a>Autentisering och tilldelning av program

### <a name="single-sign-on-for-apps"></a>Enkel inloggning för appar

Att tillhandahålla en standardiserad enda inloggningsmekanism till hela företaget är avgörande för bästa användarupplevelse, minskad risk, förmåga att rapportera och styrning. Om du använder program som stöder SSO med Azure AD men för närvarande är konfigurerade för att använda lokala konton, bör du konfigurera om dessa program för att använda SSO med Azure AD. Om du använder program som stöder SSO med Azure AD men använder en annan identitetsprovider bör du konfigurera om dessa program så att de även använder SSO med Azure AD. För program som inte stöder federationsprotokoll men stöder formulärbaserad autentisering rekommenderar vi att du konfigurerar programmet så att det använder [lösenordsvalv](../manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md) med Azure AD Application Proxy.

![AppProxy Lösenordsbaserad inloggning](./media/active-directory-ops-guide/active-directory-ops-img8.png)

> [!NOTE]
> Om du inte har någon mekanism för att identifiera ohanterade program i din organisation rekommenderar vi att du implementerar en identifieringsprocess med hjälp av en lösning för säkerhetsutjämning för molnåtkomst (CASB), till exempel [Microsoft Cloud App Security](https://www.microsoft.com/enterprise-mobility-security/cloud-app-security).

Slutligen, om du har ett Azure AD-appgalleri och använder program som stöder SSO med Azure AD, rekommenderar vi [att du listar programmet i appgalleriet](../azuread-dev/howto-app-gallery-listing.md).

#### <a name="single-sign-on-recommended-reading"></a>Enkel inloggning rekommenderad läsning

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

### <a name="migration-of-ad-fs-applications-to-azure-ad"></a>Migrering av AD FS-program till Azure AD

[Migrera appar från AD FS till Azure AD](../manage-apps/migrate-adfs-apps-to-azure.md) möjliggör ytterligare funktioner för säkerhet, mer konsekvent hanterbarhet och en bättre samarbetsupplevelse. Om du har konfigurerat program i AD FS som stöder SSO med Azure AD bör du konfigurera om dessa program för att använda SSO med Azure AD. Om du har konfigurerat program i AD FS med ovanliga konfigurationer som inte stöds av Azure AD, bör du kontakta appägarna för att förstå om den särskilda konfigurationen är ett absolut krav för programmet. Om det inte krävs bör du konfigurera om programmet för att använda SSO med Azure AD.

![Azure AD som primär identitetsprovider](./media/active-directory-ops-guide/active-directory-ops-img9.png)

> [!NOTE]
> [Azure AD Connect Health för ADFS](../hybrid/how-to-connect-health-adfs.md) kan användas för att samla in konfigurationsinformation om varje program som eventuellt kan migreras till Azure AD.

### <a name="assign-users-to-applications"></a>Tilldela användare till program

[Att tilldela användare till program](../manage-apps/assign-user-or-group-access-portal.md) mappas bäst med hjälp av grupper eftersom de ger större flexibilitet och möjlighet att hantera i stor skala. Fördelarna med att använda grupper är [attributbaserat dynamiskt gruppmedlemskap](../users-groups-roles/groups-dynamic-membership.md) och [delegering till appägare](../fundamentals/active-directory-accessmanagement-managing-group-owners.md). Om du redan använder och hanterar grupper rekommenderar vi därför att du vidtar följande åtgärder för att förbättra hanteringen i stor skala:

- Delegera grupphantering och styrning till programägare.
- Tillåt självbetjäningsåtkomst till programmet.
- Definiera dynamiska grupper om användarattribut konsekvent kan avgöra åtkomst till program.
- Implementera attestation till grupper som används för programåtkomst med hjälp av [Azure AD-åtkomstgranskningar](../governance/access-reviews-overview.md).

Å andra sidan, om du hittar program som har tilldelning till enskilda användare, se till att implementera [styrning](https://docs.microsoft.com/azure/active-directory/governance/index) runt dessa program.

#### <a name="assign-users-to-applications-recommended-reading"></a>Tilldela användare till program som rekommenderas läsning

- [Tilldela användare och grupper till ett program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups)
- [Delegera appregistreringsbehörigheter i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-delegate-app-roles)
- [Dynamiska medlemskapsregler för grupper i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

## <a name="access-policies"></a>Åtkomstprinciper

### <a name="named-locations"></a>Namngivna platser

Med [namngivna platser](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations) i Azure AD kan du märka betrodda IP-adressintervall i din organisation. Azure AD använder namngivna platser för att:

- Förhindra falska positiva identifieringar i riskhändelser. När du loggar in från en betrodd nätverksplats minskar en användares inloggningsrisk.
- Konfigurera [platsbaserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations).

![Namngivna platser](./media/active-directory-ops-guide/active-directory-ops-img10.png)

Baserat på prioritet använder du tabellen nedan för att hitta den rekommenderade lösningen som bäst uppfyller organisationens behov:

| **Prioritet** | **Scenario** | **Rekommendation** |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1 | Om du använder PHS eller PTA och namngivna platser inte har definierats | Definiera namngivna platser för att förbättra identifieringen av riskhändelser |
| 2 | Om du är federerad och inte använder anspråket "insideCorporateNetwork" och namngivna platser inte har definierats | Definiera namngivna platser för att förbättra identifieringen av riskhändelser |
| 3 | Om du inte använder namngivna platser i principer för villkorlig åtkomst och det inte finns några risk- eller enhetskontroller i principer för villkorlig åtkomst | Konfigurera principen för villkorlig åtkomst så att den innehåller namngivna platser |
| 4 | Om du är federerad och använder anspråket "insideCorporateNetwork" och namngivna platser inte har definierats | Definiera namngivna platser för att förbättra identifieringen av riskhändelser |
| 5 | Om du använder betrodda IP-adresser med MFA i stället för namngivna platser och markerar dem som betrodda | Definiera namngivna platser och markera dem som betrodda för att förbättra identifieringen av riskhändelser |

### <a name="risk-based-access-policies"></a>Riskbaserade åtkomstprinciper

Azure AD kan beräkna risken för varje inloggning och varje användare. Om du använder risk som ett kriterium i åtkomstprinciper kan det ge en bättre användarupplevelse, till exempel färre autentiseringsuppmaningar och bättre säkerhet, till exempel bara att fråga användarna när de behövs, och automatisera svars- och reparationen.

![Riskpolicy för inloggning](./media/active-directory-ops-guide/active-directory-ops-img11.png)

Om du redan äger Azure AD Premium P2-licenser som stöder användning av risker i åtkomstprinciper, men de inte används, rekommenderar vi att du lägger till risker i din säkerhetsposition.

#### <a name="risk-based-access-policies-recommended-reading"></a>Riskbaserade åtkomstprinciper rekommenderas läsning

- [Så här konfigurerar du principen för inloggningsrisk](../identity-protection/howto-identity-protection-configure-risk-policies.md)
- [Så här konfigurerar du användarriskprincipen](../identity-protection/howto-identity-protection-configure-risk-policies.md)

### <a name="client-application-access-policies"></a>Principer för klientprogramåtkomst

Microsoft Intune Application Management (MAM) ger möjlighet att driva dataskyddskontroller som lagringskryptering, PIN-kod, fjärrlagringsrensning etc. till kompatibla klientmobilprogram som Outlook Mobile. Dessutom kan principer för villkorlig åtkomst skapas för att [begränsa åtkomsten](../conditional-access/app-based-conditional-access.md) till molntjänster som Exchange Online från godkända eller kompatibla appar.

Om dina anställda installerar MAM-kompatibla program som Office-mobilappar för att komma åt företagsresurser som Exchange Online eller SharePoint Online, och du även stöder BYOD (ta med din egen enhet), rekommenderar vi att du distribuerar MAM-principer för program för att hantera programkonfigurationen på personligt ägda enheter utan MDM-registrering och sedan uppdaterar dina principer för villkorlig åtkomst så att de endast tillåter åtkomst från MAM-kompatibla klienter.

![Bevilja villkorad åtkomst](./media/active-directory-ops-guide/active-directory-ops-img12.png)

Om anställda installerar MAM-kompatibla program mot företagsresurser och åtkomsten är begränsad på Intune-hanterade enheter bör du överväga att distribuera MAM-principer för program för att hantera programkonfigurationen för personliga enheter och uppdatera principer för villkorlig åtkomst så att de endast tillåter åtkomst från MAM-kompatibla klienter.

### <a name="conditional-access-implementation"></a>Implementering av villkorlig åtkomst

Villkorlig åtkomst är ett viktigt verktyg för att förbättra säkerhetspositionen för din organisation. Därför är det viktigt att du följer dessa metodtips:

- Se till att alla SaaS-program har minst en princip tillämpad
- Undvik att kombinera filtret **Alla appar** med **blockkontrollen** för att undvika lockoutrisk
- Undvik att använda **alla användare** som filter och oavsiktligt lägga till **gäster**
- **Migrera alla "äldre" principer till Azure-portalen**
- Fånga alla kriterier för användare, enheter och program
- Använd principer för villkorlig åtkomst för att [implementera MFA](../conditional-access/plan-conditional-access.md)i stället för att använda en **MFA per användare**
- Har en liten uppsättning grundläggande principer som kan tillämpas på flera program
- Definiera tomma undantagsgrupper och lägga till dem i principerna för att ha en undantagsstrategi
- Planera för [break glass-konton](../users-groups-roles/directory-admin-roles-secure.md#break-glass-what-to-do-in-an-emergency) utan MFA-kontroller
- Se till att office 365-klientprogram får en konsekvent upplevelse, till exempel Teams, OneDrive för företag, Outlook osv.) genom att implementera samma uppsättning kontroller för tjänster som Exchange Online och Sharepoint Online
- Tilldelning till policyer bör genomföras genom grupper, inte individer
- Gör regelbundna granskningar av undantagsgrupper som används i principer för att begränsa den tid som användarna har utanför säkerhetspositionen. Om du äger Azure AD P2 kan du använda åtkomstgranskningar för att automatisera processen

#### <a name="conditional-access-recommended-reading"></a>Villkorlig åtkomst rekommenderad läsning

- [Metodtips för villkorlig åtkomst i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices)
- [Konfigurationer för identitets- och enhetsåtkomst](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Referens för Azure Active Directory-inställningar för villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference)
- [Vanliga principer för villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)

## <a name="access-surface-area"></a>Åtkomstyta

### <a name="legacy-authentication"></a>Äldre autentisering

Starka autentiseringsuppgifter som MFA kan inte skydda appar med hjälp av äldre autentiseringsprotokoll, vilket gör det till den önskade angreppsvektorn av skadliga aktörer. Att låsa äldre autentisering är avgörande för att förbättra åtkomstsäkerheten.

Äldre autentisering är en term som refererar till autentiseringsprotokoll som används av appar som:

- Äldre Office-klienter som inte använder modern autentisering (till exempel Office 2010-klient)
- Klienter som använder e-postprotokoll som IMAP/SMTP/POP

Angripare föredrar starkt dessa protokoll - i själva verket nästan [100% av lösenord spray attacker](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984) använder äldre autentisering protokoll! Hackare använder äldre autentiseringsprotokoll eftersom de inte stöder interaktiv inloggning, vilket behövs för ytterligare säkerhetsutmaningar som multifaktorautentisering och enhetsautentisering.

Om äldre autentisering används i stor utsträckning i din miljö bör du planera att migrera äldre klienter till klienter som stöder [modern autentisering](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) så snart som möjligt. På samma sätt, om du har vissa användare som redan använder modern autentisering men andra som fortfarande använder äldre autentisering, bör du vidta följande åtgärder för att låsa äldre autentiseringsklienter:

1. Använd [inloggningsaktivitetsrapporter](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) för att identifiera användare som fortfarande använder äldre autentisering och planera reparation:

   a. Uppgradera till moderna autentiseringskompatibla klienter till berörda användare.
   
   b. Planera en cutover tidsram för att låsa ner per steg nedan.
   
   c. Identifiera vilka äldre program som har ett hårt beroende av äldre autentisering. Se steg 3 nedan.

2. Inaktivera äldre protokoll vid källan (till exempel Exchange Mailbox) för användare som inte använder äldre autentisering för att undvika mer exponering.
3. För de återstående kontona (helst icke-mänskliga identiteter som tjänstkonton) använder du [villkorlig åtkomst för att begränsa äldre protokoll](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Conditional-Access-support-for-blocking-legacy-auth-is/ba-p/245417) efter autentisering.

#### <a name="legacy-authentication-recommended-reading"></a>Äldre autentisering rekommenderad läsning

- [Aktivera eller inaktivera POP3- eller IMAP4-åtkomst till postlådor i Exchange Server](https://docs.microsoft.com/exchange/clients/pop3-and-imap4/configure-mailbox-access?view=exchserver-2019)

### <a name="consent-grants"></a>Bidrag till samtycke

I en attack med otillåtet medgivande skapar angriparen ett Azure AD-registrerat program som begär åtkomst till data som kontaktinformation, e-post eller dokument. Användare kan ge samtycke till skadliga program via nätfiskeattacker när de landar på skadliga webbplatser.

Nedan finns en lista över appar med behörigheter som du kanske vill granska för Microsofts molntjänster:

- Appar med app \*eller delegerad . ReadWrite-behörigheter
- Appar med delegerad behörighet kan läsa, skicka eller hantera e-post för användarens räkning
- Appar som beviljas med hjälp av följande behörigheter:

| Resurs | Behörighet |
| :- | :- |
| Office 365 Exchange Online | Eas. AccessAsUser.All |
| | Ews. AccessAsUser.All |
| | Mail.Read |
| Microsoft Graph-API | Mail.Read |
| | Mail.Read.Shared |
| | Mail.ReadWrite |

- Appar beviljade full användarpersonifiering av den inloggade användaren. Ett exempel:

|Resurs | Behörighet |
| :- | :- |
| Microsoft Graph-API| Directory.AccessAsUser.All |
| REST-API för Azure | user_impersonation |

För att undvika det här scenariot bör du hänvisa till att [identifiera och åtgärda olagliga samtyckesbidrag i Office 365](https://docs.microsoft.com/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) för att identifiera och åtgärda alla program med olagliga bidrag eller program som har fler bidrag än vad som är nödvändigt. Ta sedan [bort självbetjäning helt](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-user-consent) och hållet och [upprätta styrningsförfaranden](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow). Slutligen schemalägg regelbundna granskningar av appbehörigheter och ta bort dem när de inte behövs.

#### <a name="consent-grants-recommended-reading"></a>Samtyckesbidrag rekommenderas läsning

- [Behörigheter för Microsoft Graph-API](https://docs.microsoft.com/graph/permissions-reference)

### <a name="user-and-group-settings"></a>Användar- och gruppinställningar

Nedan finns de användar- och gruppinställningar som kan låsas om det inte finns ett uttryckligt affärsbehov:

#### <a name="user-settings"></a>Användarinställningar

- **Externa användare** – externt samarbete kan ske organiskt i företaget med tjänster som Teams, Power BI, Sharepoint Online och Azure Information Protection. Om du har uttryckliga begränsningar för att styra det externa samarbetet som initierats av användaren rekommenderar vi att du aktiverar externa användare genom att använda hantering av [Azure AD-berättigande](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) eller en kontrollerad åtgärd, till exempel via supportavdelningen. Om du inte vill tillåta organiskt externt samarbete för tjänster kan du blockera medlemmar från att [bjuda in externa användare helt](https://docs.microsoft.com/azure/active-directory/b2b/delegate-invitations). Du kan också [tillåta eller blockera specifika domäner](https://docs.microsoft.com/azure/active-directory/b2b/allow-deny-list) i externa användaranbjudningar.
- **Appregistreringar** – när appregistreringar är aktiverade kan slutanvändare själva registrera program och ge åtkomst till sina data. Ett typiskt exempel på appregistrering är användare som aktiverar Outlook-plugin-program eller röstassistenter som Alexa och Siri för att läsa sin e-post och kalender eller skicka e-postmeddelanden för deras räkning. Om kunden bestämmer sig för att inaktivera Appregistrering måste InfoSec- och IAM-teamen vara involverade i hanteringen av undantag (appregistreringar som behövs baserat på affärskrav), eftersom de skulle behöva registrera programmen med ett administratörskonto och troligen kräva att du utformar en process för att operationalisera processen.
- **Administrationsportal -** organisationer kan låsa Azure AD-bladet i Azure-portalen så att icke-administratörer inte kan komma åt Azure AD-hantering i Azure-portalen och bli förvirrade. Gå till användarinställningarna i Azure AD-hanteringsportalen för att begränsa åtkomsten:

![Begränsad åtkomst till administrationsportalen](./media/active-directory-ops-guide/active-directory-ops-img13.png)

> [!NOTE]
> Icke-administratörer kan fortfarande komma åt Azure AD-hanteringsgränssnitten via kommandoradsgränssnitt och andra programmatiska gränssnitt.

#### <a name="group-settings"></a>Gruppinställningar

**Självbetjäning Grupphantering / Användare kan skapa säkerhetsgrupper / O365 grupper.** Om det inte finns något aktuellt självbetjäningsinitiativ för grupper i molnet kan kunderna välja att stänga av det tills de är redo att använda den här funktionen.

#### <a name="groups-recommended-reading"></a>Grupper som rekommenderas läsning

- [Vad är Azure Active Directory B2B-samarbete?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
- [Integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)
- [Appar, behörigheter och medgivande i Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)
- [Använda grupper för att hantera åtkomst till resurser i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)
- [Konfigurera självbetjäningshantering för program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)

### <a name="traffic-from-unexpected-locations"></a>Trafik från oväntade platser

Angripare kommer från olika delar av världen. Hantera den här risken genom att använda principer för villkorlig åtkomst med plats som villkor. Med [platsvillkoret](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition) för en princip för villkorlig åtkomst kan du blockera åtkomst för platser där det inte finns någon affärsorsak att logga in från.

![Skapa en ny namngiven plats](./media/active-directory-ops-guide/active-directory-ops-img14.png)

Om det finns tillgängligt använder du en SIEM-lösning (Security Information and Event Management) för att analysera och hitta åtkomstmönster mellan regioner. Om du inte använder en SIEM-produkt, eller om den inte matar in autentiseringsinformation från Azure AD, rekommenderar vi att du använder [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) för att identifiera åtkomstmönster mellan regioner.

## <a name="access-usage"></a>Åtkomstanvändning

### <a name="azure-ad-logs-archived-and-integrated-with-incident-response-plans"></a>Azure AD-loggar arkiverade och integrerade med incidentsvarsplaner

Att ha åtkomst till inloggningsaktivitet, granskningar och riskhändelser för Azure AD är avgörande för felsökning, användningsanalys och kriminaltekniska undersökningar. Azure AD ger åtkomst till dessa källor via REST API:er som har en begränsad kvarhållningsperiod. Ett siem-system (security information and event management), eller motsvarande arkivteknik, är avgörande för långsiktig lagring av revisioner och support. Om du vill aktivera långsiktig lagring av Azure AD-loggar måste du antingen lägga till dem i din befintliga SIEM-lösning eller använda [Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor). Arkivloggar som kan användas som en del av dina incidentsvarsplaner och utredningar.

#### <a name="logs-recommended-reading"></a>Loggar rekommenderad läsning

- [Azure Active Directory-gransknings-API-referens](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference)
- [API-referens för Azure Active Directory-inloggningsaktivitetsrapport](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-sign-in-activity-reference)
- [Hämta data med hjälp av Azure AD Reporting-API:et med certifikat](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-with-certificates)
- [Microsoft Graph för Azure Active Directory-identitetsskydd](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)
- [API-referens för Office 365 Management-aktivitet](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference)
- [Så här använder du innehållspaketet för Azure Active Directory Power BI](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

## <a name="summary"></a>Sammanfattning

Det finns 12 aspekter på en säker identitetsinfrastruktur. Den här listan hjälper dig att skydda och hantera autentiseringsuppgifter ytterligare, definiera autentiseringsupplevelse, delegera tilldelning, mäta användning och definiera åtkomstprinciper baserat på företagets säkerhetsposition.

- Tilldela ägare till nyckeluppgifter.
- Implementera lösningar för att identifiera svaga eller läckta lösenord, förbättra lösenordshantering och lösenordsskydd och ytterligare skydda användarnas åtkomst till resurser.
- Hantera enheternas identitet för att skydda dina resurser när som helst och från vilken plats som helst.
- Implementera lösenordslös autentisering.
- Tillhandahålla en standardiserad enkel inloggningsmekanism i hela organisationen.
- Migrera appar från AD FS till Azure AD för att möjliggöra bättre säkerhet och mer konsekvent hanterbarhet.
- Tilldela användare till program med hjälp av grupper för att möjliggöra större flexibilitet och förmåga att hantera i stor skala.
- Konfigurera riskbaserade åtkomstprinciper.
- Lås äldre autentiseringsprotokoll.
- Upptäcka och åtgärda bidrag för olagligt samtycke.
- Lås användar- och gruppinställningar.
- Aktivera långsiktig lagring av Azure AD-loggar för felsökning, användningsanalys och kriminaltekniska undersökningar.

## <a name="next-steps"></a>Nästa steg

Kom igång med [operativa kontroller och åtgärder för identitetsstyrning](active-directory-ops-guide-govern.md).
