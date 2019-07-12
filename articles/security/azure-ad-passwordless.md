---
title: Förstå en värld utan lösenord med Azure Active Directory | Microsoft Docs
description: 'Den här guiden hjälper koncernchefer, CIO: er, Ciso, Chief identitet arkitekter, Enterprise-arkitekter och säkerhet och IT-beslutsfattare som är ansvarig för att välja en lösenordslös autentiseringsmetod för deras Azure Active Directory-implementering.'
services: active-directory
keywords: passwordless, azuread
author: martincoetzer
ms.author: martinco
ms.date: 07/09/2019
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.openlocfilehash: 00f66b6010bead3de131095a47ba1e419d2511c0
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723446"
---
# <a name="a-world-without-passwords-with-azure-active-directory"></a>En värld utan lösenord med Azure Active Directory

Det är dags att dela upp din relation med lösenord. Lösenord har haft det bra att vi tidigare, men i dagens digitala arbetsplats de blivit en relativt enkel angreppsvinkel för hackare. Hackare älskar lösenord och är inte svårt att se varför när du ska välja som mest nekade lösenord i Azure Active Directory (Azure AD) innehåller villkor som året, månaden, säsongen eller en lokal Sports går samman. Dessutom [forskning har visat](https://aka.ms/passwordguidance) att traditionella rekommendationer för lösenordshantering som längdkraven, krav på komplexitet och ändra frekvenser kontraproduktiva av olika skäl rör Human natur.

Tre typer av angrepp som ofta används för att angripa användarkonton är besprutningsmedel för lösenord, nätfiske och intrång spela upp. Azure AD-funktioner som [smart kontoutelåsning](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout), [lösenord som förbjuds](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises), och [lösenordsskydd](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises) kan skydda mot dessa typer av attacker. På samma sätt kan implementera [multifaktorautentisering](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) (MFA), eller tvåstegsverifiering, ger ökad säkerhet genom att kräva ett andra formen av autentisering. Men på lång sikt, en lösning för konfiguration av lösenordsfri är den bästa lösningen för att säkerställa att den säkraste metoden för autentisering.

Den här artikeln är i början av din resa mot att förstå och implementera Microsofts lösningar för konfiguration av lösenordsfri och hjälper dig att du väljer mellan en eller flera av följande alternativ:

* **Windows Hello för företag**. I Windows 10 ersätter Windows Hello för företag lösenord med stark tvåfaktorsautentisering på datorer och mobila enheter. Den här autentiseringen består av en ny typ av autentiseringsuppgifter som är kopplad till en enhet och använder en biometriska eller PIN-kod.

* **Konfiguration av lösenordsfri inloggning med Microsoft Authenticator**. Microsoft Authenticator-appen kan användas för att logga in på en Azure AD-konto utan lösenord. Liknande teknik i Windows Hello för företag, Microsoft Authenticator använder nyckel-baserad autentisering för att aktivera en autentiseringsuppgift för användare som är kopplad till en enhet och använder en biometriska eller PIN-kod.

* **FIDO2 säkerhetsnycklar**. FIDO2 tillhandahåller kryptografiska inloggningsuppgifterna som är unika för varje webbplats och lagras på en lokal enhet som Windows Hello eller externa säkerhetsnycklar. Dessa säkerhetsnycklar är motståndskraftiga mot riskerna för nätfiske, lösenord stöld och replay-attacker. Lösningen är kombineras med verifiering av användare via biometrik eller PIN-kod, två faktorer verifiering möte moderna säkerhetskrav.

## <a name="the-future-of-passwordless-authentication"></a>Framtiden för konfiguration av lösenordsfri autentisering

Dessa dagar, banker, kreditkortsföretag, och andra organisationer och onlinetjänster ofta skydda ditt konto genom att kräva att du kan verifiera din identitet två gånger: en gång med hjälp av ditt lösenord, via telefon, text eller en app sedan igen. När multifaktorautentisering åtgärdar säkerhetsproblem av lösenord som delas, behandlar blir stulen eller gissa, den inte faktorn ber om ursäkt för att försöka komma ihåg dem. Vad användare och organisationer vill ha i dagens molneran är lösenordslös autentiseringsmetoder som är mycket säkra *och* praktiskt.

![Bekvämlighet vs-säkerhet](./media/azure-ad/azure-ad-pwdless-image1.png)

Windows Hello för företag, konfiguration av lösenordsfri inloggning med Microsoft Authenticator och FIDO2 säkerhetsnycklar alla delar en arkitektur med enkla, vanliga som ger användarna en autentiseringsmetod som är både mycket säker och praktisk att använda. Alla tre bygger på offentliga/privata nycklar som används behöver en lokal gest som en biometriska eller PIN-kod och privata nycklar som är bundna till en enskild enhet och på ett säkert sätt lagras och delas aldrig.

## <a name="windows-hello-for-business"></a>Windows Hello för företag

I Windows 10 ersätter Windows Hello för företag lösenord med stark tvåfaktorsautentisering på datorer och enheter. Autentiseringen består av en ny typ av autentiseringsuppgifter som är kopplad till en enhet och använder en biometriska gest eller PIN-kod som gör att användarna kan autentisera till Azure AD samt en lokal Active Directory. Bara logga in en enhet med Windows Hello för företag är enkelt. Du använda antingen en PIN-kod eller biometriska gester, till exempel ett fingeravtryck eller videodetektion erkännande.

### <a name="windows-hello-for-business-scenarios"></a>Windows Hello för företag-scenarier

Windows Hello för företag är perfekt för informationsarbetare som har sina egna avsedda Windows-dator. Den biometriska och autentiseringsuppgifterna är direkt knuten till användarens dator, vilket hindrar åtkomst från alla utom ägaren. Med PKI-integrering och inbyggt stöd för enkel inloggning (SSO) tillhandahåller Windows Hello för företag en enkel och smidig metod för att sömlöst komma åt företagets resurser lokalt och i molnet.

### <a name="windows-hello-for-business-deployment-considerations"></a>Windows Hello för företag distributionsöverväganden

Windows Hello för företag är ett distribuerat system som använder flera komponenter för att uppnå enhetsregistrering, etablering och autentisering. Därför kräver distribution noggrann planering i flera team i organisationen. Till Windows Hello för företag [Planeringsguiden](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) kan användas för att hjälpa dig att fatta beslut på vilken typ av Windows Hello för företag-distribution och de alternativ som du behöver tänka på.

För hybrid- eller lokala distributioner förväntas det att du har:

* Ett väl anslutet fungerande nätverk

* Internet-åtkomst

* Multi-Factor Authentication-servern stöder MFA under Windows Hello för företag-etablering

* Korrekt namnmatchning, både interna och externa namn

* Active Directory och tillräckligt många domänkontrollanter per plats för att stödja autentisering

* Active Directory Certificate Services 2012 eller senare

* En eller flera arbetsstationer som kör Windows 10, version 1903

Eftersom alla typer av Windows Hello för företag-distributioner via enterprise-utfärdade certifikat för domänkontrollanter som förtroenderoten, du kan automatiskt förnya utgångna certifikat av [konfigurera automatisk registrering för server och en användare certifikat](https://docs.microsoft.com/windows-server/networking/core-network-guide/cncg/server-certs/configure-server-certificate-autoenrollment). För lokala distributioner är identitetsprovidern den lokala servern som kör Windows Server Active Directory Federation Services (AD FS)-rollen. Federerad system kräver normalt en Utjämning av nätverksbelastning-matris av servrar, kallas även en servergrupp. Den här gruppen har konfigurerats i ett internt nätverk och perimeternätverket Nätverkstopologi för att säkerställa hög tillgänglighet för begäranden om autentisering.

När du anger en grupprincip för att kräva Windows Hello för företag på en arbetsplats kan du förbereda användare i din organisation genom att förklara hur du använder Windows Hello. Till exempel när någon ställer in en ny enhet, uppmanas de att välja mellan **den här enheten tillhör min organisation** eller **det här är min egen personliga enhet**. De bör välja den tidigare versionen för företagets enheter. Användarna måste också ett meddelande om vilket de bör välja anslutningsalternativ för: **Ansluta till Azure AD** eller **konfigurera ett lokalt konto (senare domänanslutning)** .

Det är vanligt för användare som är vana vid med en biometriska gester för att autentisera dag efter dag för att så småningom glömmer sina PIN-koder. Om du vill undvika samtal till supportavdelningen, rekommenderar vi att du ge användare möjlighet att återställa glömt [lösenord](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment) och [PIN-koder](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-features#pin-reset).

Det finns många alternativ som du kan välja när du distribuerar Windows Hello för företag. Med flera olika alternativ säkerställer nästan alla organisation kan distribuera Windows Hello för företag. Överväg följande typer av distributioner som stöds:

* [Hybrid Azure AD-ansluten viktiga förtroende distribution](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-key-trust)

* [Hybrid Azure AD-ansluten distribution av servercertifikat förtroende](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust)

* [Azure AD Join enkel inloggning distributionsguider](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso)

* [För plats viktiga förtroende distribution](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-key-trust)

* [On Premises förtroende certifikatdistributionen](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-cert-trust)

Tillhandahåller många alternativ gör distributionen visas komplexa. Men avgör de flesta organisationer sannolikt att de redan har infört de flesta av den infrastruktur som till Windows Hello för företag-distribution är beroende av. Oavsett, är det viktigt att förstå att Windows Hello för företag är ett distribuerat system och noggrann planering rekommenderas.

Vi rekommenderar att du läser [planera Windows Hello för företag distribution](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) för att avgöra på vilken distributionsmodell som bäst passar för din organisation. Sedan, baserat på den planering som du kan referera till den [Windows Hello för företag-distributionsguiden](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide) för att säkerställa en lyckad distribution av Windows Hello för företag i din befintliga miljö.

### <a name="how-windows-hello-for-business-works"></a>Hur Windows Hello för företag fungerar

#### <a name="user-sets-up-windows-hello-for-business"></a>Användare har konfigurerat Windows Hello för företag

Windows Hello ställs in på användarens enhet efter en inledande tvåstegsverifiering för användaren under registreringen, och Windows uppmanar användaren att ange en gest, som kan vara en biometrisk, till exempel ett fingeravtryck eller videodetektion erkännande eller en PIN-kod. När anger användaren gest för att verifiera sin identitet. Windows använder sedan Windows Hello för att autentisera användare.

Baserat på funktionerna i Windows 10-enheten kan har du antingen en inbyggd säker enklaven kallas en maskinvara trusted platform module (TPM) eller en TPM-programvara. TPM lagras den privata nyckeln, som kräver din ansikte, fingeravtryck eller PIN-kod för att låsa upp den. Biometriska data flyttas inte och skickas aldrig till externa enheter eller servrar. Det finns ingen enskild samling en angripare kan påverka för att stjäla biometriska data eftersom Windows Hello lagrar bara biometriska identifieringsdata på enheten.

> [!TIP]
> En PIN-kod fungerar som ett lösenord på ytan, men det är faktiskt säkrare. En viktig skillnad mellan ett lösenord och en PIN-kod är att PIN-koden är knutna till den enhet där det har ställts in. Någon som stjäl ditt lösenord kan logga in på ditt konto från var som helst. Men om de kan stjäla din PIN-kod, de skulle behöva stjäl din fysiska enhet för! Dessutom eftersom en PIN-kod är lokala för enheten kan skickas den inte var som helst så att den inte kan fångas upp i överföringen eller blir stulen från en server.

#### <a name="user-using-windows-hello-for-business-for-sign-in"></a>Användare som använder Windows Hello för företag för att logga in

Windows Hello för företag biometrik och PIN-koder använder asymmetriska (offentliga och privata nycklar) kryptering för autentisering. Under autentiseringen är krypteringen knutna till sessionsnyckeln TLS som skyddar autentiseringsprocessen så att en man-in-the-middle-attack för (MiTM) det går inte att stjäla resulterande säkerhetstoken eller artefakt och spela upp den från andra platser.

Windows Hello för företag är det praktiskt inloggning som autentiserar användaren till Azure AD och Active Directory-resurser. Azure AD-anslutna enheter autentisera till Azure vid inloggning och du kan också kan autentisera till Active Directory. Hybrid Azure Active Directory-anslutna enheter autentiseras till Active Directory under logga in och autentisera till Azure Active Directory i bakgrunden.

![Se Källavbildningen](./media/azure-ad/azure-ad-pwdless-image2.jpeg)

Följande steg illustrerar autentiseringen logga in till Azure Active Directory.

![Windows 10-låsskärmen](./media/azure-ad/azure-ad-pwdless-image3.png)

1. Användaren loggar in med hjälp av Windows biometric eller PIN-kod gest. Gesten låser upp till Windows Hello för företag privat nyckel och skickas till i Molnautentisering security supportprovider, kallas molnet AP-providern.

2. Molnet AP-providern begär en nonce från Azure Active Directory.

3. Azure AD returnerar en nonce som är giltig i fem minuter.

4. Molnet AP-providern loggar nonce med hjälp av den privata nyckeln och returnerar den signerade nonce till Azure Active Directory.

5. Azure Active Directory verifierar signerade nonce med hjälp av användarens på ett säkert sätt registrerade offentliga nyckel mot tillfälligt signaturen. När du har validerat signaturen verifierar sedan Azure AD returnerade signerade nonce. När du har validerat temporärt ID, skapar en PRT med sessionsnyckel som krypteras till enhetsnyckel transport och returnerar det till molnet AP-providern i Azure AD.

6. Molnet AP providern tar emot krypterad PRT med sessionsnyckel. Med privata transport enhetsnyckel molnet AP providern dekrypterar sessionsnyckeln och skyddar nyckeln med hjälp av enhetens TPM.

7. Molnet AP providern returnerar ett svar för lyckad autentisering till Windows efter vilken användaren kan komma åt Windows samt molnet och lokala program utan att du behöver för att autentisera igen (SSO).

En närmare titt på autentiseringsprocessen i andra scenarier som omfattar Windows Hello för företag finns [Windows Hello för företag och autentisering](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-how-it-works-authentication#Azure-AD-join-authentication-to-Active-Directory-using-a-Key).

#### <a name="user-manages-their-windows-hello-for-business-credentials"></a>Användaren hanterar sina Windows Hello för företag-autentiseringsuppgifter

Den [Microsoft PIN-återställning tjänster](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-features#pin-reset) är en funktion i Azure AD som gör att användarna kan återställa PIN-om det behövs. Med Grupprincip, Microsoft Intune eller en kompatibel MDM, kan en administratör konfigurera Windows 10-enheter om du vill använda tjänsten för återställning av Microsoft PIN som låter användare återställa glömda PIN via inställningar eller ovanför låsskärmen utan att på ett säkert sätt omregistrering.

Ibland behöver användarna återgår till att använda lösenord. [Lösenordsåterställning via självbetjäning](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment) (SSPR) är en annan Azure AD-funktion som gör att användarna kan återställa sina lösenord utan att behöva kontakta IT-personal. Användarna måste registrera dig för eller vara registrerad för självbetjäning för återställning av lösenord innan du använder tjänsten. Under registreringen väljer användaren en eller flera autentiseringsmetoder som aktiverats av organisationen. SSPR kan du snabbt få blockeringen och fortsätta arbeta oavsett var de är eller tid på dagen. Genom att tillåta användare att avblockera själva, kan din organisation minska icke-produktiva tiden och kostnaderna för hög support för de vanligaste lösenord-relaterade problem.

## <a name="passwordless-sign-in-with-microsoft-authenticator"></a>Konfiguration av lösenordsfri inloggning med Microsoft Authenticator

Konfiguration av lösenordsfri inloggning med Microsoft Authenticator är en annan konfiguration av lösenordsfri lösning som kan användas för att logga in på Azure AD-konton med hjälp av telefoninloggning. Du måste fortfarande verifiera din identitet genom att ange något användaren vet och något du har, men phone inloggning kan du hoppa över att ange ditt lösenord och utför alla identitetsverifiering på din mobila enhet med hjälp av ditt fingeravtryck, ett ansikte eller en PIN-kod.

### <a name="microsoft-authenticator-passwordless-scenarios"></a>Microsoft Authenticator lösenordslös scenarier

Microsoft Authenticator-appen kan du verifiera sin identitet och autentisera till deras arbete eller personligt konto. Det kan också användas för att utöka ett lösenord med en engångskod eller push-meddelande eller ersätta behovet av ett lösenord helt och hållet. Användaren bekräfta sin identitet med sin mobiltelefon via fingeravtryck genomsökning, videodetektion eller iris eller PIN-kod istället för att använda ett lösenord. Bygger på säker teknik som liknar vad Windows Hello används och är det här verktyget paketerat i en enkel app på en mobil enhet, vilket gör det ett enkelt alternativ för användare. Microsoft Authenticator-appen är tillgänglig för Android och iOS.

### <a name="microsoft-authenticator-deployment-considerations"></a>Överväganden vid distribution av Microsoft Authenticator

Följande är krav för att använda Microsoft Authenticator-appen för att göra lösenordslös inloggning i Azure AD:

* Slutanvändare har aktiverats för Azure Multi-Factor Authentication

* Möjligheten för användare att registrera sina enheter med Microsoft Intune eller en lösning för hantering av mobila enheter från tredje part

Under förutsättning att dessa krav är uppfyllda, administratörer aktivera lösenordslös inloggning via mobilen i klienten med hjälp av [Windows PowerShell.](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#enable-my-users) När telefoninloggning har aktiverats i klienten, slutanvändare kan välja för att logga in med telefonen genom att välja sitt arbets- eller skolkonto konto på den **konton** skärmen i appen, välj sedan **aktivera telefoninloggning** .

Anta att lösenordslös inloggning är aktiverad som en administratör och måste slutanvändare du uppfylla följande krav:

* Har registrerats i Azure Multi-Factor Authentication

* Senaste versionen av Microsoft Authenticator installerade på enheter som kör iOS 8.0 eller senare eller Android 6.0 eller senare

* Arbets- eller skolkonto konto med push-meddelanden som har lagts till i appen

Att undvika potentiella komma låst slut på ditt konto eller att skapa konton på en ny enhet, vi rekommenderar att du använder Microsoft Authenticator till [säkerhetskopiera dina kontouppgifter](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-backup-recovery) till molnet. Efter säkerhetskopieringen, kan du också använda appen för att återställa din information på en ny enhet potentiellt undvika komma låst upp eller att skapa konton.

Eftersom de flesta användare är vana vid att använda endast lösenord för autentisering, är det viktigt att din organisation utbildar användare om den här processen. Medvetenhet kan minska sannolikheten att användarna ska ringa supportavdelningen för någon [problem](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#known-issues) rör inloggning i med hjälp av Microsoft Authenticator-appen.

> [!NOTE]
> En potentiell felpunkt för den här lösningen är när en central användaren finns på en plats där det finns ingen Internet-anslutning. FIDO2 säkerhetsnycklar och Windows Hello för företag omfattas inte av samma begränsningar.

### <a name="how-passwordless-sign-in-with-microsoft-authenticator-works"></a>Hur lösenordslös inloggning med Microsoft Authenticator fungerar

#### <a name="user-sets-up-passwordless-sign-in-with-microsoft-authenticator"></a>Användare har konfigurerat lösenordslös inloggning med Microsoft Authenticator

Innan Microsoft Authenticator-appen kan användas som en lösenordslös lösning för att logga in på en Azure AD-konto, måste steg utföras av både en administratör och slutanvändare.

En administratör måste först [aktivera användningen av appen som en autentiseringsuppgift](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#enable-my-users) i klienten med hjälp av Windows PowerShell. Administratören måste också aktivera slutanvändare för Azure Multi-Factor Authentication (Azure MFA) och konfigurera Microsoft Authenticator-appen som en av de [verifieringsmetoder](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings#verification-methods).

Slutanvändare behöver [ladda ned och installera](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) Microsoft Authenticator-appen och [ställa in sitt konto](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app) att använda Microsoft Authenticator-appen som en av verifieringsmetoderna.

> [!VIDEO https://www.youtube.com/embed/uWbkLuI4g30]

#### <a name="user-using-microsoft-authenticator-for-passwordless-sign-in"></a>Användare som använder Microsoft Authenticator för konfiguration av lösenordsfri inloggning

Microsoft Authenticator-appen kan användas för att logga in på alla Azure AD-konto utan lösenord. Även om Windows 10-låsskärmen inte innehåller Microsoft Authenticator-appen som ett alternativ för inloggning kan användare fortfarande ange sitt lösenord och ta emot ett push-meddelande på sina mobila enheter att kontrollera förekomst. Användaren bekräfta sin närvaro genom matchar ett tal på skärmen inloggning och sedan att tillhandahålla ett ansikte genomsökning, fingeravtryck eller PIN-kod för att låsa upp den privata nyckeln och slutför autentiseringen. Den här multifaktorverifiering-metoden är säkrare än ett lösenord och enklare än att ange ett lösenord och en kod.

![Authenticator-inloggning](./media/azure-ad/azure-ad-pwdless-image4.png)

Konfiguration av lösenordsfri autentisering med hjälp av Microsoft Authenticator följer samma grundläggande mönster som Windows Hello för företag, men är lite mer komplicerat eftersom användaren måste identifieras så att Azure AD kan hitta Microsoft Authenticator app version som används.

![Authenticator-processen](./media/azure-ad/azure-ad-pwdless-image5.png)

1. Användaren anger sitt användarnamn.

2. Azure AD upptäcker att användaren har en stark autentiseringsuppgift och startar starka autentiseringsuppgifter flöde.

3. Meddelande skickas till appen via Apple Push Notification Service (APNS) på iOS-enheter eller via Firebase Cloud Messaging (FCM) på Android-enheter.

4. Användaren tar emot push-meddelande och öppnar appen.

5. Appen anropar Azure AD och tar emot en proof-of-presence utmaning och nonce.

6. Användaren Slutför utmaningen genom att ange sina biometriska eller PIN-kod för att låsa upp den privata nyckeln.

7. Nonce signeras med den privata nyckeln och skickas tillbaka till Azure AD.

8. Azure AD utför verifiering av offentligt/privat och returnerar en token.

#### <a name="user-manages-their-passwordless-sign-in-with-microsoft-authenticator-credentials"></a>Användaren som hanterar sina lösenordslös inloggning med Microsoft Authenticator-autentiseringsuppgifter

Med [kombineras registrering](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined), användare kan registrera och få fördelarna med både Azure Multi-Factor Authentication och lösenordsåterställning via självbetjäning. Användarna registrerar och hanterar de här inställningarna genom att gå till sina [min profilsida](https://aka.ms/mysecurityinfo). Förutom att aktivera SSPR kombineras registrering stöder flera autentiseringsmetoder och åtgärder.

## <a name="fido2-security-keys"></a>FIDO2 säkerhetsnycklar

FIDO2 är den senaste versionen av FIDO Alliance standard och har två komponenter: standard för W3C's standardautentisering via webben (WebAuthN) och motsvarande FIDO Alliance klienten till Authenticator-protokollet (CTAP2). FIDO2 standarder kan du dra nytta av maskinvaru-, mobil- och biometrik-baserade autentiserare för att autentisera enkelt med många appar och webbplatser i mobila och stationära miljöer.

Microsoft och dess partner har arbetat tillsammans med FIDO2 säkerhetsenheter för Windows Hello för att aktivera enkel och säker autentisering på delade enheter. FIDO2 säkerhetsnycklar kan du utföra dina autentiseringsuppgifter med dig och på ett säkert sätt autentisera till en [Azure AD](https://aka.ms/azuread418)-domänanslutna Windows 10-enhet som ingår i din organisation.

WebAuthN definierar ett API som gör det möjligt för utveckling och implementering av starka, konfiguration av lösenordsfri autentisering av webbappar och tjänster. Protokollet CTAP gör att externa enheter, till exempel FIDO-kompatibla säkerhetsnycklar att arbeta med WebAuthN och fungera som autentiserare. Webbautentisering kan användare logga in på online-tjänster med sina ansikte, fingeravtryck, PIN-kod eller bärbar FIDO2 säkerhetsnycklar att använda stark identifiering för offentliga nycklar i stället för lösenord. För närvarande WebAuthN stöds i Microsoft Edge och stöd för Chrome och Firefox är under utveckling.

Enheter och token som följer FIDO2 och WebAuthN CTAP protokoll åstadkomma en plattformsoberoende lösning av stark autentisering utan att använda lösenord. Microsoft-partners arbetar på en mängd olika security viktiga formfaktorer, till exempel USB-säkerhetsnycklar och NFC-aktiverad smartkort.

### <a name="fido2-security-keys-scenarios"></a>FIDO2 säkerhetsscenarier för nycklar

FIDO2 säkerhetsnycklar kan användas för att logga in på Azure AD genom att välja säkerhetsnyckeln som provider för autentiseringsuppgifter i Windows 10-låsskärmen. Ett användarnamn eller lösenord är inte obligatoriskt som gör det till en idealisk lösning för första rad arbetare som delar datorer mellan flera användare. De är också ett alternativ för utmärkt autentisering när företagets principer kräver att en användares autentiseringsuppgifter måste vara fysiskt separat från sina enheter. Användare kan också välja att logga in på webbplatser med hjälp av deras FIDO2 säkerhetsnyckel i Microsoft Edge-webbläsaren på Windows 10 version 1809 eller högre.

### <a name="fido2-security-keys-deployment-considerations"></a>FIDO2 säkerhetsnycklar distributionsöverväganden

Administratörer kan aktivera FIDO2 stöd i Azure AD och tilldela användare eller grupper möjligheten. Principerna kan även skapas för hur nycklar har etablerats och konfigurerar begränsningar, till exempel att tillåta eller blockera en specifik uppsättning maskinvara säkerhetsnycklar. Nycklar måste vara fysiskt distribueras till slutanvändare.

**Krav för aktivering av lösenordslös logga in på Azure AD och web sites med FIDO2 säkerhetsnycklar omfattar följande:**

* Azure AD

* Azure Multi-Factor Authentication

* Kombinerade registrerings-förhandsversion

* Viktiga förhandsversion av FIDO2 säkerhet kräver en kompatibel FIDO2 Säkerhetsnyckel

* Webbautentisering (WebAuthN) kräver Microsoft Edge i Windows 10 version 1809 eller senare

* FIDO2 baserat Windows inloggningen kräver Azure AD-anslutna Windows 10 version 1809 eller högre (bästa möjliga upplevelse är Windows 10 version 1903 eller senare)

FIDO2-kompatibla formfaktorer omfattar USB och NFC Bluetooth-enheter. Vi rekommenderar att du väljer formfaktorn som uppfyller dina specifika behov eftersom vissa plattformar och webbläsare är ännu inte FIDO2-kompatibel.

Vi rekommenderar också att varje organisation skapar ett protokoll för användare och administratörer att följa bör en säkerhets-och nyckeln vara tappas bort eller blir stulen. Användare bör rapportera den borttappade eller stulna nyckeln så att administratörer eller användare kan ta bort deras säkerhetsnycklar från användarens profil och etablera en ny.

### <a name="how-fido2-security-keys-works"></a>Så här fungerar FIDO2 säkerhetsnycklar

#### <a name="user-sets-up-fido2-security-key"></a>Användare har konfigurerat FIDO2 Säkerhetsnyckel

Medan administratörer kan [manuellt etablera nycklar](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-enable) och distribuera dem till slutanvändare, etablering och aktivera FIDO2-provider på Windows 10-låsskärmen för autentiseringsuppgifter kommer att stödjas genom [Intune](https://docs.microsoft.com/intune/windows-enrollment-methods). Administratörer måste också använda den [Azure-portalen](https://portal.azure.com/) att aktivera token maskinvaruenheter som en lösenordslös autentiseringsmetod.

Distribuera FIDO2 säkerhetsnycklar kräver också att användare registrerar sina nycklar med hjälp av [kombineras registrering](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined). Med kombinerade registration användare registrera en gång och få fördelarna med både Azure Multi-Factor Authentication och återställning av lösenord för enkel inloggning (SSPR).

Förutom att välja maskinvarutoken som standardmetoden för autentisering med flera faktorer, bör du också välja ett alternativ för ytterligare verifiering.

* Microsoft Authenticator – meddelande

* Authenticator-appen eller maskinvara token – code

* Telefonsamtal

* Textmeddelande

#### <a name="user-using-fido2-security-key-for-sign-in"></a>Användare som använder FIDO2 Säkerhetsnyckel för att logga in

FIDO2 innehåller ett Abstraktionslager mellan formfaktorn som används som verifierare och kryptografi med offentliga/privata nycklar för att aktivera inbyggda plattform autentiserare, till exempel Windows Hello och säkerhet att matcha till en privat nyckel och leverera en offentlig nyckel som kan användas som en identifierare för att få åtkomst till externa resurser. FIDO2 säkerhetsnycklar är utrustade med sina egna inbyggda säker enklaven som lagrar den privata nyckeln och kräver biometriska eller PIN-kod att låsa upp den. Autentiseringsuppgifter inte får återanvändas återupprepas, eller delas mellan tjänster och omfattas inte av nätfiske och MiTM-attacker eller server överträdelser.

![Logga in FIDO2](./media/azure-ad/azure-ad-pwdless-image6.png)

FIDO2 säkerhetsnyckeln för ger säker autentisering, oberoende av formfaktorn. Säkerhetsnyckeln innehåller autentiseringsuppgifterna och bör skyddas med ytterligare en andra faktor som ett fingeravtryck (integrerad i säkerhetsnyckeln) eller en PIN-kod anges på Windows-inloggning. Microsoft-partners arbetar på en mängd olika security viktiga formfaktorer. Några exempel är USB säkerhetsnycklar och NFC aktiverat smartkort.

> [!NOTE]
> En säkerhetsnyckel måste implementera vissa funktioner och tillägg från FIDO2 CTAP protokoll ska [Microsoft-kompatibla](https://aka.ms/fido2securitykeys). Microsoft har testat dessa lösningar för kompatibilitet med Windows 10 och Azure Active Directory.

![FIDO2 inloggningsprocess](./media/azure-ad/azure-ad-pwdless-image9.png)

1. Användaren ansluter FIDO2-enhet till datorn.

2. Windows identifierar FIDO2 säkerhetsnyckel.

3. Windows skickar en autentiseringsbegäran.

4. Azure AD skickar tillbaka en nonce.

5. Användaren slutför sin gest för att låsa upp den privata nyckeln lagras i FIDO2 säkerhetsnyckeln säker enklaven.

6. Säkerhetsnyckel FIDO2 loggar temporärt ID med den privata nyckeln.

7. PRT tokenbegäran med signerade nonce skickas till Azure AD.

8. Azure AD verifierar signerade nonce med hjälp av den offentliga nyckeln för FIDO2.

9. Azure AD returnerar PRT för att ge åtkomst till lokala resurser.

#### <a name="user-manages-their-fido2-security-key-credentials"></a>Användaren hanterar sina viktiga FIDO2 säkerhetsreferenser

Liknar Microsoft Authenticator-appen, hantering av inloggningsuppgifter för FIDO2 säkerhetsnycklar förlitar sig på kombinerade registrerings-upplevelse för slutanvändare.

## <a name="deciding-a-passwordless-method"></a>Bestämma en lösenordslös metod

Välja mellan alternativen för tre lösenordslös beror på företagets säkerhet, plattform och krav för appar.

Här är några faktorer du bör tänka på när du väljer lösenord utan Microsoft-teknik:

||**Windows Hello för företag**|**Konfiguration av lösenordsfri inloggning med Microsoft Authenticator-appen**|**FIDO2 säkerhetsnycklar**|
|:-|:-|:-|:-|
|**Installationsprogrammets**| Windows 10, version 1809 eller senare<br>Azure Active Directory| Microsoft Authenticator-appen<br>Telefon (iOS och Android-enheter med Android 6.0 eller senare.)|Windows 10, version 1809 eller senare<br>Azure Active Directory|
|**läge**|Plattform|Programvara|Maskinvara|
|**System och enheter**|Dator med en inbyggd Trusted Platform Module (TPM)<br>Igenkänning av PIN-kod och biometrik |PIN-kod och biometrik erkännande på telefon|FIDO2 säkerhetsenheter som är kompatibla Microsoft|
|**Användarupplevelse**|Logga in med en PIN-kod eller biometriska erkännande (videodetektion, iris eller fingeravtryck) med Windows-enheter.<br>Windows Hello-autentisering är kopplade till enheten; användaren måste både enheten och en komponent, till exempel PIN-kod eller biometriska faktor komma åt företagets resurser.|Logga in med en mobiltelefon med fingeravtryck genomsökning, videodetektion eller iris av eller fästa.<br>Användare logga in till arbets- eller personligt konto från sina PC eller mobiltelefon.|Logga in med FIDO2 säkerhetsenhet (biometrik, PIN-kod och NFC)<br>Användare kan komma åt enheten utifrån organisation kontroller och autentisera baserat på PIN-kod, biometrik använder enheter som USB säkerhetsnycklar och NFC-aktiverad smartkort, nycklar eller wearables.|
|**Aktiverade scenarier**| Lösenord utan upplevelse med Windows-enhet.<br>Gäller för dedikerade arbetsdator med möjligheten för enkel inloggning till enheter och program.|Lösenord utan var som helst lösning med hjälp av mobiltelefon.<br>Gäller för åtkomst till arbete eller personligt program på webben från valfri enhet.|Lösenord utan upplevelse för anställda med biometrik, PIN-kod och NFC.<br>Gäller för delade datorer och där en mobiltelefon inte är ett genomförbart alternativ (t.ex. för supportpersonal, offentlig dator eller sjukhuset team)|

Använd följande tabell för att välja vilken metod du ska uppfylla dina krav och användare.

|Person|Scenario|Miljö|Konfiguration av lösenordsfri teknik|
|:-|:-|:-|:-|
|**Admin**|Säker åtkomst till en enhet för administrativa uppgifter|Tilldelade Windows 10-enhet|Windows Hello för företag och/eller FIDO2 Säkerhetsnyckel|
|**Admin**|Hanteringsuppgifter på icke-Windows-enheter| Mobil- eller icke-windows-enhet|Konfiguration av lösenordsfri inloggning med Microsoft Authenticator-appen|
|**Informationsarbetare**|Produktivitet arbete|Tilldelade Windows 10-enhet|Windows Hello för företag och/eller FIDO2 Säkerhetsnyckel|
|**Informationsarbetare**|Produktivitet arbete| Mobil- eller icke-windows-enhet|Konfiguration av lösenordsfri inloggning med Microsoft Authenticator-appen|
|**Främsta worker**|Kioskdatorer i en fabrik, anläggning, detaljhandel eller data-post|Delade Windows 10-enheter|FIDO2 säkerhetsnycklar|

## <a name="getting-started"></a>Komma igång

Konfiguration av lösenordsfri autentisering är mobilteknik var framtiden och sökvägen till en säkrare miljö. Vi rekommenderar att organisationer börja planera för den här ändringen och minskar deras beroenden för lösenord. Kom igång genom att beakta följande mål:

* Aktivera användare för MFA, Microsoft Authenticator-appen + villkorlig åtkomst.

* Distribution på Azure AD-lösenordsskydd + uppdateringsprinciper.

* Aktivera användare för SSPR med kombinerade registration.

* Distribuera Microsoft Authenticator-appen för mobilitet.

* Distribuera Windows Hello för företag (1903: Håll dig uppdaterad).

* Distribuera FIDO2 enheter för användare som inte kan använda telefoner.

* När det är möjligt, inaktivera lösenordsbaserad autentisering.

Vi rekommenderar följande metod för att uppnå dessa mål:

1. Aktivera Azure Active Directory för att dra full nytta av funktioner som Azure MFA och villkorlig åtkomst.

2. Aktivera Multi-Factor authentication att ge ytterligare skydd.

3. Aktivera självbetjäningsportalen för lösenordsåterställning i den händelse att användarna behöver för att återgår till att använda ett lösenord.

4. Distribuera Microsoft Authenticator telefoninloggning för mobilitet som har lagts till.

5. Distribuera Windows Hello för företag till alla dina Windows 10-enheter.

6. Förbered för FIDO2 säkerhetsnycklar.

> [!NOTE]
> Referera till Azure Active Directory [licensiering sidan](https://azure.microsoft.com/pricing/details/active-directory/) mer information om licensieringskraven för konfiguration av lösenordsfri metoder.

## <a name="conclusion"></a>Sammanfattning

Under de senaste åren har Microsoft fortsatt åtagandet till att aktivera en värld utan lösenord. Med Windows 10, introducerade Microsoft Windows Hello för företag, ett starkt, maskinvara skyddad tvåfaktors-autentiseringsuppgifter som möjliggör enkel inloggning på Azure Active Directory och Active Directory. Liknande teknik i Windows Hello för företag, Microsoft Authenticator-appen använder nyckel-baserad autentisering för att aktivera en autentiseringsuppgift för användare som är kopplad till en mobil enhet och använder en biometriska eller PIN-kod. Nu kan FIDO2 säkerhetsnycklar du utföra dina autentiseringsuppgifter med dig och logga in på Azure AD genom att välja säkerhetsnyckeln som provider för autentiseringsuppgifter i Windows 10-låsskärmen. Alla tre av följande lösenordslös minska risken för nätfiske, lösenord besprutningsmedel och replay-attacker och ge användarna en mycket säker och praktiskt sätt att logga in och komma åt data var som helst.

Införande av moderna multifaktorautentisering tekniker som biometrik och kryptering med offentlig nyckel i större utsträckning tillgängliga enheter är en av de mest kraftfulla steg som kan meningsfullt att minska risken för ett företags identitet. Gå lösenordslös är en långsiktig metod för säker autentisering och det fortfarande under utveckling. Beroende framväxande krav, kan organisationer förbereda själva genom att göra en plan för att börja migrera till lösenordslös teknik.

## <a name="next-steps"></a>Nästa steg

* En översikt över [vad är lösenordslös?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless)
* [Så här aktiverar du lösenordslös i Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-enable)
