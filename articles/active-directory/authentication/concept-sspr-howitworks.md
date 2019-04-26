---
title: Lösenordsåterställning via självbetjäning djupdykning - Azure Active Directory
description: Hur Självbetjäning för lösenordsåterställning fungerar
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47a6f475b5f1152850ec918b196883c6974f4d95
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60415641"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>Så här fungerar det: Självåterställning av lösenord i Azure AD

Hur lösenordsåterställning via självbetjäning (SSPR) arbete? Vad betyder det alternativet i gränssnittet? Fortsätt att läsa och lär dig mer om Azure Active Directory (Azure AD) SSPR.

|     |
| --- |
| Mobilapp-meddelande och kod för mobilapp som metoder för lösenord för självbetjäning i Azure AD lösenordsåterställning är allmänt tillgänglig förhandsversionsfunktioner i Azure Active Directory. Mer information om förhandsversioner finns [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="how-does-the-password-reset-portal-work"></a>Hur lösenordsåterställning portal arbete?

När en användare går till portalen för lösenordsåterställning, ett arbetsflöde har startats för att fastställa:

   * Hur sidan vara lokaliserade?
   * Är användarkontot som är giltig?
   * Vilken organisation användaren tillhör?
   * Där hanteras användarens lösenord?
   * Användaren är licensierad för att använda funktionen?

Läs igenom följande steg för att lära dig om logiken bakom lösenordet för lösenordsåterställning:

1. Användaren väljer den **kan inte komma åt ditt konto** länkar eller går direkt till [ https://aka.ms/sspr ](https://passwordreset.microsoftonline.com).
   * Utifrån Webbläsarspråk återges upplevelsen i det aktuella språket. Lösenordsåterställningen är översatt till samma språk som har stöd för Office 365.
   * Visa lösenordsåterställningen portalen i en annan lokaliserade Lägg till ”? mkt =” i slutet av lösenordet Webbadress för återställning med exemplet som följer lokalisera till spanska [ https://passwordreset.microsoftonline.com/?mkt=es-us ](https://passwordreset.microsoftonline.com/?mkt=es-us).
2. Användaren anger ett användar-ID och skickar en captcha.
3. Azure AD verifierar att användaren ska kunna använda den här funktionen genom att göra följande kontroller:
   * Kontrollerar att användaren har den här funktionen är aktiverad och har en Azure AD-licens.
     * Om användaren inte har den här funktionen är aktiverad eller har en tilldelad licens, uppmanas användaren att kontakta administratören om du vill återställa sina lösenord.
   * Kontrollerar att användaren har rätt autentiseringsmetoder som definierats på sitt konto i enlighet med systemadministratören.
     * Om principen kräver bara en av metoderna, sedan ser till att användaren har rätt data som definierats för minst en av de autentiseringsmetoder som aktiveras av administratörsprincipen.
       * Om autentiseringsmetoderna som inte har konfigurerats, rekommenderas du att kontakta administratören om du vill återställa sina lösenord.
     * Om principen kräver två metoder, sedan ser till att användaren har rätt data som definierats för minst två av de autentiseringsmetoder som aktiveras av administratörsprincipen.
       * Om autentiseringsmetoderna som inte har konfigurerats, rekommenderas du att kontakta administratören om du vill återställa sina lösenord.
     * Om en administratör för Azure-roll tilldelas till användaren, tillämpas starkt lösenord för två gate-principen. Mer information om den här principen finns i avsnittet [administratören återställa principen skillnader](concept-sspr-policy.md#administrator-reset-policy-differences).
   * Kontrollerar om användarens lösenord måste hanteras lokalt (federerade direkt autentisering eller synkroniseras lösenords-hash).
     * Om tillbakaskrivning av distribueras och användarens lösenord måste hanteras lokalt, tillåts användaren att fortsätta att autentisera och återställa sina lösenord.
     * Om tillbakaskrivning av inte har distribuerats och användarens lösenord måste hanteras lokalt, uppmanas användaren att kontakta administratören om du vill återställa sina lösenord.
4. Om det fastställs att användaren ska kunna återställa sina lösenord har leds användaren genom återställning av processen.

## <a name="authentication-methods"></a>Autentiseringsmetoder

Om SSPR är aktiverad, måste du välja minst en av följande alternativ för autentiseringsmetoder. Ibland hör du de här alternativen som kallas ”gates”. Vi rekommenderar starkt att som du **väljer minst två autentiseringsmetoder** så att användarna har mer flexibilitet om de inte går att komma åt en när de behöver den. Mer information om de metoder som anges nedan finns i artikeln [vad är autentiseringsmetoder?](concept-authentication-methods.md).

* Mobilappmeddelande (förhandsgranskning)
* Mobilappkod (förhandsversion)
* E-post
* Mobiltelefon
* Arbetstelefon
* Säkerhetsfrågor

Användare kan bara återställa sina lösenord, om de har data som finns i de autentiseringsmetoder som administratören har aktiverat.

> [!IMPORTANT]
> Från och med mars 2019 telefonsamtal alternativen kommer inte att MFA och SSPR-användare i kostnadsfria/utvärderingsversion Azure AD-klienter. SMS-meddelanden som inte påverkas av den här ändringen. Telefonsamtal fortsätter att vara tillgängliga för användare i betalda Azure AD-klienter. Den här ändringen påverkar endast kostnadsfria/utvärderingsversion Azure AD-klienter.

> [!WARNING]
> Konton som har tilldelats Azure-administratörsroller kommer att behöva använda metoder som definierats i avsnittet [administratören återställa principen skillnader](concept-sspr-policy.md#administrator-reset-policy-differences).

![Autentisering metoder val i Azure portal][Authentication]

### <a name="number-of-authentication-methods-required"></a>Antal autentiseringsmetoder krävs

Det här alternativet anger det minsta antalet tillgängliga autentiseringsmetoderna eller portar som en användare måste gå igenom återställa eller låsa upp sina lösenord. Det kan ställas in till en eller två.

Användare kan välja att ange flera autentiseringsmetoder om administratören aktiverar som autentiseringsmetod.

Om en användare inte har de minsta nödvändiga metoderna som registrerats, visas en felsida som uppmanar dem att begära att en administratör återställa sina lösenord.

#### <a name="mobile-app-and-sspr-preview"></a>Mobilappen och SSPR (förhandsversion)

När du använder en mobilapp som Microsoft Authenticator-appen som en metod för återställning av lösenord, bör du vara medveten om följande villkor:

* När administratörer behöver en metod används för att återställa ett lösenord, verifieringskoden är det enda tillgängliga alternativet.
* När administratörer kräver två metoder används för att återställa ett lösenord, användare ska kunna använda **antingen** meddelande **eller** Verifieringskod utöver eventuella övriga aktiverat metoder.

| Antal metoder som krävs för återställning | En | Två |
| :---: | :---: | :---: |
| Mobilapp-funktioner som är tillgängliga | Kod | Kod- eller Meddelandeinställningar |

Användarna behöver inte alternativet för att registrera sina mobila app när du registrerar dig för lösenordsåterställning via självbetjäning från [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup). Användare kan registrera sina mobila appar på [ https://aka.ms/mfasetup ](https://aka.ms/mfasetup), eller i den nya förhandsversionen vid registrering för information av security [ https://aka.ms/setupsecurityinfo ](https://aka.ms/setupsecurityinfo).

> [!WARNING]
> Du måste aktivera den [konvergerat registrering för lösenordsåterställning via självbetjäning och Azure Multi-Factor Authentication (förhandsversion)](concept-registration-mfa-sspr-converged.md) innan användare kan komma åt den nya upplevelsen på [ https://aka.ms/setupsecurityinfo ](https://aka.ms/setupsecurityinfo).

### <a name="change-authentication-methods"></a>Ändra autentiseringsmetoder

Om du startar med en princip som har endast en nödvändig autentisering-metod för att återställa eller låsa upp registrerad och du ändrar att två metoderna, vad som händer?

| Antal metoder som har registrerats | Antal metoder som krävs | Resultat |
| :---: | :---: | :---: |
| 1 eller mer | 1 | **Kan** återställa eller låsa upp |
| 1 | 2 | **Det går inte** återställa eller låsa upp |
| minst 2 | 2 | **Kan** återställa eller låsa upp |

Om du ändrar vilka typer av autentiseringsmetoder som en användare kan använda, kan du oavsiktligt hindra användare från att kunna använda SSPR om de inte har den minsta mängden data som är tillgängliga.

Exempel:
1. Den ursprungliga principen är konfigurerad med två autentiseringsmetoder som krävs. Används endast arbetstelefonnummer och säkerhetsfrågorna. 
2. Administratören ändrar principen för att inte längre använda säkerhetsfrågorna, men tillåter användning av en mobiltelefon och alternativa e-post.
3. Användare utan mobiltelefon eller alternativa e-fält kan inte återställa sina lösenord.

## <a name="registration"></a>Registrering

### <a name="require-users-to-register-when-they-sign-in"></a>Kräv att användare registrerar vid inloggning

Aktivera det här alternativet måste en användare för att slutföra registreringen för lösenordsåterställning om de loggar in på de program som använder Azure AD. Det här arbetsflödet innehåller följande program:

* Office 365
* Azure Portal
* Åtkomstpanel
* Federerade program
* Anpassade program med hjälp av Azure AD

När kräver registrering har inaktiverats kan registrera användare manuellt. De kan antingen besök [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup) eller Välj den **registrera för återställning av lösenord** länka den **profil** fliken i åtkomstpanelen.

> [!NOTE]
> Användare kan stänga registreringsportalen för lösenordsåterställning genom att välja **Avbryt** eller genom att stänga fönstret. Men uppmanas de att registrera varje gång de loggar in förrän de har slutfört registreringen.
>
> Det här avbrottet bryta inte anslutningen om de redan har loggat in.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Ange antal dagar innan användare uppmanas att bekräfta sin autentiseringsinformation

Det här alternativet anger tidsperioden mellan att och reconfirming autentiseringsinformationen och är endast tillgängligt om du aktiverar den **kräver att användare registrerar vid inloggning** alternativet.

Giltiga värden är 0 till 730 dagar ”0” vilket innebär att användarna uppmanas aldrig att bekräfta sin autentiseringsinformation.

## <a name="notifications"></a>Meddelanden

### <a name="notify-users-on-password-resets"></a>Meddela användare om lösenordsåterställning

Om det här alternativet är inställt på **Ja**, och sedan användarna återställa sina lösenord får ett e-postmeddelande om att deras lösenord har ändrats. E-postmeddelandet skickas via SSPR-portalen till sina primära och alternativa e-postadresser som finns i filen i Azure AD. Ingen annan meddelas om händelsen återställning.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Meddela alla administratörer när andra administratörer återställer sina lösenord

Om det här alternativet är inställt på **Ja**, sedan *alla administratörer* får ett e-postmeddelande till den primära e-postadressen på filen i Azure AD. E-postmeddelandet meddelar dem en annan administratör har ändrat sitt lösenord genom att använda SSPR.

Exempel: Det finns fyra administratörer i en miljö. Administratör A återställer sitt lösenord genom att använda SSPR. Administratörer B, C och D får ett e-postmeddelande som varningar för återställning av lösenord.

## <a name="on-premises-integration"></a>Lokal integration

Om du installerar, konfigurerar och aktiverar Azure AD Connect har du följande ytterligare alternativ för lokal integrationer. Om dessa alternativ är nedtonade har sedan tillbakaskrivning av inte konfigurerats korrekt. Mer information finns i [konfigurera tillbakaskrivning av lösenord](howto-sspr-writeback.md).

![Verifiera tillbakaskrivning av lösenord är aktiverad och fungerar][Writeback]

Den här sidan innehåller en snabb status för den lokala tillbakaskrivningsklient, något av följande meddelanden visas baserat på den aktuella konfigurationen:

* Din lokala tillbakaskrivningsklient är igång.
* Azure AD är online och är ansluten till din lokala tillbakaskrivningsklient. Det verkar dock som den installera versionen av Azure AD Connect är inaktuell. Överväg att [uppgradera Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) så att du har de senaste anslutningsfunktionerna och viktiga felkorrigeringar.
* Tyvärr kan vi Kontrollera din lokala tillbakaskrivningsklient eftersom den installerade versionen av Azure AD Connect är inaktuell. [Uppgradera Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) för att kunna kontrollera din anslutningsstatus.
* Det verkar tyvärr som vi inte kan ansluta till din lokala tillbakaskrivningsklient just nu. [Felsöka Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) att återställa anslutningen.
* Vi kan tyvärr inte ansluta till din lokala tillbakaskrivningsklient eftersom tillbakaskrivning av lösenord inte har konfigurerats korrekt. [Konfigurera tillbakaskrivning av lösenord](howto-sspr-writeback.md) att återställa anslutningen.
* Det verkar tyvärr som vi inte kan ansluta till din lokala tillbakaskrivningsklient just nu. Detta kan bero på tillfälliga problem hos oss. Om problemet kvarstår [felsöka Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) att återställa anslutningen.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Skriv tillbaka lösenord till din lokala katalog

Den här kontrollen avgör om tillbakaskrivning av lösenord är aktiverat för den här katalogen. Om tillbakaskrivning av finns på, anger status för en lokal tillbakaskrivning av tjänsten. Den här kontrollen är användbart om du tillfälligt vill inaktivera tillbakaskrivning av lösenord utan att behöva konfigurera om Azure AD Connect.

* Om växeln har angetts till **Ja**sedan tillbakaskrivning aktiveras och autentisering för federerade, direkt eller lösenordshash synkroniserade användare ska kunna återställa sina lösenord.
* Om växeln har angetts till **nr**sedan tillbakaskrivning är inaktiverad och autentisering för federerade, direkt eller lösenordshash synkroniserade användare går inte att återställa sina lösenord.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Tillåt användare att låsa upp konton utan att återställa sitt lösenord

Den här kontrollen anger om användare som besöker portalen för återställning av lösenord ska ges alternativet att låsa upp sina lokala Active Directory-konton utan att behöva återställa sina lösenord. Som standard låser Azure AD upp konton när den utför en återställning av lösenord. Du kan använda den här inställningen för att avgränsa de två åtgärderna.

* Om inställd **Ja**, och användarna har du möjlighet att återställa sina lösenord och låsa upp kontot eller att låsa upp sitt konto utan att behöva återställa lösenordet.
* Om inställd **nr**, användare är sedan endast att kunna utföra en kombinerad lösenordsåterställning och kontoupplåsning igen.

### <a name="on-premises-active-directory-password-filters"></a>Den lokala Active Directory-lösenordsfilter

Lösenord för självbetjäning av Azure AD lösenordsåterställning utför samma sak som en administratör-initierad för återställning av lösenord i Active Directory. Om du använder ett för tredje parts-lösenordsfilter för att tillämpa anpassade lösenordsregler och du kräver att den här lösenordsfiltret har markerats under Azure AD via Självbetjäning för återställning av lösenord, kontrollera att filtret-lösning från tredje part lösenord har konfigurerats för att använda i den adminlösenord återställa scenariot. [Azure AD-lösenordsskydd för Windows Server Active Directory](concept-password-ban-bad-on-premises.md) stöds som standard.

## <a name="password-reset-for-b2b-users"></a>Återställning av lösenord för B2B-användare

Återställning av lösenord och ändrar stöds helt för alla konfigurationer för business-to-business (B2B). B2B återställning av användarlösenord stöds i följande tre fall:

* **Användare från en partnerorganisation med en befintlig Azure AD-klient**: Om du samarbetar med organisationen har en befintlig Azure AD-klient, vi *respekterar principerna för lösenordsåterställning är aktiverade på den klienten*. För lösenordsåterställning för att fungera behöver partnerorganisationen bara se till att Azure AD SSPR är aktiverad. Det finns ingen extra kostnad för Office 365-kunder och den kan aktiveras genom att följa stegen i vår [Kom igång med lösenordshantering](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords) guide.
* **Användare som registrerar sig via** självanmälan: Om organisationen du samarbetar med den [självanmälan](../users-groups-roles/directory-self-service-signup.md) funktion för att komma åt en klient, vi låter dem återställa lösenord med e-postadress som de registrerade.
* **B2B-användare**: Alla nya B2B-användare som skapats med hjälp av den nya [Azure AD B2B-funktioner](../active-directory-b2b-what-is-azure-ad-b2b.md) kommer också att kunna återställa sina lösenord med e-postadress som de registrerade under inbjudan.

Om du vill testa det här scenariot, går du till https://passwordreset.microsoftonline.com med någon av dessa partneranvändare. Om de har en alternativ e-postadress eller autentisering e-post som definierats för lösenordsåterställning fungerar som förväntat.

> [!NOTE]
> Microsoft-konton som har beviljats åtkomst till Azure AD-klienten, till exempel från Hotmail.com, Outlook.com eller andra personliga e-postadresser kan inte använda Azure AD SSPR. De behöver för att återställa sina lösenord med hjälp av informationen som finns i den [när du inte logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artikeln.

## <a name="next-steps"></a>Nästa steg

Följande artiklar ger ytterligare information om lösenordsåterställning via Azure AD:

* [Hur gör jag för att slutföra en lyckad distribution av SSPR?](howto-sspr-deployment.md)
* [Återställ eller ändra ditt lösenord](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrera för återställning av lösenord för självbetjäning](../user-help/active-directory-passwords-reset-register.md)
* [Har du en fråga om licensiering?](concept-sspr-licensing.md)
* [Vilka data används av SSPR och vilka data bör du fylla i för dina användare?](howto-sspr-authenticationdata.md)
* [Vilka autentiseringsmetoder är tillgängliga för användarna?](concept-sspr-howitworks.md#authentication-methods)
* [Vilka principalternativ finns för SSPR?](concept-sspr-policy.md)
* [Vad är tillbakaskrivning av lösenord och vad är intresserat med det?](howto-sspr-writeback.md)
* [Hur gör jag för att rapportera på aktivitet i SSPR?](howto-sspr-reporting.md)
* [Vad är alla alternativ i SSPR och vad betyder de?](concept-sspr-howitworks.md)
* [Jag tror att något har gått sönder. Hur gör jag för att felsöka SSPR?](active-directory-passwords-troubleshoot.md)
* [Jag har en fråga som inte besvarades någon annanstans](active-directory-passwords-faq.md)

[Authentication]: ./media/concept-sspr-howitworks/sspr-authentication-methods.png "Azure AD-autentiseringsmetoder som är tillgängliga och kvantitet som krävs"
[Writeback]: ./media/concept-sspr-howitworks/troubleshoot-writeback-running.png "Lokal integration lösenord tillbakaskrivning av konfiguration och felsökningsinformation"
