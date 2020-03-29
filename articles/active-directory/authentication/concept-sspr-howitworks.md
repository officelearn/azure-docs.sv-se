---
title: Djupdykning av lösenordsåterbeställning med självbetjäning - Azure Active Directory
description: Hur fungerar återställning av lösenord med självbetjäning
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848586"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>Så här fungerar det: Azure AD-återställning av lösenord för självbetjäning

Hur fungerar självbetjäningslösenordsåterställning (SSPR) ? Vad betyder det alternativet i gränssnittet? Fortsätt läsa för att ta reda på mer om Azure Active Directory (Azure AD) SSPR.

## <a name="how-does-the-password-reset-portal-work"></a>Hur fungerar portalen för återställning av lösenord?

När en användare går till portalen för återställning av lösenord startas ett arbetsflöde för att avgöra:

   * Hur ska sidan lokaliseras?
   * Är användarkontot giltigt?
   * Vilken organisation tillhör användaren?
   * Var hanteras användarens lösenord?
   * Är användaren licensierad att använda funktionen?

Läs igenom följande steg för att lära dig mer om logiken bakom sidan för återställning av lösenord:

1. Användaren väljer länken **Kan inte komma åt ditt** [https://aka.ms/sspr](https://passwordreset.microsoftonline.com)konto eller går direkt till .
   * Baserat på webbläsarens språk återges upplevelsen på rätt språk. Upplevelsen för återställning av lösenord är lokaliserad till samma språk som Office 365 stöder.
   * Om du vill visa portalen för återställning av lösenord på ett annat lokaliserat språk läggs du till "?mkt=" i slutet av url:en för återställning av lösenord med exemplet som följer lokalisera till spanska [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us).
2. Användaren anger ett användar-ID och skickar en captcha.
3. Azure AD verifierar att användaren kan använda den här funktionen genom att göra följande kontroller:
   * Kontrollerar att användaren har den här funktionen aktiverad och har tilldelats en Azure AD-licens.
     * Om användaren inte har den här funktionen aktiverad eller har tilldelats en licens uppmanas användaren att kontakta administratören för att återställa lösenordet.
   * Kontrollerar att användaren har rätt autentiseringsmetoder som definierats för sitt konto i enlighet med administratörsprincipen.
     * Om principen bara kräver en metod säkerställer den att användaren har rätt data som definierats för minst en av de autentiseringsmetoder som administratörsprincipen har aktiverat.
       * Om autentiseringsmetoderna inte är konfigurerade uppmanas användaren att kontakta administratören för att återställa lösenordet.
     * Om principen kräver två metoder säkerställer den att användaren har rätt data som definierats för minst två av de autentiseringsmetoder som aktiveras av administratörsprincipen.
       * Om autentiseringsmetoderna inte är konfigurerade uppmanas användaren att kontakta administratören för att återställa lösenordet.
     * Om en Azure-administratörsroll tilldelas användaren tillämpas den starka lösenordsprincipen med två portar. Mer information om den här principen finns i avsnittet Skillnader i [princip för administratörsåterställning](concept-sspr-policy.md#administrator-reset-policy-differences).
   * Kontrollerar om användarens lösenord hanteras lokalt (federerad, direktautentisering eller lösenordsh hash synkroniserad).
     * Om tillbakaskrivning distribueras och användarens lösenord hanteras lokalt, får användaren fortsätta att autentisera och återställa sitt lösenord.
     * Om tillbakaskrivning inte distribueras och användarens lösenord hanteras lokalt uppmanas användaren att kontakta administratören för att återställa lösenordet.
4. Om det är fastställt att användaren kan återställa sitt lösenord, då användaren styrs genom återställningsprocessen.

## <a name="authentication-methods"></a>Autentiseringsmetoder

Om SSPR är aktiverat måste du välja minst ett av följande alternativ för autentiseringsmetoderna. Ibland hör du dessa alternativ som kallas "grindar". Vi rekommenderar starkt att du **väljer två eller flera autentiseringsmetoder** så att användarna får större flexibilitet om de inte kan komma åt en när de behöver den. Ytterligare information om de metoder som anges nedan finns i artikeln [Vad är autentiseringsmetoder?](concept-authentication-methods.md).

* Meddelanden via mobilapp
* Kod för mobilapp
* E-post
* Mobiltelefon
* Arbetstelefon
* Säkerhetsfrågor

Användare kan bara återställa sitt lösenord om de har data i de autentiseringsmetoder som administratören har aktiverat.

> [!IMPORTANT]
> Från och med mars 2019 är samtalsalternativen inte tillgängliga för MFA- och SSPR-användare i Azure AD-klienter med kostnadsfri/utvärderingsversion. SMS-meddelanden påverkas inte av den här ändringen. Telefonsamtal fortsätter att vara tillgängligt för användare i betalda Azure AD-klienter. Den här ändringen påverkar endast azure AD-klienter som är kostnadsfria/prov.

> [!WARNING]
> Konton som tilldelats Azure-administratörsroller måste använda metoder som definieras i avsnittet [Administratörsåterställningsprincipskillnader](concept-sspr-policy.md#administrator-reset-policy-differences).

![Val av autentiseringsmetoder i Azure-portalen][Authentication]

### <a name="number-of-authentication-methods-required"></a>Antal autentiseringsmetoder som krävs

Det här alternativet avgör det minsta antalet tillgängliga autentiseringsmetoder eller grindar som en användare måste gå igenom för att återställa eller låsa upp sitt lösenord. Den kan ställas in på antingen en eller två.

Användare kan välja att ange fler autentiseringsmetoder om administratören aktiverar den autentiseringsmetoden.

Om en användare inte har de minsta obligatoriska metoderna registrerade ser de en felsida som leder dem att begära att en administratör återställer sitt lösenord.

#### <a name="mobile-app-and-sspr"></a>Mobilapp och SSPR

När du använder en mobilapp, till exempel Microsoft Authenticator-appen, som en metod för återställning av lösenord bör du vara medveten om följande förbehåll:

* När administratörer kräver att en metod används för att återställa ett lösenord är verifieringskod det enda tillgängliga alternativet.
* När administratörer kräver två metoder för att återställa ett lösenord kan användarna använda **ANTINGEN** meddelande **eller** verifieringskod utöver andra aktiverade metoder.

| Antal metoder som krävs för att återställa | En | Två |
| :---: | :---: | :---: |
| Tillgängliga funktioner för mobilappar | Kod | Kod eller meddelande |

Användare har inte möjlighet att registrera sin mobilapp när de registrerar [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)sig för återställning av lösenord med självbetjäning från . Användare kan registrera sin [https://aka.ms/mfasetup](https://aka.ms/mfasetup)mobilapp på eller i den [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)nya förhandsversionen av säkerhetsinformationsregistreringen på .

> [!WARNING]
> Du måste aktivera den [konvergerade registreringen för återställning av lösenord för självbetjäning och Azure Multi-Factor Authentication (Offentlig förhandsversion)](concept-registration-mfa-sspr-converged.md) innan användarna kan komma åt den nya upplevelsen på [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo).

> [!IMPORTANT]
> Autentiseringsappen kan inte väljas som den enda autentiseringsmetoden när du konfigurerar en princip med en grind. På samma sätt kan autentiseringsappen och endast en ytterligare metod inte väljas när du konfigurerar en princip med två grindar.
> När du sedan konfigurerar SSPR-principer som innehåller autentiseringsappen som en metod, bör minst en ytterligare metod väljas när du konfigurerar en princip med en 1-port, och minst två ytterligare metoder bör väljas när du konfigurerar en 2-portprincip.
> Anledningen till detta krav är att den aktuella SSPR-registreringsupplevelsen inte innehåller möjligheten att registrera autentiseringsappen. Alternativet för att registrera autentiseringsappen ingår i den nya [konvergerade registreringen för återställning av lösenord för självbetjäning och Azure Multi-Factor Authentication (Offentlig förhandsversion).](concept-registration-mfa-sspr-converged.md)
> Om du tillåter principer som endast använder autentiseringsappen (för 1-gate-principer) eller autentiseringsappen och endast en ytterligare metod (för 2-portarsprinciper) kan det leda till att användare blockeras från att registrera sig för SSPR tills de har konfigurerats för att använda den nya registreringserfarenhet.

### <a name="change-authentication-methods"></a>Ändra autentiseringsmetoder

Om du börjar med en princip som bara har en autentiseringsmetod som krävs för återställning eller upplåsning registrerad och du ändrar det till två metoder, vad händer då?

| Antal registrerade metoder | Antal metoder som krävs | Resultat |
| :---: | :---: | :---: |
| 1 eller fler | 1 | **Kunna** återställa eller låsa upp |
| 1 | 2 | **Det går inte** att återställa eller låsa upp |
| 2 eller fler | 2 | **Kunna** återställa eller låsa upp |

Om du ändrar vilka typer av autentiseringsmetoder som en användare kan använda kan du oavsiktligt hindra användare från att använda SSPR om de inte har den minsta mängden data som är tillgängliga.

Exempel:
1. Den ursprungliga principen är konfigurerad med två autentiseringsmetoder som krävs. Den använder bara kontorets telefonnummer och säkerhetsfrågor. 
2. Administratören ändrar principen så att den inte längre använder säkerhetsfrågorna, men tillåter användning av en mobiltelefon och ett alternativt e-postmeddelande.
3. Användare utan mobiltelefon eller alternativa e-postfält som fylls i kan inte återställa sina lösenord.

## <a name="registration"></a>Registrering

### <a name="require-users-to-register-when-they-sign-in"></a>Kräv att användare registrerar sig när de loggar in

Om du aktiverar det här alternativet måste en användare slutföra registreringen om återställning av lösenord om de loggar in på program med Azure AD. Det här arbetsflödet innehåller följande program:

* Office 365
* Azure Portal
* Åtkomstpanel
* Federerade applikationer
* Anpassade program med Azure AD

När registreringen är inaktiverad kan användare registrera sig manuellt. De kan [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) antingen besöka eller välja länken **Registrera för återställning av lösenord** under fliken **Profil** på åtkomstpanelen.

> [!NOTE]
> Användare kan stänga registreringsportalen för lösenordsåterställning genom att välja **avbryt** eller genom att stänga fönstret. Men de uppmanas att registrera sig varje gång de loggar in tills de är klara med sin registrering.
>
> Det här avbrottet bryter inte användarens anslutning om de redan är inloggade.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Ange antalet dagar innan användarna uppmanas att bekräfta sin autentiseringsinformation igen

Det här alternativet bestämmer tidsperioden mellan att ange och bekräfta autentiseringsinformation och är endast tillgänglig om du aktiverar alternativet **Kvitta användare för att registrera sig när du loggar in.**

Giltiga värden är 0 till 730 dagar, med "0" vilket innebär att användarna aldrig uppmanas att bekräfta sin autentiseringsinformation.

## <a name="notifications"></a>Meddelanden

### <a name="notify-users-on-password-resets"></a>Meddela användare om lösenordsåterställning

Om det här alternativet är inställt på **Ja**får användare som återställer sitt lösenord ett e-postmeddelande om att deras lösenord har ändrats. E-postmeddelandet skickas via SSPR-portalen till deras primära och alternativa e-postadresser som finns registrerade i Azure AD. Ingen annan meddelas om återställningshändelsen.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Meddela alla administratörer när andra administratörer återställer sina lösenord

Om det här alternativet är inställt på **Ja**får *alla administratörer* ett e-postmeddelande till sin primära e-postadress i Azure AD. E-postmeddelandet meddelar dem att en annan administratör har ändrat sitt lösenord med hjälp av SSPR.

Exempel: Det finns fyra administratörer i en miljö. Administratör A återställer sitt lösenord med hjälp av SSPR. Administratörer B, C och D får ett e-postmeddelande som varnar dem om återställning av lösenord.

## <a name="on-premises-integration"></a>Lokal integration

Om du installerar, konfigurerar och aktiverar Azure AD Connect har du följande ytterligare alternativ för lokala integreringar. Om dessa alternativ är nedtonade har tillbakaskrivningen inte konfigurerats korrekt. Mer information finns i [Konfigurera tillbakaskrivning av lösenord](howto-sspr-writeback.md).

![Att validera tillbakaskrivning av lösenord är aktiverat och arbete][Writeback]

På den här sidan visas en snabb status för den lokala återskrivningsklienten, ett av följande meddelanden visas baserat på den aktuella konfigurationen:

* Din lokala tillbakaskrivningsklient är igång.
* Azure AD är online och är anslutet till din lokala tillbakaskrivningsklient. Det ser dock ut som den installerade versionen av Azure AD Connect är inaktuella. Överväg [att uppgradera Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) för att säkerställa att du har de senaste anslutningsfunktionerna och viktiga buggfixar.
* Tyvärr kan vi inte kontrollera din lokala tillbakaskrivningsklientstatus eftersom den installerade versionen av Azure AD Connect är inkopplad. [Uppgradera Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) för att kunna kontrollera din anslutningsstatus.
* Tyvärr ser det ut som vi inte kan ansluta till din lokala tillbakaskrivning klient just nu. [Felsöka Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) för att återställa anslutningen.
* Tyvärr kan vi inte ansluta till din lokala tillbakaskrivningsklient eftersom tillbakaskrivning av lösenord inte har konfigurerats korrekt. [Konfigurera tillbakaskrivning av lösenord](howto-sspr-writeback.md) för att återställa anslutningen.
* Tyvärr ser det ut som vi inte kan ansluta till din lokala tillbakaskrivning klient just nu. Detta kan bero på tillfälliga problem på vår sida. Om problemet kvarstår [felsöker Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) för att återställa anslutningen.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Skriv tillbaka lösenord till din lokala katalog

Den här kontrollen avgör om tillbakaskrivning av lösenord är aktiverat för den här katalogen. Om tillbakaskrivningen är aktiverad anger den status för den lokala återskrivningstjänsten. Den här kontrollen är användbar om du tillfälligt vill inaktivera tillbakaskrivning av lösenord utan att behöva konfigurera om Azure AD Connect.

* Om växeln är inställd på **Ja**aktiveras tillbakaskrivning och federerade, direktautentisering eller synkroniserade användare med lösenordsh hash kan återställa sina lösenord.
* Om växeln är inställd på **Nej**inaktiveras tillbakaskrivningen och federerade, direktautentisering eller synkroniserade användare med lösenordsh hash kan inte återställa sina lösenord.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Tillåt användare att låsa upp konton utan att återställa sitt lösenord

Den här kontrollen anger om användare som besöker portalen för återställning av lösenord ska ges möjlighet att låsa upp sina lokala Active Directory-konton utan att behöva återställa sitt lösenord. Som standard låser Azure AD upp konton när de utför en återställning av lösenord. Du använder den här inställningen för att separera dessa två åtgärder.

* Om den är inställd på **Ja**får användarna möjlighet att återställa sitt lösenord och låsa upp kontot, eller låsa upp sitt konto utan att behöva återställa lösenordet.
* Om den är inställd på **Nej**kan användarna bara utföra en kombinerad återställning av lösenord och kontoupplåsning.

### <a name="on-premises-active-directory-password-filters"></a>Lokala Lösenordsfilter för Active Directory

Azure AD-självbetjäningslösenordsåterställning utför motsvarigheten till en administratörsinitierad lösenordsåterställning i Active Directory. Om du använder ett lösenordsfilter från tredje part för att framtvinga anpassade lösenordsregler och du kräver att det här lösenordsfiltret kontrolleras under azure ad-återställning av lösenord med självbetjäning, kontrollerar du att lösenordsfiltret från tredje part är konfigurerad för att tillämpas i scenario för återställning av lösenord. [Azure AD-lösenordsskydd för Active Directory](concept-password-ban-bad-on-premises.md) i Windows Server stöds som standard.

## <a name="password-reset-for-b2b-users"></a>Återställning av lösenord för B2B-användare

Återställning och ändring av lösenord stöds fullt ut i alla B2B-konfigurationer (Business-to-Business). B2B-återställning av användarlösenord stöds i följande tre fall:

* **Användare från en partnerorganisation med en befintlig Azure AD-klient:** Om organisationen du samarbetar med har en befintlig Azure AD-klientorganisation respekterar vi *alla principer för återställning av lösenord som är aktiverade på den klienten*. För att lösenordsåterställning ska fungera behöver partnerorganisationen bara se till att Azure AD SSPR är aktiverat. Det finns ingen extra kostnad för Office 365-kunder, och det kan aktiveras genom att följa stegen i vår [Komma igång med lösenordshanteringsguide.](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords)
* **Användare som registrerar sig via** självbetjäningsanmälan: Om den organisation du samarbetar med använde registreringsfunktionen för [självbetjäning](../users-groups-roles/directory-self-service-signup.md) för att komma in i en klientorganisation låter vi dem återställa lösenordet med den e-postadress de registrerade.
* **B2B-användare:** Alla nya B2B-användare som skapas med hjälp av de nya [Azure AD B2B-funktionerna](../active-directory-b2b-what-is-azure-ad-b2b.md) kan också återställa sina lösenord med e-postmeddelandet som de registrerade under inbjudningsprocessen.

Testa det här scenariot genom att gå till https://passwordreset.microsoftonline.com med en av dessa partneranvändare. Om de har ett alternativt e-postmeddelande eller ett e-postmeddelande med autentisering definierat fungerar återställning av lösenord som förväntat.

> [!NOTE]
> Microsoft-konton som har beviljats gäståtkomst till din Azure AD-klientorganisation, till exempel de från Hotmail.com, Outlook.com eller andra personliga e-postadresser, kan inte använda Azure AD SSPR. De måste återställa sitt lösenord med hjälp av informationen i artikeln [När du inte kan logga in på ditt Microsoft-konto.](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

## <a name="next-steps"></a>Nästa steg

Följande artiklar ger ytterligare information om lösenordsåterställning via Azure AD:

* [Hur gör jag för att slutföra en lyckad distribution av SSPR?](howto-sspr-deployment.md)
* [Återställa eller ändra ditt lösenord](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrera för återställning av lösenord med självbetjäning](../user-help/active-directory-passwords-reset-register.md)
* [Har du en fråga om licensiering?](concept-sspr-licensing.md)
* [Vilka data används av SSPR och vilka data bör du fylla i för dina användare?](howto-sspr-authenticationdata.md)
* [Vilka autentiseringsmetoder är tillgängliga för användarna?](concept-sspr-howitworks.md#authentication-methods)
* [Vilka principalternativ finns för SSPR?](concept-sspr-policy.md)
* [Vad är tillbakaskrivning av lösenord och vad är intresserat med det?](howto-sspr-writeback.md)
* [Hur gör jag för att rapportera på aktivitet i SSPR?](howto-sspr-reporting.md)
* [Vad är alla alternativ i SSPR och vad betyder de?](concept-sspr-howitworks.md)
* [Jag tror att något är trasigt. Hur felsöker jag SSPR?](active-directory-passwords-troubleshoot.md)
* [Jag har en fråga som inte besvarades någon annanstans](active-directory-passwords-faq.md)

[Authentication]: ./media/concept-sspr-howitworks/manage-authentication-methods-for-password-reset.png "Azure AD-autentiseringsmetoder som är tillgängliga och kvantitet som krävs"
[Writeback]: ./media/concept-sspr-howitworks/troubleshoot-on-premises-integration-writeback.png "Konfiguration och felsökningsinformation för lokal integrering av lösenord"
