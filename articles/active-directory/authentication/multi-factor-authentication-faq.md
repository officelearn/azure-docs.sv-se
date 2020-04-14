---
title: Vanliga frågor och svar om Azure Multi Factor Authentication – Azure Active Directory
description: Vanliga frågor och svar relaterade till Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d28f93f316ac2a63be6b3a8eb0b80678bd7607f
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81271411"
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Vanliga frågor om Azure Multi-Factor Authentication

Den här vanliga frågor och svar på vanliga frågor om Azure Multi-Factor-autentisering och användning av tjänsten Multi-Factor Authentication. Den är uppdelad i frågor om tjänsten i allmänhet, faktureringsmodeller, användarupplevelser och felsökning.

> [!IMPORTANT]
> Från och med den 1 juli 2019 kommer Microsoft inte längre att erbjuda MFA Server för nya distributioner. Nya kunder som vill kräva multifaktorautentisering från sina användare bör använda molnbaserad Azure Multi-Factor-autentisering. Befintliga kunder som har aktiverat MFA Server före den 1 juli kommer att kunna ladda ner den senaste versionen, framtida uppdateringar och generera aktiveringsautentiseringsuppgifter som vanligt.
>
> Informationen som delas nedan om Azure Multi-Factor Authentication Server gäller endast för användare som redan har MFA-servern igång.
>
> Konsumtionsbaserad licensiering är inte längre tillgänglig för nya kunder från och med den 1 september 2018.
> Från och med den 1 september 2018 kan nya auth-leverantörer inte längre skapas. Befintliga auth-leverantörer kan fortsätta att användas och uppdateras. Multifaktorautentisering fortsätter att vara en tillgänglig funktion i Azure AD Premium-licenser.

## <a name="general"></a>Allmänt

* [Hur hanterar Azure Multi-Factor Authentication Server användardata?](#how-does-azure-multi-factor-authentication-server-handle-user-data)
* [Vilka SMS-kortkoder används för att skicka SMS-meddelanden till mina användare?](#what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users)

### <a name="how-does-azure-multi-factor-authentication-server-handle-user-data"></a>Hur hanterar Azure Multi-Factor Authentication Server användardata?

Med Multifaktor authentication Server lagras användardata endast på de lokala servrarna. Inga beständiga användardata lagras i molnet. När användaren utför tvåstegsverifiering skickar MultiFaktor Authentication Server data till molntjänsten Azure Multi-Factor Authentication för autentisering. Kommunikation mellan MultiFaktor Authentication Server och molntjänsten MultiFaktor Authentication använder SSL (Secure Sockets Layer) eller TLS (Transport Layer Security) via utgående port 443.

När autentiseringsbegäranden skickas till molntjänsten samlas data in för autentiserings- och användningsrapporter. Följande datafält ingår i tvåstegsverifieringsloggar:

* **Unikt ID** (antingen användarnamn eller lokalt server-ID för multifaktorautentisering)
* **För- och efternamn** (valfritt)
* **E-postadress** (valfritt)
* **Telefonnummer** (vid användning av röstsamtal eller SMS-autentisering)
* **Enhetstoken** (vid användning av autentisering av mobilappar)
* **Autentiseringsläge**
* **Autentiseringsresultat**
* **Servernamn för multifaktorautentisering**
* **IP-för autentiseringsserver för flera faktorer**
* **Klient-IP** (om tillgängligt)

De valfria fälten kan konfigureras i Multifaktorautentiseringsservern.

Verifieringsresultatet (framgång eller avslag) och orsaken om det nekades lagras med autentiseringsdata. Dessa data är tillgängliga i autentiserings- och användningsrapporter.

### <a name="what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users"></a>Vilka SMS-kortkoder används för att skicka SMS-meddelanden till mina användare?

I USA använder vi följande kortkoder för SMS:

* *97671*
* *69829*
* *51789*
* *99399*

I Kanada använder vi följande KORTKODER FÖR SMS:

* *759731*
* *673801*

Det finns ingen garanti för konsekvent SMS eller röstbaserad multifaktorautentisering snabb leverans med samma nummer. I våra användares intresse kan vi lägga till eller ta bort korta koder när som helst när vi gör ruttjusteringar för att förbättra SMS-leveransbarheten. Vi stöder inte korta koder för länder eller regioner förutom USA och Kanada.

## <a name="billing"></a>Fakturering

De flesta faktureringsfrågor kan besvaras genom att referera till antingen sidan Prissättning för [multifaktorautentisering](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) eller dokumentationen för [Azure Multi-Factor Authentication-versioner och förbrukningsplaner](concept-mfa-licensing.md).

* [Debiteras min organisation för att skicka telefonsamtal och textmeddelanden som används för autentisering?](#is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication)
* [Debiterar faktureringsmodellen per användare mig för alla aktiverade användare, eller bara de som utförde tvåstegsverifiering?](#does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification)
* [Hur fungerar multifaktorautentiseringsfakturering?](#how-does-multi-factor-authentication-billing-work)
* [Finns det en gratis version av Azure Multi-Factor Authentication?](#is-there-a-free-version-of-azure-multi-factor-authentication)
* [Kan min organisation växla mellan faktureringsmodeller per användare och autentiseringsförbrukning när som helst?](#can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time)
* [Kan min organisation växla mellan förbrukningsbaserad fakturering och prenumerationer (en licensbaserad modell) när som helst?](#can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time)
* [Måste min organisation använda och synkronisera identiteter för att använda Azure Multi-Factor Authentication?](#does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-multi-factor-authentication)

### <a name="is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication"></a>Debiteras min organisation för att skicka telefonsamtal och textmeddelanden som används för autentisering?

Nej, du debiteras inte för enskilda telefonsamtal eller textmeddelanden som skickas till användare via Azure Multi-Factor Authentication. Om du använder en MFA-provider per autentisering debiteras du för varje autentisering, men inte för den metod som används.

Användarna kan debiteras för de telefonsamtal eller textmeddelanden de får, enligt deras personliga telefontjänst.

### <a name="does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification"></a>Debiterar faktureringsmodellen per användare mig för alla aktiverade användare, eller bara de som utförde tvåstegsverifiering?

Fakturering baseras på antalet användare som konfigurerats för att använda multifaktorautentisering, oavsett om de utförde tvåstegsverifiering den månaden.

### <a name="how-does-multi-factor-authentication-billing-work"></a>Hur fungerar multifaktorautentiseringsfakturering?

När du skapar en MFA-provider per användare eller per autentisering faktureras organisationens Azure-prenumeration månadsvis baserat på användning. Den här faktureringsmodellen liknar hur Azure fakturerar för användning av virtuella datorer och webbappar.

När du köper en prenumeration för Azure Multi-Factor Authentication betalar din organisation bara den årliga licensavgiften för varje användare. MFA-licenser och Office 365-, Azure AD Premium- eller Enterprise Mobility + Security-paket faktureras på det här sättet.

Mer information finns i [Så här hämtar du Azure Multi-Factor Authentication](concept-mfa-licensing.md).

### <a name="is-there-a-free-version-of-azure-multi-factor-authentication"></a>Finns det en gratis version av Azure Multi-Factor Authentication?

Standardvärden för säkerhet kan aktiveras på Azure AD Free-nivån. Med standardvärden för säkerhet är alla användare aktiverade för multifaktorautentisering med hjälp av Microsoft Authenticator-appen. Det finns ingen möjlighet att använda sms eller telefonverifiering med säkerhetsstandarder, bara Microsoft Authenticator-appen.

Mer information finns i [Vad är standardinställningar för säkerhet?](../fundamentals/concept-fundamentals-security-defaults.md)

### <a name="can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time"></a>Kan min organisation växla mellan faktureringsmodeller per användare och autentiseringsförbrukning när som helst?

Om din organisation köper MFA som en fristående tjänst med förbrukningsbaserad fakturering väljer du en faktureringsmodell när du skapar en MFA-leverantör. Du kan inte ändra faktureringsmodellen när en MFA-leverantör har skapats. 

Om din MFA-provider *inte* är länkad till en Azure AD-klientorganisation eller om du länkar den nya MFA-providern till en annan Azure AD-klient överförs inte användarinställningar och konfigurationsalternativ. Dessutom måste befintliga Azure MFA-servrar återaktiveras med hjälp av aktiveringsautentiseringsuppgifter som skapas via den nya MFA-providern. Återaktiveringen av MFA-servrarna för att koppla dem till den nya MFA-providern påverkar inte autentiseringen via telefonsamtal eller SMS, men mobilappsaviseringarna slutar fungera för alla användare tills de återaktiverar mobilappen.

Läs mer om MFA-providers i [Komma igång med en Azure Multi-Factor Auth Provider](concept-mfa-authprovider.md).

### <a name="can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time"></a>Kan min organisation växla mellan förbrukningsbaserad fakturering och prenumerationer (en licensbaserad modell) när som helst?

I vissa fall, ja.

Om katalogen har en Azure Multi-Factor *Authentication-provider per användare* kan du lägga till MFA-licenser. Användare med licenser räknas inte i den konsumtionsbaserade faktureringen per användare. Användare utan licenser kan fortfarande aktiveras för MFA via MFA-leverantören. Om du köper och tilldelar licenser för alla användare som konfigurerats för att använda multifaktorautentisering kan du ta bort Azure Multi-Factor Authentication Provider. Du kan alltid skapa en annan MFA-leverantör per användare om du har fler användare än licenser i framtiden.

Om din katalog har en Azure Multi-Factor *Authentication-provider per autentisering* debiteras du alltid för varje autentisering, så länge MFA-providern är länkad till din prenumeration. Du kan tilldela MFA-licenser till användare, men du debiteras fortfarande för varje tvåstegsverifieringsbegäran, oavsett om den kommer från någon med en MFA-licens tilldelad eller inte.

### <a name="does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-multi-factor-authentication"></a>Måste min organisation använda och synkronisera identiteter för att använda Azure Multi-Factor Authentication?

Om din organisation använder en förbrukningsbaserad faktureringsmodell är Azure Active Directory valfritt, men krävs inte. Om din MFA-provider inte är länkad till en Azure AD-klient kan du bara distribuera Azure Multi-Factor Authentication Server lokalt.

Azure Active Directory krävs för licensmodellen eftersom licenser läggs till i Azure AD-klienten när du köper och tilldelar dem till användare i katalogen.

## <a name="manage-and-support-user-accounts"></a>Hantera och stöd användarkonton

* [Vad ska jag be mina användare att göra om de inte får något svar på sin telefon?](#what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone)
* [Vad ska jag göra om en av mina användare inte kan komma in på sitt konto?](#what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account)
* [Vad ska jag göra om en av mina användare förlorar en telefon som använder applösenord?](#what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords)
* [Vad händer om en användare inte kan logga in på appar som inte är webbläsare?](#what-if-a-user-cant-sign-in-to-non-browser-apps)
* [Mina användare säger att de ibland inte får sms:et eller verifieringstidens.](#my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out)
* [Kan jag ändra hur lång tid mina användare måste ange verifieringskoden från ett textmeddelande innan systemet time out?](#can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out)
* [Kan jag använda maskinvarutoken med Azure Multi-Factor Authentication Server?](#can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server)
* [Kan jag använda Azure Multi-Factor Authentication Server för att skydda Terminal Services?](#can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services)
* [Jag har konfigurerat nummerpresentatör i MFA Server, men mina användare får fortfarande multifaktorautentiseringsanrop från en anonym uppringare.](#i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller)
* [Varför uppmanas mina användare att registrera sin säkerhetsinformation?](#why-are-my-users-being-prompted-to-register-their-security-information)

### <a name="what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone"></a>Vad ska jag be mina användare att göra om de inte får något svar på sin telefon?

Låt användarna försöka upp till fem gånger på 5 minuter för att få ett telefonsamtal eller SMS för autentisering. Microsoft använder flera leverantörer för att leverera samtal och SMS-meddelanden. Om den här metoden inte fungerar öppnar du ett supportärende för felsökning ytterligare.

Säkerhetsappar från tredje part kan också blockera sms:et eller telefonsamtalet för verifieringskoden. Om du använder en säkerhetsapp från tredje part kan du prova att inaktivera skyddet och sedan begära att en annan MFA-verifieringskod skickas.

Om stegen ovan inte fungerar kontrollerar du om användarna är konfigurerade för mer än en verifieringsmetod. Försök logga in igen, men välj en annan verifieringsmetod på inloggningssidan.

Mer information finns i [felsökningsguiden](../user-help/multi-factor-authentication-end-user-troubleshoot.md)för slutanvändare .

### <a name="what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account"></a>Vad ska jag göra om en av mina användare inte kan komma in på sitt konto?

Du kan återställa användarens konto genom att göra dem att gå igenom registreringsprocessen igen. Läs mer om hur du [hanterar användar- och enhetsinställningar med Azure Multi-Factor Authentication i molnet](howto-mfa-userdevicesettings.md).

### <a name="what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords"></a>Vad ska jag göra om en av mina användare förlorar en telefon som använder applösenord?

Om du vill förhindra obehörig åtkomst tar du bort alla användarens applösenord. När användaren har en ersättningsenhet kan de återskapa lösenorden. Läs mer om hur du [hanterar användar- och enhetsinställningar med Azure Multi-Factor Authentication i molnet](howto-mfa-userdevicesettings.md).

### <a name="what-if-a-user-cant-sign-in-to-non-browser-apps"></a>Vad händer om en användare inte kan logga in på appar som inte är webbläsare?

Om din organisation fortfarande använder äldre klienter och du [har tillåtit användning av applösenord](howto-mfa-mfasettings.md#app-passwords)kan användarna inte logga in på dessa äldre klienter med sitt användarnamn och lösenord. I stället måste de [ställa in applösenord](../user-help/multi-factor-authentication-end-user-app-passwords.md). Användarna måste rensa (ta bort) sin inloggningsinformation, starta om appen och sedan logga in med sitt användarnamn och *applösenord* i stället för sitt vanliga lösenord.

Om din organisation inte har äldre klienter bör du inte tillåta användarna att skapa applösenord.

> [!NOTE]
> **Modern autentisering för Office 2013-klienter**
>
> Applösenord är bara nödvändiga för appar som inte stöder modern autentisering. Office 2013-klienter stöder moderna autentiseringsprotokoll, men måste konfigureras. Modern autentisering är tillgänglig för alla kunder som kör mars 2015 eller senare för Office 2013. Mer information finns i blogginlägget [Uppdaterad Office 365 modern autentisering](https://www.microsoft.com/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview/).

### <a name="my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out"></a>Mina användare säger att de ibland inte får sms:et eller verifieringstidens.

Leverans av SMS-meddelanden garanteras inte eftersom det finns okontrollerbara faktorer som kan påverka tjänstens tillförlitlighet. Dessa faktorer inkluderar destinationslandet eller destinationsregionen, mobiltelefonoperatören och signalstyrkan.

Säkerhetsappar från tredje part kan också blockera sms:et eller telefonsamtalet för verifieringskoden. Om du använder en säkerhetsapp från tredje part kan du prova att inaktivera skyddet och sedan begära att en annan MFA-verifieringskod skickas.

Om användarna ofta har problem med att ta emot textmeddelanden på ett tillförlitligt sätt kan du be dem använda Microsoft Authenticator-appen eller telefonsamtalsmetoden i stället. Microsoft Authenticator kan ta emot meddelanden både via mobil- och Wi-Fi-anslutningar. Dessutom kan mobilappen generera verifieringskoder även när enheten inte har någon signal alls. Microsoft Authenticator-appen finns för [Android](https://go.microsoft.com/fwlink/?Linkid=825072), [iOS](https://go.microsoft.com/fwlink/?Linkid=825073) och [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6).

### <a name="can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out"></a>Kan jag ändra hur lång tid mina användare måste ange verifieringskoden från ett textmeddelande innan systemet time out?

I vissa fall, ja.

För enkelriktade SMS med Azure MFA Server v7.0 eller senare kan du konfigurera timeout-inställningen genom att ange en registernyckel. När MFA-molntjänsten har skickat sms:et returneras verifieringskoden (eller engångslösenordet) till MFA-servern. MFA-servern lagrar koden i minnet i 300 sekunder som standard. Om användaren inte anger koden innan 300 sekunder har passerat nekas autentiseringen. Så här ändrar du standardinställningen för timeout:

1. Gå till `HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor`.
2. Skapa en **DWORD-registernyckel** som kallas *pfsvc_pendingSmsTimeoutSeconds* och ange den tid i sekunder som du vill att Azure MFA Server ska lagra engångslösenord.

>[!TIP]
>
> Om du har flera MFA-servrar är det bara den som bearbetade den ursprungliga autentiseringsbegäran som känner till verifieringskoden som skickades till användaren. När användaren anger koden måste autentiseringsbegäran för att verifiera den skickas till samma server. Om kodverifieringen skickas till en annan server nekas autentiseringen.

Om användarna inte svarar på SMS inom den angivna timeout-perioden nekas deras autentisering.

För enkelriktade SMS med Azure MFA i molnet (inklusive AD FS-kortet eller tillägget Nätverksprincipserver) kan du inte konfigurera timeout-inställningen. Azure AD lagrar verifieringskoden i 180 sekunder.

### <a name="can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server"></a>Kan jag använda maskinvarutoken med Azure Multi-Factor Authentication Server?

Om du använder Azure Multi-Factor Authentication Server kan du importera tidsbaserade ed-token (Open Authentication) (Open-Part, ett engångslösenord) och sedan använda dem för tvåstegsverifiering.

Du kan använda ActiveIdentity-token som är OATH TOTP-token om du placerar den hemliga nyckeln i en CSV-fil och importerar till Azure Multi-Factor Authentication Server. Du kan använda OATH-token med ADFS (Active Directory Federation Services), IIS-formulärbaserad autentisering (Internet Information Server) och RADIUS (Remote Authentication Dial-In User Service) så länge klientsystemet kan acceptera användarindata.

Du kan importera OATH TOTP-token från tredje part med följande format:  

- Bärbar symmetrisk nyckelbehållare (PSKC)
- CSV om filen innehåller ett serienummer, en hemlig nyckel i Base 32-format och ett tidsintervall

### <a name="can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services"></a>Kan jag använda Azure Multi-Factor Authentication Server för att skydda Terminal Services?

Ja, men om du använder Windows Server 2012 R2 eller senare kan du bara skydda Terminal Services med hjälp av Fjärrskrivbordsgateway (Rd Gateway).

Säkerhetsändringar i Windows Server 2012 R2 förändrade hur Azure Multi-Factor Authentication Server ansluter till LSA-säkerhetspaketet (Local Security Authority) i Windows Server 2012 och tidigare versioner. För versioner av Terminal Services i Windows Server 2012 eller tidigare kan du [skydda ett program med Windows-autentisering](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Om du använder Windows Server 2012 R2 behöver du port gateway för fjärrskrivbord.

### <a name="i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller"></a>Jag har konfigurerat nummerpresentatör i MFA Server, men mina användare får fortfarande multifaktorautentiseringsanrop från en anonym uppringare.

När multifaktorautentiseringssamtal placeras via det allmänna telefonnätet dirigeras de ibland via en operatör som inte stöder nummerpresentatör. På grund av det här operatörsbeteendet garanteras inte nummerpresentatörs-ID, även om multifaktorautentiseringssystemet alltid skickar det.

### <a name="why-are-my-users-being-prompted-to-register-their-security-information"></a>Varför uppmanas mina användare att registrera sin säkerhetsinformation?

Det finns flera orsaker till att användare kan uppmanas att registrera sin säkerhetsinformation:

- Användaren har aktiverats för MFA av sin administratör i Azure AD, men har inte säkerhetsinformation registrerad för sitt konto ännu.
- Användaren har aktiverats för återställning av lösenord för självbetjäning i Azure AD. Säkerhetsinformationen hjälper dem att återställa sitt lösenord i framtiden om de någonsin glömmer det.
- Användaren har åtkomst till ett program som har en princip för villkorlig åtkomst för att kräva MFA och som inte tidigare har registrerats för MFA.
- Användaren registrerar en enhet med Azure AD (inklusive Azure AD Join) och din organisation kräver MFA för enhetsregistrering, men användaren har inte tidigare registrerats för MFA.
- Användaren genererar Windows Hello för företag i Windows 10 (som kräver MFA) och har inte tidigare registrerats för MFA.
- Organisationen har skapat och aktiverat en MFA-registreringsprincip som har tillämpats på användaren.
- Användaren har tidigare registrerats för MFA, men valde en verifieringsmetod som en administratör sedan dess har inaktiverat. Användaren måste därför gå igenom MFA-registrering igen för att välja en ny standardverifieringsmetod.

## <a name="errors"></a>Fel

* [Vad ska användarna göra om de ser felmeddelandet "Autentiseringsbegäran inte är för ett aktiverat konto" när de använder aviseringar i mobilappen?](#what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications)
* [Vad ska användarna göra om de ser ett felmeddelande om 0x800434D4L när de loggar in på ett program som inte är webbläsarprogram?](#what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application)

### <a name="what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications"></a>Vad ska användarna göra om de ser felmeddelandet "Autentiseringsbegäran inte är för ett aktiverat konto" när de använder aviseringar i mobilappen?

Be användaren att slutföra följande procedur för att ta bort sitt konto från Microsoft Authenticator och sedan lägga till det igen:

1. Gå till [deras Azure-portalprofil](https://account.activedirectory.windowsazure.com/profile/) och logga in med ett organisationskonto.
2. Välj **Ytterligare säkerhetsverifiering**.
3. Ta bort det befintliga kontot från Microsoft Authenticator-appen.
4. Klicka på **Konfigurera**och följ sedan instruktionerna för att konfigurera om Microsoft Authenticator.

### <a name="what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application"></a>Vad ska användarna göra om de ser ett felmeddelande om 0x800434D4L när de loggar in på ett program som inte är webbläsarprogram?

*Felet 0x800434D4L* uppstår när du försöker logga in på ett program som inte är webbläsarprogram, installerat på en lokal dator, som inte fungerar med konton som kräver tvåstegsverifiering.

En lösning på det här felet är att ha separata användarkonton för administratörsrelaterade och icke-administratörsåtgärder. Senare kan du länka postlådor mellan administratörskontot och icke-administratörskontot så att du kan logga in i Outlook med hjälp av ditt icke-administratörskonto. Mer information om den här lösningen finns i hur du [ger en administratör möjlighet att öppna och visa innehållet i en användares postlåda](https://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Nästa steg

Om din fråga inte besvaras här är följande supportalternativ tillgängliga:

* Sök i [Microsoft Support Knowledge Base](https://support.microsoft.com) efter lösningar på vanliga tekniska problem.
* Sök efter och bläddra bland tekniska frågor och svar från communityn, eller ställ din egen fråga i [Azure Active Directory Q&A](https://docs.microsoft.com/answers/topics/azure-active-directory.html).
* Kontakta Microsoft Professional via [Azure Multi-Factor Authentication Server support](https://support.microsoft.com/oas/default.aspx?prid=14947). När du kontaktar oss är det användbart om du kan inkludera så mycket information om ditt problem som möjligt. Information som du kan ange inkluderar sidan där du såg felet, den specifika felkoden, det specifika sessions-ID:t och ID:t för den användare som såg felet.
* Om du är en äldre PhoneFactor-kund och du har frågor eller [phonefactorsupport@microsoft.com](mailto:phonefactorsupport@microsoft.com) behöver hjälp med att återställa ett lösenord använder du e-postadressen för att öppna ett supportärende.
