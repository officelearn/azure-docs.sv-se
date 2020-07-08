---
title: Lösen ords återställning via självbetjäning – Azure Active Directory
description: Hur fungerar lösen ords återställning via självbetjäning
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b19c80378aa40a7f791a3eb61130b013217ddee
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "74848586"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>Så här fungerar det: återställning av lösen ord för självbetjäning i Azure AD

Hur fungerar självbetjäning för återställning av lösen ord (SSPR)? Vad betyder det alternativet i gränssnittet? Fortsätt att läsa för att få mer information om Azure Active Directory (Azure AD) SSPR.

## <a name="how-does-the-password-reset-portal-work"></a>Hur fungerar portalen för lösen ords återställning?

När en användare går till portalen för lösen ords återställning startas ett arbets flöde för att fastställa:

   * Hur ska sidan lokaliseras?
   * Är användar kontot giltigt?
   * Vilken organisation tillhör användaren?
   * Var hanteras användarens lösen ord?
   * Är användaren licensierad för att använda funktionen?

Läs igenom följande steg för att lära dig mer om logiken bakom sidan för lösen ords återställning:

1. Användaren väljer länken **kan inte komma åt ditt konto** eller går direkt till [https://aka.ms/sspr](https://passwordreset.microsoftonline.com) .
   * Baserat på webbläsarens nationella inställningar, återges upplevelsen på det aktuella språket. Lösen ords återställnings upplevelsen lokaliseras på samma språk som Office 365 stöder.
   * Om du vill visa portalen för återställning av lösen ord på ett annat lokaliserat språk lägger du till "? mkt =" i slutet av URL: en för lösen ords återställning med exemplet som följer nationella inställningar till spanska [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us) .
2. Användaren anger ett användar-ID och skickar en captcha.
3. Azure AD kontrollerar att användaren kan använda den här funktionen genom att göra följande:
   * Kontrollerar att användaren har den här funktionen aktive rad och har tilldelats en Azure AD-licens.
     * Om användaren inte har den här funktionen aktive rad eller har en tilldelad licens, uppmanas användaren att kontakta sin administratör för att återställa sitt lösen ord.
   * Kontrollerar att användaren har rätt autentiseringsmetoder definierade för sitt konto i enlighet med administratörs principen.
     * Om principen bara kräver en metod ser det till att användaren har rätt data definierad för minst en av de autentiseringsmetoder som Aktiver ATS av administratörs principen.
       * Om autentiseringsmetoderna inte har kon figurer ATS, uppmanas användaren att kontakta sin administratör för att återställa sitt lösen ord.
     * Om principen kräver två metoder ser det till att användaren har rätt data definierad för minst två av de autentiseringsmetoder som har Aktiver ATS av administratörs principen.
       * Om autentiseringsmetoderna inte har kon figurer ATS, uppmanas användaren att kontakta sin administratör för att återställa sitt lösen ord.
     * Om en Azure-administratör är tilldelad användaren, tillämpas den starka lösen ords principen på två-grind. Mer information om den här principen finns i avsnittet [återställa princip skillnader](concept-sspr-policy.md#administrator-reset-policy-differences).
   * Kontrollerar om användarens lösen ord hanteras lokalt (federerad autentisering, direktautentisering eller hash-synkronisering av lösen ord).
     * Om tillbakaskrivning distribueras och användarens lösen ord hanteras lokalt, tillåts användaren att fortsätta att autentisera och återställa sitt lösen ord.
     * Om tillbakaskrivning inte distribueras och användarens lösen ord hanteras lokalt, uppmanas användaren att kontakta sin administratör för att återställa sitt lösen ord.
4. Om det har fastställts att användaren kan återställa sitt lösen ord, guidas användaren genom återställnings processen.

## <a name="authentication-methods"></a>Autentiseringsmetoder

Om SSPR har Aktiver ATS måste du välja minst ett av följande alternativ för autentiseringsmetoderna. Ibland kan du höra dessa alternativ som kallas "grindar". Vi rekommenderar starkt att du **väljer två eller fler autentiseringsmetoder** så att användarna har större flexibilitet om de inte kan komma åt någon när de behöver dem. Du hittar mer information om de metoder som anges nedan i artikeln [Vad är autentiseringsmetoder?](concept-authentication-methods.md).

* Meddelanden via mobilapp
* Kod för mobilapp
* E-post
* Mobiltelefon
* Arbetstelefon
* Säkerhetsfrågor

Användare kan bara återställa sina lösen ord om de har data som finns i de autentiseringsmetoder som administratören har aktiverat.

> [!IMPORTANT]
> Från och med mars 2019 kommer Telefonsamtals alternativen inte att vara tillgängliga för MFA-och SSPR-användare i kostnads fria/utvärderings versioner av Azure AD-klienter. SMS-meddelanden påverkas inte av den här ändringen. Telefonsamtalet fortsätter att vara tillgängligt för användare i betalda Azure AD-klienter. Den här ändringen påverkar endast kostnads fria/utvärderings versioner av Azure AD.

> [!WARNING]
> Konton tilldelade Azure Administrator-roller krävs för att använda metoder som definieras i avsnittet [återställa princip skillnader](concept-sspr-policy.md#administrator-reset-policy-differences).

![Val av autentiseringsmetoder i Azure Portal][Authentication]

### <a name="number-of-authentication-methods-required"></a>Antal autentiseringsmetoder som krävs

Det här alternativet anger det minsta antalet tillgängliga autentiseringsmetoder eller portar som en användare måste gå igenom för att återställa eller låsa upp sina lösen ord. Den kan anges till antingen en eller två.

Användarna kan välja att tillhandahålla fler autentiseringsmetoder om administratören aktiverar den autentiseringsmetoden.

Om en användare inte har minst de nödvändiga metoderna registrerade visas en felsida som uppmanar dem att begära att en administratör återställer sitt lösen ord.

#### <a name="mobile-app-and-sspr"></a>Mobilapp och SSPR

När du använder en mobilapp, t. ex. Microsoft Authenticator-appen, som en metod för lösen ords återställning, bör du vara medveten om följande varningar:

* När administratörer behöver en metod för att återställa ett lösen ord är verifierings koden det enda tillgängliga alternativet.
* När administratörer kräver att två metoder används för att återställa ett lösen ord kan användare använda **antingen** meddelande- **eller** verifierings kod förutom andra aktiverade metoder.

| Antal metoder som krävs för att återställa | En | Två |
| :---: | :---: | :---: |
| Mobile app-funktioner som är tillgängliga | Kod | Kod eller meddelande |

Användarna har inte möjlighet att registrera sin mobilapp när de registrerar sig för lösen ords återställning via självbetjäning från [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) . Användare kan registrera sina mobilappar på [https://aka.ms/mfasetup](https://aka.ms/mfasetup) eller i den nya för hands versionen av säkerhets information på [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

> [!WARNING]
> Du måste aktivera den [konvergerade registreringen för självbetjäning för återställning av lösen ord och Azure Multi-Factor Authentication (offentlig för hands version)](concept-registration-mfa-sspr-converged.md) innan användarna kan komma åt den nya upplevelsen på [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

> [!IMPORTANT]
> Det går inte att välja Authenticator-appen som den enda autentiseringsmetoden när du konfigurerar en princip för 1-grind. På samma sätt kan inte Authenticator-appen och endast en ytterligare metod väljas när du konfigurerar en princip på 2 portar.
> När du sedan konfigurerar SSPR-principer som inkluderar Authenticator-appen som en metod, måste du välja minst ytterligare en metod när du konfigurerar en princip för en 1-grind och minst två ytterligare metoder bör väljas när du konfigurerar en princip för 2 portar.
> Orsaken till det här kravet är att den aktuella SSPR-registreringen inte innehåller alternativet för att registrera Authenticator-appen. Alternativet att registrera Authenticator-appen ingår i den nya [konvergerade registreringen för självbetjäning för återställning av lösen ord och Azure Multi-Factor Authentication (offentlig för hands version)](concept-registration-mfa-sspr-converged.md).
> Att tillåta att principer som bara använder Authenticator-appen (för 1-grind-principer) eller Authenticator-appen och endast en ytterligare metod (för 2-grind-principer) kan leda till att användare blockeras från att registrera sig för SSPR tills de har kon figurer ATS för att använda den nya registrerings upplevelsen.

### <a name="change-authentication-methods"></a>Ändra autentiseringsmetoder

Vad händer om du börjar med en princip som bara har en obligatorisk autentiseringsmetod för återställning eller upplåsning av registreras och du ändrar den till två metoder?

| Antal registrerade metoder | Antal metoder som krävs | Resultat |
| :---: | :---: | :---: |
| 1 eller flera | 1 | **Kan** återställa eller låsa upp |
| 1 | 2 | **Det går inte** att återställa eller låsa upp |
| 2 eller flera | 2 | **Kan** återställa eller låsa upp |

Om du ändrar de typer av autentiseringsmetoder som en användare kan använda kan du oavsiktligt hindra användare från att kunna använda SSPR om de inte har den minsta mängden data som är tillgängliga.

Exempel:
1. Den ursprungliga principen konfigureras med två autentiseringsmetoder som krävs. Den använder bara arbets telefonnumret och säkerhets frågorna. 
2. Administratören ändrar principen till att inte längre använda säkerhets frågorna, men kan använda en mobil telefon och ett alternativt e-postmeddelande.
3. Användare utan mobil telefon eller alternativa e-postfält ifyllda kan inte återställa sina lösen ord.

## <a name="registration"></a>Registrering

### <a name="require-users-to-register-when-they-sign-in"></a>Kräv att användare registrerar sig när de loggar in

Om du aktiverar det här alternativet måste en användare slutföra registreringen av lösen ords återställning om de loggar in i alla program som använder Azure AD. Det här arbets flödet innehåller följande program:

* Office 365
* Azure Portal
* Åtkomstpanel
* Federerade program
* Anpassade program med hjälp av Azure AD

När registreringen är inaktive rad kan användarna registrera sig manuellt. De kan antingen besöka [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) eller välja länken **Registrera för lösen ords återställning** under fliken **profil** på åtkomst panelen.

> [!NOTE]
> Användare kan stänga registrerings portalen för lösen ords återställning genom att välja **Avbryt** eller stänga fönstret. Men de uppmanas att registrera sig varje gången de loggar in tills de har slutfört registreringen.
>
> Detta avbrott bryter inte användarens anslutning om de redan är inloggade.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Ange antalet dagar innan användare uppmanas att bekräfta sin autentiseringsinformation

Det här alternativet bestämmer hur lång tid det tar mellan att ange och bekräfta autentiseringsinformation och bara är tillgängligt om du aktiverar alternativet **Kräv att användare registrerar sig vid inloggning** .

Giltiga värden är 0 till 730 dagar, med "0", vilket innebär att användare aldrig uppmanas att bekräfta sin autentiseringsinformation.

## <a name="notifications"></a>Meddelanden

### <a name="notify-users-on-password-resets"></a>Meddela användare om lösenordsåterställning

Om det här alternativet är inställt på **Ja**får användare som återställer sina lösen ord ett e-postmeddelande om att deras lösen ord har ändrats. E-postmeddelandet skickas via SSPR-portalen till sina primära och alternativa e-postadresser som finns i filen i Azure AD. Ingen annan får ett meddelande om reset-händelsen.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Meddela alla administratörer när andra administratörer återställer sina lösen ord

Om det här alternativet är inställt på **Ja**får *alla administratörer* ett e-postmeddelande till sin primära e-postadress på filen i Azure AD. E-postmeddelandet meddelar att en annan administratör har ändrat sitt lösen ord med hjälp av SSPR.

Exempel: det finns fyra administratörer i en miljö. Administratör A återställer lösen ordet med hjälp av SSPR. Administratörer B, C och D får en e-postavisering om att lösen ordet återställs.

## <a name="on-premises-integration"></a>Lokal integration

Om du installerar, konfigurerar och aktiverar Azure AD Connect har du följande ytterligare alternativ för lokala integreringar. Om de här alternativen är nedtonade har tillbakaskrivning inte kon figurer ATS korrekt. Mer information finns i [Konfigurera tillbakaskrivning av lösen ord](howto-sspr-writeback.md).

![Validering av tillbakaskrivning av lösen ord är aktiverat och fungerar][Writeback]

På den här sidan får du en snabb status för den lokala tillbakaskrivning-klienten, något av följande meddelanden visas baserat på den aktuella konfigurationen:

* Din lokala tillbakaskrivning-klient är igång.
* Azure AD är online och är ansluten till din lokala tillbakaskrivning-klient. Det ser dock ut som att den installerade versionen av Azure AD Connect är inaktuell. Överväg att [uppgradera Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) för att säkerställa att du har de senaste anslutnings funktionerna och viktiga fel korrigeringar.
* Tyvärr kan vi inte kontrol lera din lokala tillbakaskrivning av tillbakaskrivning-klientens status eftersom den installerade versionen av Azure AD Connect är inaktuell. [Uppgradera Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) för att kunna kontrol lera anslutnings status.
* Tyvärr ser det ut som att vi inte kan ansluta till din lokala tillbakaskrivning-klient just nu. [Felsök Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) för att återställa anslutningen.
* Tyvärr kan vi inte ansluta till din lokala tillbakaskrivning-klient eftersom tillbakaskrivning av lösen ord inte har kon figurer ATS korrekt. [Konfigurera tillbakaskrivning av lösen ord](howto-sspr-writeback.md) för att återställa anslutningen.
* Tyvärr ser det ut som att vi inte kan ansluta till din lokala tillbakaskrivning-klient just nu. Detta kan bero på tillfälliga problem på vår sida. Om problemet kvarstår [felsöker du Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) för att återställa anslutningen.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Skriv tillbaka lösen ord till din lokala katalog

Den här kontrollen avgör om tillbakaskrivning av lösen ord är aktiverat för den här katalogen. Om tillbakaskrivning är på, anger det statusen för den lokala tillbakaskrivning tjänsten. Den här kontrollen är användbar om du tillfälligt vill inaktivera tillbakaskrivning av lösen ord utan att behöva konfigurera om Azure AD Connect.

* Om växeln är inställd på **Ja**är tillbakaskrivning aktive rad, och federerad, direktautentisering, eller så kan användare som synkroniseras med lösen ords-hash återställa sina lösen ord.
* Om växeln är inställd på **Nej**, så inaktive ras tillbakaskrivning och federerad, direktautentisering eller lösen ords-hash synkroniserade användare kan inte återställa sina lösen ord.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Tillåt att användare låser upp konton utan att återställa sina lösen ord

Den här kontrollen anger om användare som besöker portalen för återställning av lösen ord ska ges möjlighet att låsa upp sina lokala Active Directory-konton utan att behöva återställa sina lösen ord. Som standard låser Azure AD upp konton när en lösen ords återställning utförs. Du kan använda den här inställningen för att avgränsa de två åtgärderna.

* Om det är inställt på **Ja**får användarna möjlighet att återställa sina lösen ord och låsa upp kontot eller för att låsa upp kontot utan att behöva återställa lösen ordet.
* Om det är inställt på **Nej**kan användarna bara utföra en kombinerad lösen ords återställning och konto upplåsning.

### <a name="on-premises-active-directory-password-filters"></a>Lösen ords filter för lokala Active Directory

Återställning av lösen ord för självbetjäning i Azure AD utför motsvarigheten till en administratörs initierad lösen ords återställning i Active Directory. Om du använder ett lösen ords filter från tredje part för att genomdriva anpassade lösen ords regler och du kräver att det här lösen ords filtret kontrol leras under återställning av lösen ord i Azure AD, kontrollerar du att lösningen för lösen ords filter från tredje part är konfigurerad att gälla i scenariot för återställning av lösen ord. [Azure AD Password Protection för Windows Server Active Directory](concept-password-ban-bad-on-premises.md) stöds som standard.

## <a name="password-reset-for-b2b-users"></a>Lösen ords återställning för B2B-användare

Återställning av lösen ord och ändringar stöds helt och hållet i alla konfigurationer för Business-to-Business (B2B). B2B-återställning av användar lösen ord stöds i följande tre fall:

* **Användare från en partner organisation med en befintlig Azure AD-klient**: om organisationen som du samarbetar med har en befintlig Azure AD-klient ser vi *hur principerna för återställning av lösen ord är aktiverade på klienten*. För att lösen ords återställning ska fungera måste partner organisationen bara se till att Azure AD SSPR har Aktiver ATS. Det finns ingen extra kostnad för Office 365-kunder och den kan aktive ras genom att följa stegen i guiden [komma igång med lösen ords hantering](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords) .
* **Användare som registrerar sig via** självbetjänings registrering: om den organisation som du samarbetar med använder funktionen för [självbetjänings registrering](../users-groups-roles/directory-self-service-signup.md) för att ansluta till en klient kan vi återställa lösen ordet med det e-postmeddelande som de har registrerat.
* **B2B-användare**: alla nya B2B-användare som skapats med hjälp av de nya [Azure AD B2B-funktionerna](../active-directory-b2b-what-is-azure-ad-b2b.md) kommer också att kunna återställa sina lösen ord med e-postmeddelandet som de registrerade under den inbjudna processen.

För att testa det här scenariot går du till https://passwordreset.microsoftonline.com med någon av dessa partner användare. Om en annan e-postadress eller ett e-postmeddelande har definierats fungerar lösen ords återställning som förväntat.

> [!NOTE]
> Microsoft-konton som har beviljats gäst åtkomst till din Azure AD-klient, t. ex. från Hotmail.com, Outlook.com eller andra personliga e-postadresser, kan inte använda Azure AD SSPR. De behöver återställa sina lösen ord med hjälp av informationen i [när du inte kan logga in på Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) -artikeln.

## <a name="next-steps"></a>Nästa steg

Följande artiklar ger ytterligare information om lösenordsåterställning via Azure AD:

* [Hur gör jag för att slutföra en lyckad distribution av SSPR?](howto-sspr-deployment.md)
* [Återställ eller ändra ditt lösen ord](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrera för återställning av lösenord med självbetjäning](../user-help/active-directory-passwords-reset-register.md)
* [Har du en fråga om licensiering?](concept-sspr-licensing.md)
* [Vilka data används av SSPR och vilka data bör du fylla i för dina användare?](howto-sspr-authenticationdata.md)
* [Vilka autentiseringsmetoder är tillgängliga för användarna?](concept-sspr-howitworks.md#authentication-methods)
* [Vilka principalternativ finns för SSPR?](concept-sspr-policy.md)
* [Vad är tillbakaskrivning av lösenord och vad är intresserat med det?](howto-sspr-writeback.md)
* [Hur gör jag för att rapportera på aktivitet i SSPR?](howto-sspr-reporting.md)
* [Vad är alla alternativ i SSPR och vad betyder de?](concept-sspr-howitworks.md)
* [Jag tror att något är brutet. Hur gör jag för att felsöka SSPR?](active-directory-passwords-troubleshoot.md)
* [Jag har en fråga som inte besvarades någon annanstans](active-directory-passwords-faq.md)

[Authentication]: ./media/concept-sspr-howitworks/manage-authentication-methods-for-password-reset.png "Azure AD-autentiseringsmetoder som är tillgängliga och kvantitet som krävs"
[Writeback]: ./media/concept-sspr-howitworks/troubleshoot-on-premises-integration-writeback.png "Konfiguration av tillbakaskrivning av lösen ord i lokal integration och felsöknings information"
