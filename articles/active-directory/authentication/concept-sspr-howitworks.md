---
title: Självbetjäning för lösenordsåterställning hur fungerar – Azure Active Directory
description: Azure AD Självbetjäning för lösenordsåterställning ingående
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 6f755754097336fc97678940ea1fa0aa28315fab
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="self-service-password-reset-in-azure-ad-deep-dive"></a>Självbetjäning för återställning av lösenord i Azure AD ingående

Hur Självbetjäning för återställning av lösenord (SSPR) arbete? Vad innebär det alternativet i gränssnittet? Läs mer om Azure Active Directory (AD Azure) SSPR.

## <a name="how-does-the-password-reset-portal-work"></a>Hur återställning portal arbete av lösenord?

När en användare går till portalen för återställning av lösenord, ett arbetsflöde har inletts för att fastställa:

   * Hur sidan vara lokaliserade?
   * Är användarkonton som är giltig?
   * Vilken organisation användaren tillhör?
   * Där hanteras användarens lösenord?
   * Användaren är licensierad för att använda funktionen?

Läs igenom följande steg för att lära dig om logiken bakom lösenordet Återställ sida:

1. Användaren väljer den **kan inte komma åt ditt konto** länkar eller går direkt till [ https://aka.ms/sspr ](https://passwordreset.microsoftonline.com).
   * Baserat på webbläsarens språk återges upplevelsen i det aktuella språket. Lösenord Återställ upplevelse är lokaliserade till samma språk som har stöd för Office 365.
2. Användaren anger ett användar-ID och skickar en captcha.
3. Azure AD verifierar att användaren kan använda den här funktionen genom att göra följande:
   * Kontrollerar att användaren har den här funktionen är aktiverad och har en Azure AD-licens.
     * Om användaren inte har den här funktionen är aktiverad eller har tilldelats en licens, uppmanas användaren att kontakta administratören om du vill återställa sina lösenord.
   * Kontrollerar att användaren har rättigheten kräver data som definierats på sitt konto i enlighet med systemadministratören.
     * Om principen kräver bara en utmaning, sedan ser till att användaren har lämpliga data har definierats för minst en av de utmaningar som aktiveras av administratörsprincipen.
       * Om användaren challenge inte har konfigurerats bör användaren att kontakta administratören om du vill återställa sina lösenord.
     * Om principen kräver två utmaningar, sedan ser till att användaren har definierat för åtminstone två av de utmaningar som aktiveras av administratörsprincipen lämpliga data.
       * Om användaren challenge inte har konfigurerats bör användaren att kontakta administratören om du vill återställa sina lösenord.
   * Kontrollerar om användarens lösenord är hanteras lokalt (federerade, direkt-autentisering eller synkroniseras lösenords-hash).
     * Om tillbakaskrivning distribueras och användarens lösenord är hanteras lokalt, tillåts användaren att fortsätta att autentisera och återställa sina lösenord.
     * Om tillbakaskrivning distribueras inte och lösenordet är hanteras lokalt uppmanas användaren att kontakta administratören om du vill återställa sina lösenord.
4. Om det fastställs att användaren kan återställa sina lösenord har leds användaren genom återställning av processen.

## <a name="authentication-methods"></a>Autentiseringsmetoder

Om SSPR är aktiverat, måste du välja minst en av följande alternativ för autentiseringsmetoder. Ibland hör du dessa alternativ som kallas ”gates”. Vi rekommenderar starkt att du väljer minst två autentiseringsmetoder så att användarna har du mer flexibilitet.

* E-post
* Mobiltelefon
* Arbetstelefon
* Säkerhetsfrågor

![Autentisering][Authentication]

### <a name="what-fields-are-used-in-the-directory-for-the-authentication-data"></a>Vilka fält som används i katalogen för autentiseringsdata?

* **Arbetstelefon**: motsvarar Arbetstelefon.
    * Användare kan inte anges i det här fältet själva. Det måste definieras av administratören.
* **Mobiltelefon**: motsvarar telefon för autentisering (inte synligt offentligt) eller mobiltelefonen (synligt offentligt).
    * Tjänsten söker efter telefon för autentisering först och sedan faller tillbaka till mobiltelefonen om telefon för autentisering är finns inte.
* **Alternativ e-postadress**: motsvarar autentisering e-postmeddelandet (inte synligt offentligt) eller den alternativa e-postadress.
    * Tjänsten ser ut för e-post för autentisering först och sedan flyttas tillbaka till den alternativa e-postadress.

Som standard synkroniseras endast molnet attribut arbetstelefon och mobiltelefon till din molnkatalog från din lokala katalog för autentiseringsdata.

Användare kan bara återställa sina lösenord, om de har data som finns i de autentiseringsmetoder som administratören har aktiverat och kräver.

Om användarna inte vill att deras mobiltelefonnummer som ska visas i katalogen, men de vill använda för återställning av lösenord, Administratörer bör inte att fylla det i katalogen. Användare bör sedan fylla i sina **telefon för autentisering** attributet den [registreringsportalen för lösenordsåterställning](https://aka.ms/ssprsetup). Administratörer kan se den här informationen i användarens profil, men publiceras inte någon annanstans.

### <a name="the-number-of-authentication-methods-required"></a>Antalet autentiseringsmetoder krävs

Det här alternativet anger det minsta antalet tillgängliga autentiseringsmetoderna eller portar som en användare måste genomgå för att återställa eller låsa upp sitt lösenord. Det kan anges till en eller två.

Användare kan välja att ange flera autentiseringsmetoder om administratören aktiverar som autentiseringsmetod.

Om en användare inte har minsta nödvändiga metoder som registrerats, visas en felsida som hänvisar dem att begära att en administratör återställa sina lösenord.

#### <a name="change-authentication-methods"></a>Ändra autentiseringsmetod

Om du börjar med en princip som har endast en krävs autentiseringsmetoden för återställning eller låsa upp registrerade och du ändrar att två metoderna, vad som händer?

| Antal metoder som har registrerats | Antal metoder som krävs | Resultat |
| :---: | :---: | :---: |
| 1 eller fler | 1 | **Kan** att återställa eller låsa upp |
| 1 | 2 | **Det går inte** att återställa eller låsa upp |
| 2 eller högre | 2 | **Kan** att återställa eller låsa upp |

Om du ändrar typerna av autentiseringsmetoder som en användare kan använda, kan du oavsiktligt hindra användare från att kunna använda SSPR om de inte har den minsta mängden data som är tillgängliga.

Exempel: 
1. Den ursprungliga principen har konfigurerats med två autentiseringsmetoder krävs. Används endast den telefonnummer till arbetet och säkerhetsfrågorna. 
2. Administratören ändras för att inte längre använda säkerhetsfrågorna, men tillåter användning av en mobiltelefon och en alternativ e-post.
3. Användare utan mobiltelefon och alternativa e-fält kan inte återställa sina lösenord.

### <a name="how-secure-are-my-security-questions"></a>Hur säker är min säkerhetsfrågor?

Om du använder säkerhetsfrågor, rekommenderar vi använder dem tillsammans med en annan metod. Säkerhetsfrågor kan vara mindre säkert än andra metoder eftersom vissa personer kanske känner till svar på frågor för en annan användare.

> [!NOTE] 
> Säkerhetsfrågor lagras säkert och privat på ett användarobjekt i katalogen och kan endast besvaras av användare under registreringen. Det går inte att en administratör att läsa eller ändra en användares frågor och svar.
>

### <a name="security-question-localization"></a>Översättning av säkerhet fråga

De fördefinierade frågor som följer är lokaliserade till den fullständiga uppsättningen av Office 365-språk och baseras på användarens Webbläsarspråk:

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
* Vad är ditt äldsta syskon födelsedag månad och år? (t.ex. November 1985)
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

Anpassade säkerhetsfrågor är inte lokaliserade för olika språk. Alla anpassade frågor visas på samma språk som de har angetts i gränssnittet administrativ användare, även om användarens webbläsare språkinställningar är olika. Om du behöver lokaliserade frågor, bör du använda fördefinierade frågor.

Den maximala längden på en anpassad säkerhetsfråga är 200 tecken.

### <a name="security-question-requirements"></a>Fråga säkerhetskrav

* Den minsta svar teckengränsen är tre tecken.
* Den maximala svar teckengränsen är 40 tecken.
* Användare kan inte svara på samma fråga mer än en gång.
* Användare kan inte ange samma svaret på mer än en fråga.
* Alla teckenuppsättningen kan användas för att definiera frågor och svar, inklusive Unicode-tecken.
* Antalet frågor som definierats måste vara större än eller lika med antalet frågor som krävs för att registrera.

## <a name="registration"></a>Registrering

### <a name="require-users-to-register-when-they-sign-in"></a>Användaren måste registrera när de loggar in

Om du vill aktivera det här alternativet måste en användare som har aktiverats för lösenordsåterställning slutföra registreringen för lösenordsåterställning när de loggar in till program med hjälp av Azure AD. Detta omfattar följande:

* Office 365
* Azure Portal
* Åtkomstpanel
* Federerade program
* Anpassade program med hjälp av Azure AD

När kräver registrering är inaktiverat kan användare fortfarande manuellt registrera kontaktuppgifter. De kan antingen besök [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup) eller Välj den **registrera dig för lösenordsåterställning** länken under den **profil** fliken på åtkomstpanelen.

> [!NOTE]
> Användare kan stänga registreringsportalen för lösenordsåterställning genom att välja **Avbryt** eller genom att stänga fönstret. Men uppmanas de att registrera varje gång de loggar in förrän de har slutfört registreringen.
>
> Detta bryta inte anslutningen om de redan har loggat in.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Ange antalet dagar innan användarna uppmanas att bekräfta sin autentiseringsinformation

Det här alternativet anger tidsperioden mellan inställningen och reconfirming autentiseringsinformationen och är bara tillgängligt om du aktiverar den **användarna att registrera när du loggar in** alternativet.

Giltiga värden är 0 till 730 dagar ”0” vilket innebär att användarna aldrig ombeds bekräfta sina autentiseringsinformationen.

## <a name="notifications"></a>Meddelanden

### <a name="notify-users-on-password-resets"></a>Meddela användare om lösenordsåterställning

Om det här alternativet är inställt på **Ja**, och användaren som återställer sitt lösenord får ett e-postmeddelande till dem om att lösenordet har ändrats. E-postmeddelandet skickas via SSPR-portalen till sina primära och alternativa e-postadresser som finns i filen i Azure AD. Ingen annan får ett meddelande om återställning av den här händelsen.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Meddela alla administratörer när andra administratörer återställa sina lösenord

Om det här alternativet är inställt på **Ja**, sedan *alla administratörer* får ett e-postmeddelande till sina primära e-postadress på filen i Azure AD. E-postmeddelandet meddelar dem en annan administratör har ändrat sitt lösenord med hjälp av SSPR.

Exempel: Det finns fyra administratörer i en miljö. Administratören A återställer sitt lösenord med hjälp av SSPR. Administratörer B och C D får ett e-postmeddelande som meddelar dem om återställning av lösenord.

## <a name="on-premises-integration"></a>Lokal integration

Om du installerar, konfigurerar och aktiverar Azure AD Connect har du följande ytterligare alternativ för lokal integreringar. Om dessa alternativ är nedtonade har sedan tillbakaskrivning inte konfigurerats korrekt. Mer information finns i [konfigurera tillbakaskrivning av lösenord](howto-sspr-writeback.md#configure-password-writeback).

![Tillbakaskrivning][Writeback]

Den här sidan innehåller en snabb lokal tillbakaskrivning av klientens status något av följande meddelanden visas baserat på den aktuella konfigurationen:

* Lokal tillbakaskrivning-klient är aktiv och körs.
* Azure AD är online och är ansluten till din lokala tillbakaskrivning av klienten. Det verkar dock som den installerade versionen av Azure AD Connect är inaktuell. Överväg att [uppgraderar Azure AD Connect](./../connect/active-directory-aadconnect-upgrade-previous-version.md) så att du har de senaste funktionerna för anslutning och viktiga korrigeringarna.
* Vi kan inte tyvärr, kontrollera din lokala tillbakaskrivning klientstatus eftersom den installerade versionen av Azure AD Connect är inaktuell. [Uppgradera Azure AD Connect](./../connect/active-directory-aadconnect-upgrade-previous-version.md) för att kunna kontrollera anslutningsstatus för.
* Det verkar tyvärr som vi inte kan ansluta till din lokala tillbakaskrivning klient just nu. [Felsöka Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) att återställa anslutningen.
* Vi kan tyvärr inte ansluta till lokalt tillbakaskrivning klienten eftersom tillbakaskrivning av lösenord inte har konfigurerats korrekt. [Konfigurera tillbakaskrivning av lösenord](howto-sspr-writeback.md#configure-password-writeback) att återställa anslutningen.
* Det verkar tyvärr som vi inte kan ansluta till din lokala tillbakaskrivning klient just nu. Detta kan bero på tillfälliga problem hos oss. Om problemet kvarstår [felsöka Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) att återställa anslutningen.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Skriv tillbaka lösenord till din lokala katalog

Den här kontrollen bestämmer om tillbakaskrivning av lösenord är aktiverat för den här katalogen. Om tillbakaskrivning finns på, visar status för tjänsten lokalt tillbakaskrivning. Detta är användbart om du tillfälligt vill inaktivera tillbakaskrivning av lösenord utan att behöva konfigurera om Azure AD Connect.

* Om växeln har angetts till **Ja**sedan tillbakaskrivning aktiveras och federerade, genomströmning verifiering eller lösenord hash-synkroniserade användare ska kunna återställa sina lösenord.
* Om växeln har angetts till **nr**sedan tillbakaskrivning är inaktiverad och federerade, genomströmning verifiering eller lösenord hash-synkroniserade användare kan inte återställa sina lösenord.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Tillåt användare att låsa upp konton utan att återställa sina lösenord

Den här kontrollen anger om användare som besöker portalen för återställning av lösenord bör ges möjlighet att låsa upp sina lokala Active Directory-konton utan att behöva återställa sina lösenord. Standard Azure AD att låsa upp konton när den utför en återställning av lösenord. Du kan använda den här inställningen för att avgränsa de två åtgärderna. 

* Om värdet **Ja**, och sedan på användare möjlighet att återställa sina lösenord och låsa upp kontot eller att låsa upp sitt konto utan att återställa lösenordet.
* Om värdet **nr**, sedan användarna är bara att utföra en kombinerad lösenordsåterställning och kontoupplåsning igen.

## <a name="how-does-password-reset-work-for-b2b-users"></a>Hur lösenordsåterställning för B2B-användare?
Återställning av lösenord och ändrar stöds fullt ut för alla konfigurationer för business-to-business (B2B). B2B lösenordsåterställning för användare stöds i följande tre fall:

   * **Användare från en organisation med en befintlig Azure AD-klient**: Om du samarbetar med organisationen har en befintlig Azure AD-klient vi *respektera oavsett principer för återställning av lösenord är aktiverade på den klienten*. För lösenordsåterställning för att fungera måste partnerorganisationen bara se till att Azure AD SSPR är aktiverad. Det finns utan extra kostnad för Office 365-kunder och den kan aktiveras genom att följa stegen i vår [Kom igång med lösenordshantering](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords) guide.
   * **Användare som loggar in via** självbetjäning registrering: om organisationen du samarbetar med används den [självbetjäning anmälan](../active-directory-self-service-signup.md) funktion för att få till en klient, vi låter dem återställa lösenordet med e-postmeddelandet som de har registrerats.
   * **B2B användare**: alla nya B2B-användare som skapats med hjälp av den nya [Azure AD B2B-funktioner](../active-directory-b2b-what-is-azure-ad-b2b.md) kommer även att kunna återställa sina lösenord med e-postmeddelandet som de registrerade under inbjudan.

Om du vill testa det här scenariot, gå till http://passwordreset.microsoftonline.com med något av dessa partner-användare. Om de har en alternativ e-postadress eller autentisering e-definierade lösenordsåterställning fungerar som förväntat.

> [!NOTE]
> Microsoft-konton som har beviljats åtkomst till din Azure AD-klient, till exempel Hotmail.com, Outlook.com eller andra personliga e-postadresser är inte kunna använda Azure AD SSPR. De behöver för att återställa sina lösenord med hjälp av informationen i den [när du inte logga in till ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artikel.

## <a name="next-steps"></a>Nästa steg

Följande artiklar ger ytterligare information om lösenordsåterställning via Azure AD:

* [Hur gör jag för att slutföra en lyckad distribution av SSPR?](howto-sspr-deployment.md)
* [Återställ eller ändra ditt lösenord](../active-directory-passwords-update-your-own-password.md)
* [Registrera för återställning av lösenord för självbetjäning](../active-directory-passwords-reset-register.md)
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
[Writeback]: ./media/concept-sspr-howitworks/troubleshoot-writeback-running.png "Lokal integrering lösenord tillbakaskrivning konfiguration och information om felsökning"
