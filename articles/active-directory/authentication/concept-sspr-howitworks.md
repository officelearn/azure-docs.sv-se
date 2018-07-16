---
title: Lösenordsåterställning via självbetjäning hur det fungerar – Azure Active Directory
description: Azure lösenordsåterställning AD via självbetjäning djupdykning
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 04a446f43bd39ef7bfca590af67289813eab4032
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39048887"
---
# <a name="self-service-password-reset-in-azure-ad-deep-dive"></a>Lösenord för självbetjäning i Azure AD djupdykning

Hur lösenordsåterställning via självbetjäning (SSPR) arbete? Vad betyder det alternativet i gränssnittet? Fortsätt att läsa och lär dig mer om Azure Active Directory (Azure AD) SSPR.

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
2. Användaren anger ett användar-ID och skickar en captcha.
3. Azure AD verifierar att användaren ska kunna använda den här funktionen genom att göra följande kontroller:
   * Kontrollerar att användaren har den här funktionen är aktiverad och har en Azure AD-licens.
     * Om användaren inte har den här funktionen är aktiverad eller har en tilldelad licens, uppmanas användaren att kontakta administratören om du vill återställa sina lösenord.
   * Kontrollerar att användaren har rättigheten utmana data som definierats på sitt konto i enlighet med systemadministratören.
     * Om principen kräver endast en utmaning, sedan ser till att användaren har rätt data som definierats för minst en av de utmaningar som aktiveras av administratörsprincipen.
       * Om användaren utmaningen inte är konfigurerad, är användaren bäst att kontakta administratören om du vill återställa sina lösenord.
     * Om principen kräver två utmaningar, sedan ser till att användaren har rätt data som definierats för minst två av de utmaningar som aktiveras av administratörsprincipen.
       * Om användaren utmaningen inte är konfigurerad, är användaren bäst att kontakta administratören om du vill återställa sina lösenord.
   * Kontrollerar om användarens lösenord måste hanteras lokalt (federerade direkt autentisering eller synkroniseras lösenords-hash).
     * Om tillbakaskrivning av distribueras och användarens lösenord måste hanteras lokalt, tillåts användaren att fortsätta att autentisera och återställa sina lösenord.
     * Om tillbakaskrivning av inte har distribuerats och användarens lösenord måste hanteras lokalt, uppmanas användaren att kontakta administratören om du vill återställa sina lösenord.
4. Om det fastställs att användaren ska kunna återställa sina lösenord har leds användaren genom återställning av processen.

## <a name="authentication-methods"></a>Autentiseringsmetoder

Om SSPR är aktiverad, måste du välja minst en av följande alternativ för autentiseringsmetoder. Ibland hör du de här alternativen som kallas ”gates”. Vi rekommenderar starkt att du väljer minst två autentiseringsmetoder så att användarna har mer flexibilitet.

* E-post
* Mobiltelefon
* Arbetstelefon
* Säkerhetsfrågor

![Autentisering][Authentication]

### <a name="what-fields-are-used-in-the-directory-for-the-authentication-data"></a>Vilka fält som används i katalogen för autentisering?

* **Arbetstelefon**: motsvarar Arbetstelefon.
    * Användare kan inte ange det här fältet själva. Det måste definieras av en administratör.
* **Mobiltelefon**: motsvarar telefon för autentisering (inte synligt offentligt) eller mobiltelefonen (offentligt synligt).
    * Tjänsten söker efter telefon för autentisering först och sedan infaller under tillbaka till mobiltelefonen om telefon för autentisering är finns inte.
* **Alternativ e-postadress**: motsvarar autentisering e-postmeddelandet (inte synligt offentligt) eller alternativa e-postmeddelandet.
    * Tjänsten ser ut för e-post för autentisering först och sedan växlas tillbaka till den alternativa e-postadress.

Som standard synkroniseras endast molnet attribut arbetstelefon och mobiltelefon med din molnkatalog från din lokala katalog för autentiseringsdata.

Användare kan bara återställa sina lösenord, om de har data som finns i de autentiseringsmetoder som administratören har aktiverat och kräver.

Om användarna inte vill ha sina mobila telefonnummer som ska visas i katalogen, men fortfarande vill använda den för återställning av lösenord, bör administratörer inte fyller den i katalogen. Användare bör sedan fylla i sina **Autentiseringstelefon** attributet den [registreringsportalen för lösenordsåterställning](https://aka.ms/ssprsetup). Administratörer kan se den här informationen i användarens profil, men det publiceras inte någon annanstans.

### <a name="the-number-of-authentication-methods-required"></a>Antalet autentiseringsmetoder krävs

Det här alternativet anger det minsta antalet tillgängliga autentiseringsmetoderna eller portar som en användare måste gå igenom återställa eller låsa upp sina lösenord. Det kan ställas in till en eller två.

Användare kan välja att ange flera autentiseringsmetoder om administratören aktiverar som autentiseringsmetod.

Om en användare inte har de minsta nödvändiga metoderna som registrerats, visas en felsida som uppmanar dem att begära att en administratör återställa sina lösenord.

#### <a name="change-authentication-methods"></a>Ändra autentiseringsmetoder

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
3. Användare utan mobiltelefon och alternativa e-fält kan inte återställa sina lösenord.

### <a name="how-secure-are-my-security-questions"></a>Hur säker är Mina säkerhetsfrågor?

Om du använder säkerhetsfrågor, bör du använda dem tillsammans med en annan metod. Säkerhetsfrågor kan vara mindre säkert än andra metoder, eftersom vissa kanske känner till svar på en annan användares frågor.

> [!NOTE] 
> Säkerhetsfrågor lagras säkert och privat på ett användarobjekt i katalogen och kan endast besvaras av användare under registreringen. Det går inte att en administratör att läsa eller ändra en användares frågorna och svaren.
>

### <a name="security-question-localization"></a>Security fråga lokalisering

De fördefinierade frågor som följer är lokaliserade till den fullständiga uppsättningen Office 365-språk och baseras på användarens webbläsare nationella inställningar:

* I vilken stad träffade du din första make/maka/partner?
* I vilken stad träffades dina föräldrar?
* I vilken stad bor ditt närmsta syskon?
* I vilken stad föddes din pappa?
* I vilken stad hade du ditt första jobb?
* I vilken stad föddes din mamma?
* Vilken stad befann du dig i på nyårsafton 2000?
* Vad hette din favoritlärare i gymnasiet i efternamn?
* Vad heter ett av de universitet du har ansökt till men aldrig gått på?
* Vad heter den plats där du hade din första bröllopsmottagning?
* Vilket är din pappas mellannamn?
* Vilken är din favoriträtt?
* Vad hette din mormor i för- och efternamn?
* Vilket är din mammas mellannamn?
* Vad är ditt äldsta syskon månad och år? (t.ex. November 1985)
* Vilket är ditt äldsta syskons mellannamn?
* Vad hette din farfar i för- och efternamn?
* Vilket är ditt yngsta syskons mellannamn?
* Vilken skola gick du i när du gick i sjätte klass?
* Vad hette din bästa barndomskompis i för- och efternamn?
* Vad hette din första kärlek i för- och efternamn?
* Vad hette din favoritlärare i grundskolan i efternamn?
* Av vilket märke och vilken modell var din första bil eller motorcykel?
* Vad hette den första skolan som du gick i?
* Vad hette det sjukhus där du föddes?
* Vad är adressen till ditt första barndomshem?
* Vad hette din barndomshjälte?
* Vad hette din favoritgosedjur?
* Vad hette ditt första husdjur?
* Vad hade du för smeknamn som barn?
* Vilken var din favoritsport i gymnasiet?
* Vilket var ditt första jobb?
* Vilka är de fyra sista siffrorna i det telefonnummer du hade som barn?
* Vad drömde du om att bli som vuxen när du var barn?
* Vilken är den mest berömda person som du har träffat?

### <a name="custom-security-questions"></a>Anpassade säkerhetsfrågor

Anpassade säkerhetsfrågor är inte lokaliserade för olika språk. Alla anpassade frågor visas på samma språk när det anges i gränssnittet administrativ användare, även om användarens webbläsare språkinställningar är olika. Om du behöver lokaliserade frågor, bör du använda fördefinierade frågor.

Den maximala längden på en anpassad säkerhetsfråga är 200 tecken.

Visa portalen för återställning av lösenord och frågor i en annan lokaliserade språk Lägg till ”? mkt =<Locale>” i slutet av lösenordet Webbadress för återställning med exemplet som följer lokalisera till spanska [ https://passwordreset.microsoftonline.com/?mkt=es-us ](https://passwordreset.microsoftonline.com/?mkt=es-us).

### <a name="security-question-requirements"></a>Säkerhetskrav för fråga

* Teckengränsen minsta svar är tre tecken.
* Gränsen för högsta svar tecken är 40 tecken.
* Användare kan inte svara på samma fråga mer än en gång.
* Användare kan inte ange samma svar på fler än en fråga.
* Alla teckenuppsättningen kan användas för att definiera frågorna och svar, inklusive Unicode-tecken.
* Antalet frågor som definierats måste vara större än eller lika med antalet frågor som krävs för att registrera.

## <a name="registration"></a>Registrering

### <a name="require-users-to-register-when-they-sign-in"></a>Kräv att användare registrerar vid inloggning

Om du vill aktivera det här alternativet om du har en användare som har aktiverats för återställning av lösenord att slutföra registreringen för lösenordsåterställning att logga in till program med hjälp av Azure AD. Detta omfattar följande:

* Office 365
* Azure Portal
* Åtkomstpanel
* Federerade program
* Anpassade program med hjälp av Azure AD

När kräver registrering har inaktiverats kan registrera användare fortfarande manuellt deras kontaktuppgifter. De kan antingen besök [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup) eller Välj den **registrera för återställning av lösenord** länka den **profil** fliken i åtkomstpanelen.

> [!NOTE]
> Användare kan stänga registreringsportalen för lösenordsåterställning genom att välja **Avbryt** eller genom att stänga fönstret. Men uppmanas de att registrera varje gång de loggar in förrän de har slutfört registreringen.
>
> Det innebära inte att användarens anslutning om de redan har loggat in.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Ange antal dagar innan användare uppmanas att bekräfta sin autentiseringsinformation

Det här alternativet anger tidsperioden mellan att och reconfirming autentiseringsinformationen och är endast tillgängligt om du aktiverar den **kräver att användare registrerar vid inloggning** alternativet.

Giltiga värden är 0 till 730 dagar ”0” vilket innebär att användarna uppmanas aldrig att bekräfta sin autentiseringsinformation.

## <a name="notifications"></a>Meddelanden

### <a name="notify-users-on-password-resets"></a>Meddela användare om lösenordsåterställning

Om det här alternativet är inställt på **Ja**, och användaren som återställer sitt lösenord får ett e-postmeddelande om att deras lösenord har ändrats. E-postmeddelandet skickas via SSPR-portalen till sina primära och alternativa e-postadresser som finns i filen i Azure AD. Ingen annan meddelas om återställning av den här händelsen.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Meddela alla administratörer när andra administratörer återställer sina lösenord

Om det här alternativet är inställt på **Ja**, sedan *alla administratörer* får ett e-postmeddelande till den primära e-postadressen på filen i Azure AD. E-postmeddelandet meddelar dem en annan administratör har ändrat sitt lösenord genom att använda SSPR.

Exempel: Det finns fyra administratörer i en miljö. Administratör A återställer sitt lösenord genom att använda SSPR. Administratörer B, C och D får ett e-postmeddelande som uppmanar för återställning av lösenord.

## <a name="on-premises-integration"></a>Lokal integration

Om du installerar, konfigurerar och aktiverar Azure AD Connect har du följande ytterligare alternativ för lokal integrationer. Om dessa alternativ är nedtonade har sedan tillbakaskrivning av inte konfigurerats korrekt. Mer information finns i [konfigurera tillbakaskrivning av lösenord](howto-sspr-writeback.md#configure-password-writeback).

![Tillbakaskrivning av][Writeback]

Den här sidan innehåller en snabb lokal tillbakaskrivning av klientens status visas något av följande meddelanden baserat på den aktuella konfigurationen:

* Din lokala tillbakaskrivningsklient är igång.
* Azure AD är online och är ansluten till din lokala tillbakaskrivningsklient. Det verkar dock som den installera versionen av Azure AD Connect är inaktuell. Överväg att [uppgradera Azure AD Connect](./../connect/active-directory-aadconnect-upgrade-previous-version.md) så att du har de senaste anslutningsfunktionerna och viktiga felkorrigeringar.
* Tyvärr kan vi Kontrollera din lokala tillbakaskrivningsklient eftersom den installerade versionen av Azure AD Connect är inaktuell. [Uppgradera Azure AD Connect](./../connect/active-directory-aadconnect-upgrade-previous-version.md) för att kunna kontrollera din anslutningsstatus.
* Det verkar tyvärr som vi inte kan ansluta till din lokala tillbakaskrivningsklient just nu. [Felsöka Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) att återställa anslutningen.
* Vi kan tyvärr inte ansluta till din lokala tillbakaskrivningsklient eftersom tillbakaskrivning av lösenord inte har konfigurerats korrekt. [Konfigurera tillbakaskrivning av lösenord](howto-sspr-writeback.md#configure-password-writeback) att återställa anslutningen.
* Det verkar tyvärr som vi inte kan ansluta till din lokala tillbakaskrivningsklient just nu. Detta kan bero på tillfälliga problem hos oss. Om problemet kvarstår [felsöka Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) att återställa anslutningen.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Skriv tillbaka lösenord till din lokala katalog

Den här kontrollen avgör om tillbakaskrivning av lösenord är aktiverat för den här katalogen. Om tillbakaskrivning av finns på, anger status för en lokal tillbakaskrivning av tjänsten. Detta är användbart om du vill att tillfälligt inaktivera tillbakaskrivning av lösenord utan att behöva konfigurera om Azure AD Connect.

* Om växeln har angetts till **Ja**sedan tillbakaskrivning aktiveras och autentisering för federerade, direkt eller lösenordshash synkroniserade användare ska kunna återställa sina lösenord.
* Om växeln har angetts till **nr**sedan tillbakaskrivning är inaktiverad och autentisering för federerade, direkt eller lösenordshash synkroniserade användare går inte att återställa sina lösenord.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Tillåt användare att låsa upp konton utan att återställa sitt lösenord

Den här kontrollen anger om användare som besöker portalen för återställning av lösenord ska ges alternativet att låsa upp sina lokala Active Directory-konton utan att behöva återställa sina lösenord. Som standard låser Azure AD upp konton när den utför en återställning av lösenord. Du kan använda den här inställningen för att avgränsa de två åtgärderna. 

* Om inställd **Ja**, och användarna har du möjlighet att återställa sina lösenord och låsa upp kontot eller att låsa upp sitt konto utan att behöva återställa lösenordet.
* Om inställd **nr**, användare är sedan endast att kunna utföra en kombinerad lösenordsåterställning och kontoupplåsning igen.

## <a name="how-does-password-reset-work-for-b2b-users"></a>Hur för lösenordsåterställning fungerar för B2B-användare?
Återställning av lösenord och ändrar stöds helt för alla konfigurationer för business-to-business (B2B). B2B återställning av användarlösenord stöds i följande tre fall:

   * **Användare från en partnerorganisation med en befintlig Azure AD-klient**: Om du samarbetar med organisationen har en befintlig Azure AD-klient, vi *respekterar principerna för lösenordsåterställning är aktiverade på den klienten*. För lösenordsåterställning för att fungera behöver partnerorganisationen bara se till att Azure AD SSPR är aktiverad. Det finns ingen extra kostnad för Office 365-kunder och den kan aktiveras genom att följa stegen i vår [Kom igång med lösenordshantering](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords) guide.
   * **Användare som registrerar sig via** självanmälan: om organisationen du samarbetar med används den [självanmälan](../users-groups-roles/directory-self-service-signup.md) funktion för att komma åt en klient, vi låter dem återställa lösenord med e-postadress som de registrerade.
   * **B2B-användare**: alla nya B2B-användare som skapats med hjälp av den nya [Azure AD B2B-funktioner](../active-directory-b2b-what-is-azure-ad-b2b.md) kommer också att kunna återställa sina lösenord med e-postadress som de registrerade under inbjudan.

Om du vill testa det här scenariot, går du till http://passwordreset.microsoftonline.com med någon av dessa partneranvändare. Om de har en alternativ e-postadress eller autentisering e-post som definierats för lösenordsåterställning fungerar som förväntat.

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
