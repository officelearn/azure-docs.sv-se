---
title: Förstå en värld utan lösen ord med Azure Active Directory | Microsoft Docs
description: 'Den här guiden hjälper CEOs, CIO: er, ciso, Chief Identity Architects, Enterprise Architects och IT-besluts fattare som ansvarar för att välja en lösenordsskyddad autentiseringsmetod för Azure Active Directory implementering.'
keywords: lösen ords azuread,
author: martincoetzer
ms.author: martinco
ms.date: 07/09/2019
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.workload: identity
ms.openlocfilehash: 30c2eb2964f11b729b0ffaf58c532d5189cdc2cd
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727761"
---
# <a name="a-world-without-passwords-with-azure-active-directory"></a>En värld utan lösen ord med Azure Active Directory

Det är dags att dela upp relationen med lösen ord. Lösen ord har varit bra tidigare, men i dagens digitala arbets plats har de blivit en relativt enkel attack vektor för hackare. Hackare älskar lösen ord och det är inte svårt att se varför när du anser att de vanligaste lösen orden i Azure Active Directory (Azure AD) innehåller termer som år, månad, säsong eller ett lokalt idrotts lag. Dessutom [har forskningen visat](https://aka.ms/passwordguidance) att traditionella rekommendationer för lösen ords hantering, till exempel längd krav, komplexitets krav och ändrings frekvenser är counterproductive av olika orsaker som rör mänsklig natur.

Tre typer av attacker som ofta används för att kompromettera användar konton är lösen ords sprutning, nätfiske och brotts omuppspelning. Azure AD-funktioner som [Smart utelåsning](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout), [förbjudna lösen ord](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises)och [lösen ords skydd](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises) kan skydda mot de här typerna av attacker. På samma sätt ger implementering av [Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) (MFA) eller tvåstegsverifiering en ytterligare säkerhet genom att kräva en andra form av autentisering. Men i lång körning är en snabbare lösning den bästa lösningen för att säkerställa den säkraste metoden för autentisering.

Den här artikeln är början av resan för att hjälpa dig att förstå och implementera Microsofts lösningar för lösen ords lösa och hjälpa dig att välja mellan ett eller flera av följande alternativ:

* **Windows Hello för företag**. I Windows 10 ersätter Windows Hello för företag lösen ord med stark tvåfaktorautentisering på datorer och mobila enheter. Den här autentiseringen består av en ny typ av användar referenser som är knutna till en enhet och använder bio metrisk eller PIN-kod.

* **Inloggning med lösen ord med Microsoft Authenticator**. Microsoft Authenticator-appen kan användas för att logga in på ett Azure AD-konto utan att använda ett lösen ord. På samma sätt som med Windows Hello för företag, använder Microsoft Authenticator nyckelbaserad autentisering för att aktivera autentiseringsuppgifter som är knutna till en enhet och använder bio metrisk eller PIN-kod.

* **FIDO2 säkerhets nycklar**. FIDO2 tillhandahåller autentiseringsuppgifter för kryptografisk inloggning som är unika för alla webbplatser och lagras på en lokal enhet som Windows Hello eller externa säkerhets nycklar. Dessa säkerhets nycklar är motstånds kraftiga mot risker med nätfiske, lösen ords stöld och repetitions attacker. Kombinerat med användar verifiering via biometrik eller PIN-kod, är lösningen två faktorer som uppfyller modern säkerhets behov.

## <a name="the-future-of-passwordless-authentication"></a>Framtiden för lösen ords verifiering

Dessa dagar, banker, kredit korts företag och andra organisationer och onlinetjänster ofta skyddar ditt konto genom att kräva att du verifierar din identitet två gånger: en gång med ditt lösen ord, sedan via telefon, text eller en app. Även om Multi-Factor Authentication tar itu med säkerhets problem med lösen ord som delas, blir stulna eller gissade, tar det inte upp besvärs faktorn för att försöka komma ihåg dem. De användare och organisationer som vill ha dagens moln era är lösenordsskyddade autentiseringsmetoder som är mycket säkra *och* praktiska.

![Bekvämlighet vs Security](./media/ad-passwordless/azure-ad-pwdless-image1.png)

Windows Hello för företag, lösen ords lös inloggning med Microsoft Authenticator och FIDO2-säkerhetsnycklar delar en enkel, gemensam arkitektur som ger användare en autentiseringsmetod som är både mycket säker och bekväm att använda. Alla tre baseras på offentlig/privat nyckel teknik, behöver en lokal gest, till exempel en bio metrisk eller PIN-kod, och privata nycklar som är kopplade till en enskild enhet och lagras säkert och aldrig delas.

## <a name="windows-hello-for-business"></a>Windows Hello för företag

I Windows 10 ersätter Windows Hello för företag lösen ord med stark tvåfaktorautentisering på datorer och enheter. Autentiseringen består av en ny typ av användarautentisering som är kopplad till en enhet och använder en bio metrisk gest eller PIN-kod som gör det möjligt för användare att autentisera till Azure AD samt en lokal Active Directory. Att bara logga in på en enhet med Windows Hello för företag är enkelt. Du använder antingen PIN-kod eller bio metrisk gest, till exempel finger avtryck eller ansikts igenkänning.

### <a name="windows-hello-for-business-scenarios"></a>Windows Hello för företag-scenarier

Windows Hello för företag är idealiskt för informations anställda som har sin egen angivna Windows-dator. Bio måttet och autentiseringsuppgifterna är direkt knutna till användarens dator, vilket förhindrar åtkomst från någon annan än ägaren. Med PKI-integrering och inbyggt stöd för enkel inloggning (SSO) är Windows Hello för företag en enkel och smidig metod för sömlös åtkomst till företagets resurser lokalt och i molnet.

### <a name="windows-hello-for-business-deployment-considerations"></a>Överväganden för distribution av Windows Hello för företag

Windows Hello för företag är ett distribuerat system som använder flera komponenter för att utföra registrering av enheter, etablering och autentisering. Därför kräver distributionen korrekt planering över flera team i organisationen. [Planerings guiden](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) för Windows Hello för företag kan användas för att fatta beslut om en typ av Windows Hello för företag-distribution och vilka alternativ du behöver tänka på.

Det finns många alternativ som du kan välja när du distribuerar Windows Hello för företag. Att tillhandahålla flera alternativ garanterar att nästan alla organisationer kan distribuera Windows Hello för företag. Tänk på följande typer av distributioner som stöds:

* [Distribution av hybrid Azure AD-ansluten nyckel förtroende](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-key-trust)

* [Distribution av hybrid Azure AD-anslutna certifikat](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust)

* [Distributions guider för enkel inloggning med Azure AD Join](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso)

* [Distribution av lokal nyckel förtroende](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-key-trust)

* [Distribution av lokalt förtroende för certifikat](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-cert-trust)

Att tillhandahålla många alternativ gör distributionen komplex. De flesta organisationer kommer dock troligt vis att avgöra att de redan har implementerat det mesta av den infrastruktur som Windows Hello för företag-distributionen är beroende av. Oavsett vilket är det viktigt att förstå att Windows Hello för företag är ett distribuerat system och att lämplig planering rekommenderas.

Vi rekommenderar att du läser [Planera en Windows Hello för företag-distribution](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) för att hjälpa dig att avgöra vilken distributions modell som passar bäst för din organisation. Sedan, baserat på den planering du gör, läser du [distributions guiden för Windows Hello för företag](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide) för att säkerställa en lyckad distribution av Windows Hello för företag i din befintliga miljö.

### <a name="how-windows-hello-for-business-works"></a>Så här fungerar Windows Hello för företag

#### <a name="user-sets-up-windows-hello-for-business"></a>Användare konfigurerar Windows Hello för företag

Efter en inledande tvåstegsverifiering för användaren under registreringen, konfigureras Windows Hello på användarens enhet och Windows ber användaren att ställa in en gest, som kan vara ett biometric, till exempel ett finger avtryck eller ansikts igenkänning eller en PIN-kod. När användaren har angetts ger användaren gesten för att verifiera sin identitet. Windows använder sedan Windows Hello för att autentisera användare.

Baserat på funktionerna i din Windows 10-enhet har du antingen en inbyggd säker enklaven, som kallas TPM (Trusted Platform Module) eller en programvaru-TPM. TPM: en lagrar den privata nyckeln, som kräver antingen ditt ansikte, finger avtryck eller PIN-kod för att låsa upp den. Bio metriska data är inte roamade och skickas aldrig till externa enheter eller servrar. Det finns ingen samlad plats som en angripare kan kompromettera för att stjäla bio metriska data, eftersom Windows Hello endast lagrar bio metriska identifierings data på enheten.

> [!TIP]
> På ytan är en PIN-kod som verkar som ett lösen ord, men det är i själva verket säkrare. En viktig skillnad mellan ett lösen ord och en PIN-kod är att PIN-koden är kopplad till den enhet där den har kon figurer ATS. Någon som stjäl ditt lösen ord kan logga in på ditt konto var som helst. Men om de stjäl din PIN-kod måste de stjäla din fysiska enhet för! Dessutom, eftersom en PIN-kod är lokal för enheten, överförs den inte var som helst, så den kan inte fångas upp i överföring eller stulits från en server.

#### <a name="user-using-windows-hello-for-business-for-sign-in"></a>Användare som använder Windows Hello för företag för inloggning

Windows Hello för företag-biometrik och-PIN använder asymmetrisk (offentlig/privat nyckel) kryptering för autentisering. Under autentiseringen är krypteringen kopplad till TLS-sessionsnyckeln, som skyddar autentiseringsprocessen så att en man-in-the-Middle (MiTM)-attack inte kan stjäla den resulterande säkerhetstoken eller artefakten och spela upp den från andra platser.

Windows Hello för företag är en bekväm inloggnings upplevelse som autentiserar användaren för Azure AD och Active Directory resurser. Azure AD-anslutna enheter autentiseras för Azure vid inloggning och kan också autentisera till Active Directory. Hybrid Azure Active Directory anslutna enheter autentiseras för att Active Directory vid inloggning och autentisera till Azure Active Directory i bakgrunden.

![Se käll avbildningen](./media/ad-passwordless/azure-ad-pwdless-image2.jpeg)

Följande steg illustrerar inloggnings autentiseringen för Azure Active Directory.

![Windows 10-Lås skärm](./media/ad-passwordless/azure-ad-pwdless-image3.png)

1. Användaren loggar in i Windows med bio metrisk eller PIN-kod. Gesten låser upp den privata nyckeln för Windows Hello för företag och skickas till Cloud authentication-security support provider som kallas för moln-AP-providern.

2. Cloud AP-providern begär en nonce från Azure Active Directory.

3. Azure AD returnerar en nonce som är giltig i 5 minuter.

4. Molnets AP-Provider signerar nonce med hjälp av användarens privata nyckel och returnerar signerad nonce till Azure Active Directory.

5. Azure Active Directory verifierar signerad nonce med hjälp av användarens säkert registrerade offentliga nyckel mot signaturen för nonce. När du har validerat signaturen verifierar Azure AD den returnerade signerade nonce. När du har verifierat nonce skapar Azure AD en PRT med en sessionsnyckel som är krypterad med enhetens transport nyckel och tillbaka den till molnets AP-Provider.

6. Cloud AP-providern tar emot den krypterade PRT med sessionsnyckeln. Med hjälp av enhetens privata transport nyckel dekrypterar Cloud AP-providern sessionsnyckeln och skyddar sessionsnyckeln med enhetens TPM.

7. Molnets AP-Provider returnerar ett lyckat verifierings svar för Windows efter vilken användaren har åtkomst till Windows såväl som moln program och lokala program utan att du behöver autentisera igen (SSO).

En djupare titt på autentiseringsprocessen i andra scenarier som omfattar Windows Hello för företag finns i [Windows Hello för företag och autentisering](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-how-it-works-authentication#azure-ad-join-authentication-to-active-directory-using-a-key).

#### <a name="user-manages-their-windows-hello-for-business-credentials"></a>Användaren hanterar sina Windows Hello för företag-autentiseringsuppgifter

[Microsofts tjänster för PIN-återställning](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-features#pin-reset) är en funktion i Azure AD som gör det möjligt för användare att återställa sina PIN-koder vid behov. Med hjälp av en grup princip, Microsoft Intune eller en kompatibel MDM, kan en administratör konfigurera Windows 10-enheter för att säkert använda Microsofts tjänst för PIN-återställning som gör det möjligt för användare att återställa sina glömt PIN-koder via inställningar eller ovanför Lås skärmen utan att kräva omregistrering.

Ibland måste användare använda lösen ord. [Lösen ords återställning](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment) via självbetjäning (SSPR) är en annan Azure AD-funktion som gör det möjligt för användare att återställa sina lösen ord utan att behöva kontakta IT-personalen. Användare måste registreras för eller registreras för lösen ords återställning via självbetjäning innan tjänsten kan användas. Vid registreringen väljer användaren en eller flera autentiseringsmetoder som har Aktiver ATS av organisationen. SSPR gör det möjligt för användarna att snabbt få tag i blockering och fortsätta att arbeta oavsett var de befinner sig eller tiden på dagen. Genom att tillåta användare att avblockera sig själva kan din organisation minska den icke produktiva tiden och höga support kostnader för de flesta vanliga problem som rör lösen ord.

## <a name="passwordless-sign-in-with-microsoft-authenticator"></a>Inloggning med lösen ord med Microsoft Authenticator

Lösen ords lös inloggning med Microsoft Authenticator är en annan lösen ords lös lösning som kan användas för att logga in på Azure AD-konton med hjälp av telefonin loggning. Du måste fortfarande verifiera din identitet genom att tillhandahålla något du känner till och något du har, men med telefonin loggning kan du hoppa över att ange ditt lösen ord och utföra all din identitets verifiering på din mobila enhet med ditt finger avtryck, ansikte eller PIN-kod.

### <a name="microsoft-authenticator-passwordless-scenarios"></a>Microsoft Authenticator lösen ords bara scenarier

Microsoft Authenticator-appen gör det möjligt för användare att verifiera sin identitet och autentisera till sitt arbete eller personliga konto. Det kan också användas för att utöka ett lösen ord med ett eng ång slö sen ord eller push-meddelande eller ersätta behovet av ett lösen ord helt och hållet. I stället för att använda ett lösen ord bekräftar användarna sin identitet med hjälp av sin mobil telefon med finger avtrycks skanning, ansikts-eller iris eller PIN-kod. Verktyget är byggt på säker teknik som liknar vad Windows Hello använder, men det är ett enkelt sätt att använda det här verktyget i en enkel app på en mobil enhet. Microsoft Authenticator-appen är tillgänglig för Android och iOS.

### <a name="microsoft-authenticator-deployment-considerations"></a>Microsoft Authenticator distributions överväganden

Krav för att använda den Microsoft Authenticator appen för lösen ords lös inloggning i Azure AD inkluderar följande:

* Slutanvändare är aktiverade för Azure Multi-Factor Authentication

* Möjligheten för användare att registrera sina enheter med hjälp av Microsoft Intune eller en lösning för hantering av mobila enheter från tredje part (MDM)

Förutsatt att dessa krav uppfylls, kan administratörer Aktivera inloggning med lösen ord i klienten med hjälp av [Windows PowerShell.](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#enable-my-users) När telefonin loggning har Aktiver ATS i klient organisationen kan slutanvändare välja att logga in med telefonen genom att välja sitt arbets-eller skol konto på skärmen **konton** i appen och sedan välja **Aktivera telefonin loggning**.

Förutsatt att lösen ords lös inloggning har Aktiver ATS av en administratör måste slutanvändarna uppfylla följande krav:

* Registrerad i Azure Multi-Factor Authentication

* Den senaste versionen av Microsoft Authenticator installerad på enheter som kör iOS 8,0 eller senare, eller Android 6,0 eller senare

* Arbets-eller skol konto med push-meddelanden tillagda i appen

För att undvika möjligheten att bli utelåst från ditt konto eller om du behöver återskapa konton på en ny enhet, rekommenderar vi att du använder Microsoft Authenticator för att [säkerhetskopiera dina konto uppgifter](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-backup-recovery) till molnet. När du har säkerhetskopierat kan du också använda appen för att återställa din information på en ny enhet, vilket kan undvika att bli utelåst eller behöver återskapa konton.

Eftersom de flesta användare bara är vana vid att använda lösen ord för att autentisera, är det viktigt att organisationen utlovar användare om den här processen. Medvetenheten kan minska sannolikheten för att användarna kontaktar supportavdelningen för [problem](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#known-issues) som rör inloggning med hjälp av Microsoft Authenticator-appen.

> [!NOTE]
> En möjlig felpunkt för den här lösningen är när en nätverks växlings användare finns på en plats där det inte finns någon Internet anslutning. FIDO2 säkerhets nycklar och Windows Hello för företag omfattas inte av samma begränsning.

### <a name="how-passwordless-sign-in-with-microsoft-authenticator-works"></a>Så här loggar du in utan lösen ord med Microsoft Authenticator fungerar

#### <a name="user-sets-up-passwordless-sign-in-with-microsoft-authenticator"></a>Användare konfigurerar inloggning med lösen ord med Microsoft Authenticator

Innan Microsoft Authenticator-appen kan användas som en lösen ords lös lösning för att logga in på ett Azure AD-konto måste steg utföras av både administratörer och slutanvändarna.

Först måste administratören [aktivera användningen av appen som autentiseringsuppgifter](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#enable-my-users) i klienten med hjälp av Windows PowerShell. Administratören måste också aktivera slutanvändare för Azure Multi-Factor Authentication (Azure MFA) och konfigurera Microsoft Authenticator-appen som en av verifierings [metoderna](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings#verification-methods).

Slutanvändare måste [Ladda ned och installera](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) Microsoft Authenticator-appen och [Konfigurera sitt konto](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app) för att använda Microsoft Authenticator-appen som en av verifierings metoderna.

> [!VIDEO https://www.youtube.com/embed/uWbkLuI4g30]

#### <a name="user-using-microsoft-authenticator-for-passwordless-sign-in"></a>Användare som använder Microsoft Authenticator för lösen ords registrering

Microsoft Authenticator-appen kan användas för att logga in på ett Azure AD-konto utan att använda ett lösen ord. Även om sidan för Windows 10-lås inte innehåller Microsoft Authenticator appen som ett inloggnings alternativ kan användarna fortfarande ange sitt användar namn och sedan få ett push-meddelande på sin mobila enhet för att verifiera närvaron. Användarna bekräftar sin närvaro genom att matcha ett tal på inloggnings skärmen och sedan ange en ansikts skanning, finger avtryck eller PIN-kod för att låsa upp den privata nyckeln och slutföra autentiseringen. Den här Multi-Factor Verification-metoden är säkrare än ett lösen ord och praktiskt än att ange ett lösen ord och en kod.

![Autentisera inloggning](./media/ad-passwordless/azure-ad-pwdless-image4.png)

Autentisering utan lösen ord med Microsoft Authenticator följer samma grundläggande mönster som Windows Hello för företag, men det är lite mer komplicerat eftersom användaren måste identifieras så att Azure AD kan hitta Microsoft Authenticator-appens version används.

![Autentiserare-process](./media/ad-passwordless/azure-ad-pwdless-image5.png)

1. Användaren anger sitt användar namn.

2. Azure AD identifierar att användaren har starka autentiseringsuppgifter och startar det starka flödet av autentiseringsuppgifter.

3. Ett meddelande skickas till appen via Apple Push Notification Service (APN) på iOS-enheter eller via Firebase Cloud Messaging (FCM) på Android-enheter.

4. Användaren får push-meddelandet och öppnar appen.

5. Appen anropar Azure AD och tar emot en utmanings-och nonce-begäran.

6. Användaren slutför utmaningen genom att ange sitt bio mått eller PIN-kod för att låsa upp den privata nyckeln.

7. Nonce signeras med den privata nyckeln och skickas tillbaka till Azure AD.

8. Azure AD utför verifiering av offentliga/privata nycklar och returnerar en token.

#### <a name="user-manages-their-passwordless-sign-in-with-microsoft-authenticator-credentials"></a>Användaren hanterar inloggning med lösen ord med Microsoft Authenticator autentiseringsuppgifter

Med [kombinerad registrering](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined)kan användarna registrera sig och få fördelarna med både Azure Multi-Factor Authentication och lösen ords återställning via självbetjäning. Användarna registrerar och hanterar dessa inställningar genom att gå till [min profil sida](https://aka.ms/mysecurityinfo). Förutom att aktivera SSPR stöder kombinerad registrering flera autentiseringsmetoder och-åtgärder.

## <a name="fido2-security-keys"></a>FIDO2 säkerhets nycklar

FIDO2 är den senaste versionen av FIDO Alliance standard och har två Components-W3C's Web Authentication (webauthn) och motsvarande FIDO Alliance client-to-Authenticator Protocol (CTAP2). FIDO2-standarder gör det möjligt för användare att använda maskinvaru-, mobil-och biometrik-baserade autentiserare för att enkelt autentisera med många appar och webbplatser i mobila och Station ära miljöer.

Microsoft och bransch partner har arbetat tillsammans på FIDO2-säkerhetsenheter för Windows Hello för att möjliggöra enkel och säker autentisering på delade enheter. Med FIDO2 säkerhets nycklar kan du överföra dina autentiseringsuppgifter med dig och på ett säkert sätt till en [Azure AD](https://aka.ms/azuread418)-ansluten Windows 10-enhet som tillhör din organisation.

Webauthn definierar ett API som möjliggör utveckling och implementering av stark, lösenordsbaserad autentisering av webbappar och tjänster. CTAP-protokollet gör det möjligt för externa enheter, till exempel FIDO säkerhets nycklar, att arbeta med webauthn och fungerar som autentiserare. Med Webbautentisering kan användare logga in på onlinetjänster med sina ansikts-, finger avtryck-, PIN-eller Portable FIDO2-säkerhetsnycklar, med hjälp av starka autentiseringsuppgifter för offentliga nycklar i stället för lösen ord. För närvarande stöds webauthn i Microsoft Edge och support för Chrome och Firefox i utvecklingen.

Enheter och token som följer FIDO2-, webauthn-och CTAP-protokollen tar upp en plattforms oberoende lösning med stark autentisering utan att använda lösen ord. Microsoft-partner arbetar på en rad olika säkerhets nyckel former, till exempel USB-säkerhetsnycklar och NFC-aktiverade smartkort.

### <a name="fido2-security-keys-scenarios"></a>FIDO2 säkerhets nyckel scenarier

FIDO2-säkerhetsnycklar kan användas för att logga in på Azure AD genom att välja säkerhets nyckeln som Credential-Provider på Lås skärmen för Windows 10. Det krävs inget användar namn eller lösen ord, vilket gör det till en idealisk lösning för första rad arbetare som delar datorer mellan flera användare. De är också ett utmärkt autentiseringsalternativ när företagets principer anger att en användares autentiseringsuppgifter måste vara fysiskt åtskilda från sina enheter. Användare kan också välja att logga in på webbplatser med hjälp av deras FIDO2-säkerhetsnyckel i Microsoft Edge-webbläsaren på Windows 10 version 1809 eller senare.

### <a name="fido2-security-keys-deployment-considerations"></a>Överväganden vid distribution av FIDO2 säkerhets nycklar

Administratörer kan aktivera FIDO2-stöd i Azure AD och tilldela funktioner till användare eller grupper. Principer kan också skapas för hur nycklar ska tillhandahållas och konfigurera begränsningar, till exempel tillåta eller blockera en speciell uppsättning maskin varu säkerhets nycklar. Nycklar måste distribueras fysiskt till slutanvändare.

**Kraven för att aktivera lösen ords lös inloggning på Azure AD och webbplatser med hjälp av FIDO2 säkerhets nycklar är följande:**

* Azure AD

* Azure Multi-Factor Authentication

* För hands version av kombinerad registrering

* För hands versionen av FIDO2 Security Key krävs en kompatibel säkerhets nyckel för FIDO2

* Web Authentication (webauthn) kräver Microsoft Edge på Windows 10 version 1809 eller senare

* FIDO2-baserad Windows-inloggning kräver Azure AD-ansluten Windows 10 version 1809 eller senare (den bästa upplevelsen är i Windows 10 version 1903 eller senare)

FIDO2-kompatibla form faktorer är bland annat USB-, NFC-och Bluetooth-enheter. Vi rekommenderar att du väljer den form faktor som uppfyller dina krav, eftersom vissa plattformar och webbläsare inte är FIDO2-kompatibla.

Vi rekommenderar också att varje organisation skapar ett protokoll för användare och administratörer att följa, om en säkerhets nyckel skulle försvinna eller bli stulen. Användarna bör rapportera den förlorade eller stulna nyckeln så att administratörer eller användare kan ta bort sina säkerhets nycklar från användarens profil och etablera en ny.

### <a name="how-fido2-security-keys-works"></a>Så här fungerar säkerhets nycklar för FIDO2

#### <a name="user-sets-up-fido2-security-key"></a>Användare ställer in säkerhets nyckel för FIDO2

Administratörer kan [manuellt etablera nycklar](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-enable) och distribuera dem till slutanvändare, och etablering och aktivering av FIDO2 på Windows 10-Lås-skärmen kommer att stödjas via [Intune](https://docs.microsoft.com/intune/windows-enrollment-methods). Administratörer måste också använda [Azure Portal](https://portal.azure.com/) för att aktivera maskinvaru-token-enheter som en metod för lösen ords lös autentisering.

Att distribuera FIDO2 säkerhets nycklar kräver också att användarna registrerar sina nycklar med hjälp av [kombinerad registrering](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined). Med kombinerad registrering kan användarna registrera sig en gång och få fördelarna med både Azure Multi-Factor Authentication och återställning av lösen ord för enkel inloggning (SSPR).

Förutom att välja maskinvaru-token som standard metod för Multi-Factor Authentication, rekommenderar vi att du även väljer ett ytterligare verifierings alternativ.

* Microsoft Authenticator--meddelande

* Authenticator-app eller maskinvarubaserad token-Code

* Telefonsamtal

* Textmeddelande

#### <a name="user-using-fido2-security-key-for-sign-in"></a>Användare som använder FIDO2 säkerhets nyckel för inloggning

FIDO2 tillhandahåller ett abstraktions lager mellan den form faktor som används som autentiserare och offentlig/privat nyckel kryptering för att aktivera inbyggda plattforms autentiserare, till exempel Windows Hello-och säkerhets nycklar för att matcha till en privat nyckel och leverera en offentlig nyckel som kan användas som identifierare för att komma åt externa resurser. FIDO2-säkerhetsnycklar är utrustade med sina egna inbyggda säkra enklaven som lagrar den privata nyckeln och kräver bio metrisk eller PIN-kod för att låsa upp den. Autentiseringsuppgifterna kan inte återanvändas, spelas upp eller delas mellan tjänster och omfattas inte av phishing-och MiTM-attacker eller Server intrång.

![FIDO2-inloggning](./media/ad-passwordless/azure-ad-pwdless-image6.png)

FIDO2 säkerhets nycklar ger säker autentisering, oberoende av form faktorn. Säkerhets nyckeln innehåller autentiseringsuppgifterna och bör skyddas med ytterligare en andra faktor, till exempel ett finger avtryck (integrerat i säkerhets nyckeln) eller en PIN-kod som ska anges vid Windows-inloggning. Microsoft-partner arbetar på en rad olika säkerhets nyckel former. Några exempel är USB-säkerhetsnycklar och NFC-aktiverade smartkort.

> [!NOTE]
> En säkerhets nyckel måste implementera vissa funktioner och tillägg från FIDO2 CTAP-protokollet som ska vara [Microsoft-kompatibelt](https://aka.ms/fido2securitykeys). Microsoft har testat dessa lösningar för kompatibilitet med Windows 10 och Azure Active Directory.

![FIDO2 inloggnings process](./media/ad-passwordless/azure-ad-pwdless-image9.png)

1. Användaren ansluter FIDO2-enheten till datorn.

2. Windows identifierar säkerhets nyckeln FIDO2.

3. Windows skickar en autentiseringsbegäran.

4. Azure AD skickar tillbaka en nonce.

5. Användaren har slutfört sin gest för att låsa upp den privata nyckel som lagras i säkerhets nyckelns säkra enklaven-FIDO2.

6. Säkerhets nyckeln FIDO2 signerar nonce med den privata nyckeln.

7. PRT token-begäran med signerat nonce skickas till Azure AD.

8. Azure AD verifierar signerad nonce med hjälp av den offentliga nyckeln FIDO2.

9. Azure AD returnerar PRT för att ge åtkomst till lokala resurser.

#### <a name="user-manages-their-fido2-security-key-credentials"></a>Användaren hanterar sina FIDO2 säkerhets nyckel uppgifter

I likhet med Microsoft Authenticator-appen använder hantering av autentiseringsuppgifter för FIDO2 säkerhets nycklar en kombinerad registrerings upplevelse för slutanvändarna.

## <a name="deciding-a-passwordless-method"></a>Bestämma en metod för lösen ords aven

Om du väljer mellan dessa tre lösen ords avskrivna alternativ beror ditt företags krav på säkerhet, plattform och appar.

Här följer några faktorer som du bör tänka på när du väljer Microsoft Password-mindre teknik:

||**Windows Hello för företag**|**Lösen ords återsignering med Microsoft Authenticator-appen**|**FIDO2 säkerhets nycklar**|
|:-|:-|:-|:-|
|**Krav**| Windows 10, version 1809 eller senare<br>Azure Active Directory| Microsoft Authenticator-app<br>Telefon (iOS-och Android-enheter som kör Android 6,0 eller senare)|Windows 10, version 1809 eller senare<br>Azure Active Directory|
|**Offline**|Plattform|Programvara|Maskinvara|
|**System och enheter**|PC med inbyggd Trusted Platform Module (TPM)<br>PIN-kod och biometrik-igenkänning |PIN-kod och biometrik-igenkänning på telefon|FIDO2 säkerhets enheter som är Microsoft-kompatibla|
|**Användar upplevelse**|Logga in med en PIN-kod eller bio metrisk igenkänning (ansikts, iris eller finger avtryck) med Windows-enheter.<br>Windows Hello-autentisering är knuten till enheten. användaren behöver både enheten och en inloggnings komponent som en PIN-kod eller bio metrisk faktor för att få åtkomst till företags resurser.|Logga in med en mobil telefon med finger avtrycks skanning, ansikts-eller iris eller PIN-kod.<br>Användarna loggar in på arbetet eller det personliga kontot från sin dator eller mobil telefon.|Logga in med FIDO2-säkerhetsenheten (biometrik, PIN och NFC)<br>Användaren kan komma åt enheten baserat på organisations kontroller och autentiseras baserat på PIN-kod, biometrik med enheter som USB-säkerhetsnycklar och NFC-aktiverade smartkort, nycklar eller wearables.|
|**Aktiverade scenarier**| Lösen ords lös upplevelse med Windows-enheter.<br>Gäller för dedikerade arbets datorer med möjlighet till enkel inloggning till enhet och program.|Lösen ords lös lösningen överallt med mobil telefon.<br>Gäller för åtkomst till arbets-eller personliga program på webben från vilken enhet som helst.|Lösen ords lös upplevelse för arbetare som använder biometrik, PIN och NFC.<br>Gäller för delade datorer och där en mobil telefon inte är ett lämpligt alternativ (t. ex. för support personal, offentlig kiosk eller sjukhus lag)|

Använd följande tabell för att välja vilken metod som ska ha stöd för dina krav och användare.

|Person|Scenario|Miljö|Teknik med lösen ords teknik|
|:-|:-|:-|:-|
|**Admin**|Säker åtkomst till en enhet för hanterings uppgifter|Tilldelad Windows 10-enhet|Windows Hello för företag och/eller FIDO2 säkerhets nyckel|
|**Admin**|Hanterings uppgifter på icke-Windows-enheter| Mobil eller icke-Windows-enhet|Lösen ords återsignering med Microsoft Authenticator-appen|
|**Informations arbetare**|Produktivitets arbete|Tilldelad Windows 10-enhet|Windows Hello för företag och/eller FIDO2 säkerhets nyckel|
|**Informations arbetare**|Produktivitets arbete| Mobil eller icke-Windows-enhet|Lösen ords återsignering med Microsoft Authenticator-appen|
|**Frontline Worker**|Kiosker i en fabriks-, anläggnings-, detalj-eller data ingång|Delade Windows 10-enheter|FIDO2 säkerhets nycklar|

## <a name="getting-started"></a>Komma igång

Lösen ords lös autentisering är den långsammare i framtiden och sökvägen till en säkrare miljö. Vi rekommenderar att organisationer börjar planera för den här ändringen och minskar deras beroenden av lösen ord. Tänk på följande om du vill komma igång:

* Aktivera användare för MFA + Microsoft Authenticator app + villkorlig åtkomst.

* Distribuera Azure AD Password Protection + uppdaterings principer.

* Aktivera användare för SSPR med kombinerad registrering.

* Distribuera Microsoft Authenticator app för mobilitet.

* Distribuera Windows Hello för företag (1903: Håll dig uppdaterad).

* Distribuera FIDO2-enheter för användare som inte kan använda telefoner.

* Inaktivera lösenordsbaserad autentisering när det är möjligt.

För att uppnå dessa mål rekommenderar vi följande:

1. Aktivera Azure Active Directory för att dra full nytta av funktioner som Azure MFA och villkorlig åtkomst.

2. Aktivera Multi-Factor Authentication för att ge ytterligare skydd.

3. Aktivera lösen ords återställning via självbetjäning i händelse användarna måste gå tillbaka till lösen ordet.

4. Distribuera Microsoft Authenticator telefonin loggning för extra rörlighet.

5. Distribuera Windows Hello för företag till alla dina Windows 10-enheter.

6. Förbered dig för säkerhets nycklar för FIDO2.

> [!NOTE]
> På [sidan Azure Active Directory licensiering](https://azure.microsoft.com/pricing/details/active-directory/) finns mer information om licensierings kraven för lösen ords lös metoder.

## <a name="conclusion"></a>Sammanfattning

Under de senaste åren har Microsoft fortsatt engagemang för att aktivera en värld utan lösen ord. Med Windows 10 lanserade Microsoft Windows Hello för företag, en stark, maskin vara som skyddas av två faktorer som möjliggör enkel inloggning till Azure Active Directory och Active Directory. På samma sätt som med Windows Hello för företag, använder Microsoft Authenticator-appen nyckelbaserad autentisering för att aktivera autentiseringsuppgifter som är knutna till en mobil enhet och använder bio metrisk eller PIN-kod. Med FIDO2 säkerhets nycklar kan du överföra dina autentiseringsuppgifter med dig och logga in på Azure AD genom att välja säkerhets nyckeln som Credential-Provider på Lås skärmen för Windows 10. Alla tre av dessa lösningar för lösen ords lösa minskar risken för nätfiske, lösen ords sprutning och repetitions attacker och ger användare ett mycket säkert och bekvämt sätt att logga in och komma åt data från var som helst.

Införandet av modern Multi-Factor Authentication-teknik, till exempel biometrik och offentlig nyckel kryptografi i mycket tillgängliga enheter, är en av de mest påverkade stegen som kan minska ett företags identitets risker. Att gå med lösen ords skydd är en långsiktig metod för säker autentisering, och den håller fortfarande på att utvecklas. Med tanke på nya krav kan organisationer förbereda sig själva genom att göra en plan för att börja flytta till lösen ords lös teknik.

## <a name="next-steps"></a>Nästa steg

* En översikt över [vad som är lösen ord?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless)
* [Så här aktiverar du ett lösen ord i Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-enable)
