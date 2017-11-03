---
title: Azure Active Directory PoC Playbook implementering | Microsoft Docs
description: "Utforska och snabbt implementera scenarier för identitets- och åtkomsthantering"
services: active-directory
keywords: Azure active directory, playbook, konceptbevis, PoC
documentationcenter: 
author: dstefanMSFT
manager: asuthar
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: dstefan
ms.openlocfilehash: 10877ee33ec04cf0d350417af59d598eab249aa0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-proof-of-concept-playbook-implementation"></a>Azure Active Directory bevis på koncept Playbook: implementering

## <a name="foundation---syncing-ad-to-azure-ad"></a>Foundation - synkroniserar AD till Azure AD 

En hybrididentitet är grunden för de flesta av de enterprise-kunder som redan har en lokal katalog. Avsikten är att avsiktligt ägna mindre tid som går att visa värdet för de faktiska identitets- och scenarierna. 

| Scenario | Byggblock| 
| --- | --- |  
| [Utöka din lokala identiteter till molnet](#extending-your-on-premises-identity-to-the-cloud) | [Katalogsynkronisering - Hash-synkronisering](active-directory-playbook-building-blocks.md#directory-synchronization---password-hash-sync-phs---new-installation) <br/>**Obs**: Om du redan har DirSync-ADSync eller tidigare versioner av Azure AD Connect detta steg är valfritt. Vissa scenarier i den här guiden kan kräva en nyare version av Azure AD Connect.  <br/>[Anpassning](active-directory-playbook-building-blocks.md#branding) | 
| [Tilldela licenser för Azure AD med hjälp av grupper](#assigning-azure-ad-licenses-using-groups) | [Grupp baserad licensiering](active-directory-playbook-building-blocks.md#group-based-licensing) |


### <a name="extending-your-on-premises-identity-to-the-cloud"></a>Utöka din lokala identiteter till molnet 

1. Bob är Active Directory-administratör på Contoso. Han hämtar kravet på att aktivera identitet som en tjänst för en uppsättning användare. Efter körning av Azure AD Connect-guiden, identiteten för målgruppsanvändare som är tillgängliga i molnet. 
2. Bob frågar Susie en målgruppsanvändare att komma åt åtkomstpanelen Azure Active Directory och bekräfta att hon kan autentiseras. Susie ser en företagsanpassad inloggningssida och ett tomt åtkomstpanelen som är klar för att aktivera framtida programåtkomst.

### <a name="assigning-azure-ad-licenses-using-groups"></a>Tilldela licenser för Azure AD med hjälp av grupper 

1. Bob är den Azure AD-administratör och vill tilldela licenser för Azure AD att en specifik uppsättning användare som en del av den första installationen av Azure AD.
2. Bob skapar en grupp för pilotanvändarna. 
3. Bob tilldelar licenser till gruppen
4. Susie en informationsanställda, har lagts till i säkerhetsgruppen som en del av sin jobbfunktioner
5. Efter en stund har Susie åtkomst till Azure AD premium-licens. Detta gör att flera av POC scenarier vid ett senare tillfälle.

## <a name="theme---lots-of-apps-one-identity"></a>Tema - mängd appar, en identitet

| Scenario | Byggblock| 
| --- | --- |  
| [Integrera SaaS-program - federerad enkel inloggning](#integrate-saas-applications---federated-sso) | [Konfiguration för SaaS-federerad enkel inloggning](active-directory-playbook-building-blocks.md#saas-federated-sso-configuration) <br/>[Grupper – delegerad ägarskap](active-directory-playbook-building-blocks.md#groups---delegated-ownership) |
| [Integrera SaaS-program - lösenord för enkel inloggning](#integrate-saas-applications---password-sso) | [SaaS-lösenord SSO-konfiguration](active-directory-playbook-building-blocks.md#saas-password-sso-configuration) |
| [Enkel inloggning och Identity Lifecycle Events](#sso-and-identity-lifecycle-events) | [SaaS och identitet livscykel](active-directory-playbook-building-blocks.md#saas-and-identity-lifecycle) |
| [Säker åtkomst till delade konton](#secure-access-to-shared-accounts) | [SaaS delad konfiguration för konton](active-directory-playbook-building-blocks.md#saas-shared-accounts-configuration) |
| [Säker fjärråtkomst till lokala program](#secure-remote-access-to-on-premises-applications) | [Appen proxykonfiguration](active-directory-playbook-building-blocks.md#app-proxy-configuration) |
| [Synkronisera LDAP identiteter till Azure AD](#synchronize-ldap-identities-to-azure-ad) |  [Allmän LDAP Connector-konfiguration](active-directory-playbook-building-blocks.md#generic-ldap-connector-configuration) |

### <a name="integrate-saas-applications---federated-sso"></a>Integrera SaaS-program - federerad enkel inloggning 

1. Bob är den Azure AD-administratör och tar emot en begäran från marknadsföringsavdelningen att aktivera åtkomst till sina ServiceNow-instans. Bob hittar stegvisa självstudier i Azure AD-dokumentationen och följer det och delegerar tilldelningen av användare i appen till Kevin head för marknadsföring team. 
2. Kevin loggar in som ägare av ServiceNow rättigheter och tilldelar Susie till appen. Kevin meddelanden även att Susies profilen har skapats i ServiceNow automatiskt
3. Susie är en informationsanställd i marknadsföringsavdelningen. Hon loggar in på azure AD och söker efter alla SaaS-program som hon har tilldelats i myapps portal. Därifrån kan får hon sömlöst åtkomst till ServiceNow.
4. Marknadsföringsavdelningen vill granska vem som öppnat ServiceNow. Bob laddar ned en rapport och delar det med Kevin via e-post.  

### <a name="sso-and-identity-lifecycle-events"></a>Enkel inloggning och Identity Lifecycle Events

1. Susie tar för och av företagspolicy lokal AD-kontot är tillfälligt inaktiverad. Susie nu kan inte logga in till Azure AD och därför åtkomst till inte ServiceNow. 
2. Susie blir en lateral förflyttning marknadsföringskampanjer för försäljning. Kevin tar bort sin åtkomst från ServiceNow. Susie loggar i azure ad-myapps och ser hon inte längre ServiceNow-panelen. Efter 10 minuter bekräftar Kevin att Susie konto har inaktiverats ServiceNow-hanteringskonsolen.

### <a name="integrate-saas-applications---password-sso"></a>Integrera SaaS-program - lösenord för enkel inloggning

1. Johan konfigurerar åtkomst till Atlassian HipChat. HipChat har lösenord SSO-integrering och bevilja åtkomst till Susie
2. Susie loggar in på portalen myapps och ser en länk för att hämta webbläsartillägget Azure AD-IE som hon hämtar
3. När du klickar på, hämtar hon ange sitt HipChat användarnamn och lösenord för autentiseringsuppgifter. Detta är en engångsåtgärd och när du har slutfört den hon har åtkomst till HipChat
4. Några dagar senare Susie öppnas myapps portal och klickar på HipChat igen. Den här gången runt får hon sömlös åtkomst
5. Kevin HipChat app ägare, vill granska vem som har åtkomst till programmet. Bob hämtas en kontrollrapport och delar det med Kevin via e-post. 

### <a name="secure-access-to-shared-accounts"></a>Säker åtkomst till delade konton 

1. Bob gett för att skydda delade Twitter-referens för medlemmar i försäljnings-teamet. Han lägger till Twitter som ett SSO-program och tilldelar den till säkerhetsgruppen för försäljning-teamet. Han fick autentiseringsuppgifterna till det delade kontot och han anger den i systemet. 
2. Delning av autentiseringsuppgifter för Twitter inte längre är betrodd på grund av flera personer att känna till det. Bob aktiverar automatisk förnyelse av Twitter-lösenord.
3. Susie medlem i försäljningsgrupp, loggar in på portalen myapps och ser en länk för att hämta Azure AD-IE webbläsartillägg. Hon installerar den.
4. När du klickar på Hämta hon åtkomst direkt till Twitter. Hon vet inte lösenordet.
5. Arnold ingår också i säljgruppen. Han har samma upplevelse som Susie i steg 3 och 4
6. Försäljningsavdelningen vill granska vem som öppnat Twitter. Bob laddar ned en rapport och delar det med Kevin via e-post. 

### <a name="secure-remote-access-to-on-premises-applications"></a>Säker fjärråtkomst till lokala program

1. Bob, Azure AD globala administratören, har tagit emot ett stort antal begäranden om att ge anställda åtkomst till flera användbara lokala resurser, till exempel programmet utgifter när du arbetar via fjärranslutning. Han följer den [Application Proxy dokumentationen](active-directory-application-proxy-enable.md) att installera en koppling och publicera kostnader som ett program med Application Proxy. 
2. Bob dela externa utgifter programmets URL med Susie en av de anställda som behöver fjärråtkomst. Hon har åtkomst till länken och efter att autentisera mot AAD hon kan att komma åt appen kostnader och fortsätter att vara produktiva när remote. 
3. Bob fortsätter sedan att publicera ytterligare lokala program använder samma process och ge åtkomst till användare efter behov. Han lägger till villkorlig åtkomst och multifaktorautentisering för känsliga program som han publicerar för att säkerställa ytterligare säkerhet.

### <a name="synchronize-ldap-identities-to-azure-ad"></a>Synkronisera LDAP identiteter till Azure AD

1. Bobs företaget har komplexa identitetsinfrastrukturen. De flesta användarna bevaras i Windows Server Active Directory Domain Services (lägger till). Några av dem, hanteras av HR-system i Active Directory Lightweight Directory Services (ADLDS).
2. Bob är har behörighet att aktivera åtkomst till SaaS-appar för alla användare (även dessa inte finns i ADDS).
3. Johan konfigurerar allmän LDAP Connector som hämtar data från ADLDS i Azure AD Connect.
4. Bob skapar Synkroniseringsregler så LDAP-användare fylls i metaversum och till Azure AD
5. Susie som LDAP-användare kommer åt sitt SaaS appen med synkroniserade identitet



> [!IMPORTANT] 
> Det här är en avancerad konfiguration kräver bekant med FIM/MIM. Om används i produktionen, rekommenderar vi frågor om den här konfigurationen gå igenom [Premier Support](https://support.microsoft.com/premier).



## <a name="theme---increase-your-security"></a>Tema - Öka säkerheten 

| Scenario | Byggblock| 
| --- | --- |  
| [Säker administratörsåtkomst för kontot](#secure-administrator-account-access) | [Azure MFA med telefonsamtal](active-directory-playbook-building-blocks.md#azure-multi-factor-authentication-with-phone-calls) |
| [Säker åtkomst för program](#secure-access-to-applications) | [Villkorlig åtkomst för SaaS-program](active-directory-playbook-building-blocks.md#mfa-conditional-access-for-saas-applications) |
| [Aktivera bara In Time-administration](#enable-just-in-time-jit-administration) | [Privileged Identity Management](active-directory-playbook-building-blocks.md#privileged-identity-management-pim) |
| [Skydda identiteter baserat på risk](#protect-identities-based-on-risk) | [Identifiering av riskhändelser](active-directory-playbook-building-blocks.md#discovering-risk-events) <br/>[Distribuera inloggning risk principer](active-directory-playbook-building-blocks.md#deploying-sign-in-risk-policies) |
| [Autentisering utan lösenord med certifikatbaserad autentisering](#authenticate-without-passwords-using-certificate-based-authentication) | [Konfigurera certifikatbaserad autentisering](active-directory-playbook-building-blocks.md#configuring-certificate-based-authentication)

### <a name="secure-administrator-account-access"></a>Säker administratörsåtkomst för kontot

1. Bob är den Azure AD-administratör. Han har identifierat Stuart som medadministratör av tjänsten. 
2. Johan konfigurerar Stuarts konto för att alltid kräva MFA för att förbättra säkerhetstillståndet
3. Stuart loggar in på Azure-portalen och meddelanden som han måste registrera sina telefonnummer om du vill fortsätta inloggningen
4. Efterföljande inloggningar från Stuart är nu skyddade med Multifaktorautentisering, och han nu får ett telefonsamtal för att verifiera sin identitet.

### <a name="secure-access-to-applications"></a>Säker åtkomst till program

1. Kevin är ägaren av ServiceNow. Företaget vill nu dessa användare att logga in med MFA för att få åtkomst till utanför företagsnätverket.
2. Bob, vår Global Azure AD-administratör lägger till en princip för villkorlig åtkomst till ServiceNow-programmet för att aktivera MFA för extern åtkomst
3. Susie våra informationsarbetare loggar i Mina appar portal och klickar på panelen ServiceNow. Hon anropas nu med MFA.

### <a name="enable-just-in-time-jit-administration"></a>Aktivera bara i JIT (time) administration

1. Bob och Stuart är Azure AD globala administratörer. De vill att aktivera JIT-åtkomst till av hanteringsroller, samt att hålla poster på användningen av Privilegierade roller.
2. Bob gör PIM i Azure AD-klient och blir säkerhetsadministratör. Ändrar han både själv och rollmedlemskap för Stuart's global administratör från permanent till berättigade.
3. Bob och Stuart kräver nu aktivera rollen via Azure-portalen innan du gör några ändringar till Azure AD-konfiguration. 

### <a name="protect-identities-based-on-risk"></a>Skydda identiteter baserat på risk 

1. Susie, en informationsanställd försöker logga in i en tor-webbläsare. 
2. Bob kontrollerar Azure AD identity protection instrumentpanelen och ser Susies inloggning från en anonym IP-adress. Säkerhet gruppen vill utmaning sådana har tillgång till användare med MFA
3. Bob aktiverar Azure AD Identity Protection-principen till kräver MFA för medelstora eller högre riskhändelser
4. Tiden och Susie loggas Tor webbläsaren igen. Den här tiden kan ser hon MFA-kontrollen

### <a name="authenticate-without-passwords-using-certificate-based-authentication"></a>Autentisering utan lösenord med certifikatbaserad autentisering

1. Bob är Global administratör för finansinstitut som tillåter inte användning av lösenord som en autentiseringsfaktor för sina program.
2. Bob gör och tillämpar certifikatautentisering på AD FS och Azure AD
3. Susie vid åtkomst till programmet uppmanas att autentisera med certifikat

## <a name="theme---scale-with-self-service"></a>Tema - skala med självbetjäning

| Scenario | Byggblock| 
| --- | --- |  
| [Lösenordsåterställning via självbetjäning](#self-service-password-reset) | [Lösenordsåterställning via självbetjäning](active-directory-playbook-building-blocks.md#self-service-password-reset) |
| [Självbetjäningsportalen åtkomst till program](#self-service-access-to-applications) | [Självbetjäningsportalen åtkomst till program](active-directory-playbook-building-blocks.md#self-service-access-to-application-management) |

### <a name="self-service-password-reset"></a>Lösenordsåterställning via självbetjäning 

1. Bob är Global administratör för Azure AD och möjliggör Self Service lösenordshantering till en delmängd av användare, inklusive Susie. 
2. Susie loggar i myapps portal och se ett meddelande om att registrera sin säkerhetsinformation för framtida lösenord återställa händelser.
3. Snabb framåt några dagar Susie glömmer sitt lösenord och återställer via Azure AD-portalen

### <a name="self-service-access-to-applications"></a>Självbetjäningsportalen åtkomst till program 

1. Kevin är ägaren av ServiceNow. Han vill användarna att ”logga” för den på begäran, i stället för att lägga till dem på samma gång
2. Bob, vår Global Azure AD-administratör ändrar ServiceNow-program så att begäranden för självbetjäningsportalen
3. Susie våra informationsarbetare loggar i Mina appar portal och klickar på knappen ”Lägg till fler program” och visa ServiceNow som en av de rekommenderade program. Sedan hon gå tillbaka till Mina appar portal och se ServiceNow-program.

[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]