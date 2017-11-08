---
title: "Så här fungerar det? Azure AD SSPR | Microsoft Docs"
description: "Azure AD Självbetjäning för lösenordsåterställning ingående"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 618c5908-5bf6-4f0d-bf88-5168dfb28a88
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: fd9515120049dd3837a43c95de8a9b6822719e19
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="self-service-password-reset-in-azure-ad-deep-dive"></a>Självbetjäning för återställning av lösenord i Azure AD ingående

Hur fungerar SSPR? Vad innebär det alternativet i gränssnittet? Läs mer om Azure AD-lösenordet för självbetjäning återställa.

## <a name="how-does-the-password-reset-portal-work"></a>Hur återställning portal arbete av lösenord

När en användare navigerar till portalen för återställning av lösenord, ett arbetsflöde har inletts för att fastställa:

   * Hur sidan vara lokaliserade?
   * Är användarkonton som är giltig?
   * Vilken organisation användaren tillhör?
   * Där användarens lösenord hanteras?
   * Användaren är licensierad för att använda funktionen?


Läs igenom följande steg för att lära dig om logiken bakom lösenordet återställa sidan.

1. Användaren klickar på den kan inte komma åt ditt kontolänk eller går direkt till [https://aka.ms/sspr](https://passwordreset.microsoftonline.com).
2. Baserat på webbläsarens språk upplevelsen återges i det aktuella språket. Lösenord Återställ upplevelse är lokaliserade till samma språk som har stöd för Office 365.
3. Användaren anger ett användar-ID och skickar en captcha.
4. Azure AD verifierar om användaren kan använda den här funktionen genom att göra följande:
   * Kontrollerar att användaren har den här funktionen är aktiverad och en Azure AD-licens.
     * Om användaren inte har den här funktionen är aktiverad eller tilldelats en licens, uppmanas användaren att kontakta administratören om du vill återställa sina lösenord.
   * Kontrollerar att användaren har rättigheten kräver data som definierats på sitt konto i enlighet med systemadministratören.
     * Om principen kräver bara en utmaning, är det säkerställas att användaren har lämpliga data har definierats för minst en av de utmaningar som aktiveras av administratörsprincipen.
       * Om användaren inte har konfigurerats bör användaren att kontakta administratören om du vill återställa sina lösenord.
     * Om principen kräver två utmaningar, är det säkerställas att användaren har definierat för åtminstone två av de utmaningar som aktiveras av administratörsprincipen lämpliga data.
       * Om användaren inte har konfigurerats, så vi användaren bör kontakta administratören om du vill återställa sina lösenord.
   * Kontrollerar om användarens lösenord hanteras lokalt (federerad eller synkroniseras lösenords-hash).
     * Om tillbakaskrivning distribueras och hanteras lokalt användarens lösenord tillåts användaren att fortsätta att autentisera och återställa sina lösenord.
     * Om tillbakaskrivning inte har distribuerats och användarens lösenord hanteras lokalt, uppmanas användaren att kontakta administratören om du vill återställa sina lösenord.
5. Om det fastställs att användaren kan återställa sina lösenord har leds användaren genom återställning av processen.

## <a name="authentication-methods"></a>Autentiseringsmetoder

Om Self-Service lösenord återställa (SSPR) är aktiverat, måste du välja minst en av följande alternativ för autentiseringsmetoder. Ibland kanske du hör alternativen kallas portar. Vi rekommenderar starkt att välja minst två autentiseringsmetoder så att användarna har du mer flexibilitet.

* E-post
* Mobiltelefon
* Arbetstelefon
* Säkerhetsfrågor

![Autentisering][Authentication]

### <a name="what-fields-are-used-in-the-directory-for-authentication-data"></a>Vilka fält som ska användas i katalogen för autentiseringsdata

* Arbetstelefon motsvarar Arbetstelefon
    * Användare kan inte ange det här fältet själva definieras av administratören
* Mobiltelefon motsvarar telefon för autentisering (inte synligt offentligt) eller mobiltelefon (synligt offentligt)
    * Tjänsten söker efter telefon för autentisering först, sedan faller tillbaka till din mobiltelefon om det inte finns
* Den alternativa e-postadressen motsvarar autentisering e-post som (inte synligt offentligt) eller alternativa e-post
    * Tjänsten ser ut för e-post för autentisering först och sedan flyttas tillbaka till alternativa e-post

Som standard synkroniseras endast molnet attributen arbetstelefon och mobila telefonnummer till din molnkatalog från din lokala katalog för autentiseringsdata.

Användare kan bara återställa sina lösenord, om de har data som finns i de autentiseringsmetoder som administratören har aktiverat och kräver.

Om användarna inte vill att deras mobiltelefonnummer som ska visas i katalogen, men ändå vill använda för återställning av lösenord, Administratörer bör inte fylla det i katalogen och användaren bör fyller sina **telefonförautentisering** attributet den [registreringsportalen för lösenordsåterställning](http://aka.ms/ssprsetup). Administratörer kan se den här informationen i användarens profil men publiceras inte någon annanstans.

### <a name="number-of-authentication-methods-required"></a>Antal autentiseringsmetoder krävs

Det här alternativet anger det minsta antalet tillgängliga autentiseringsmetoderna eller gates en användare måste genomgå för att återställa eller låsa upp sitt lösenord och kan ställas in på 1 eller 2.

Användare kan välja att ange flera autentiseringsmetoder om de har aktiverats av administratören.

Om en användare inte har minsta nödvändiga metoder som registrerats, visas en felsida som hänvisar dem att begära en administratör att återställa sina lösenord.

#### <a name="changing-authentication-methods"></a>Ändra autentiseringsmetoder

Om du börjar med en princip som har endast en autentiseringsmetod som krävs för återställning eller låsa upp registrerade och du ändrar att med två händer?

| Antal metoder som har registrerats | Antal metoder som krävs | Resultat |
| :---: | :---: | :---: |
| 1 eller fler | 1 | **Kan** att återställa eller låsa upp |
| 1 | 2 | **Det går inte** att återställa eller låsa upp |
| 2 eller högre | 2 | **Kan** att återställa eller låsa upp |

Om du ändrar kan typer av autentiseringsmetoder som en användare kan använda du oavsiktligt hindra användare från att kunna använda SSPR om de inte har den minsta mängden data som är tillgängliga.

Exempel: 
1. Ursprungliga principen har konfigurerats med 2 autentiseringsmetoder som krävs med hjälp av endast office telefon- och frågor. 
2. Administratören ändrar princip för att inte längre använda säkerhetsfrågor men Tillåt användning av mobiltelefon och alternativa e-post.
3. Användare utan mobiltelefon och alternativa e-fält kan inte återställa sina lösenord.

### <a name="how-secure-are-my-security-questions"></a>Hur säker är min säkerhetsfrågor

Om du använder säkerhetsfrågor, rekommenderar vi dem används med en annan metod som de kan vara mindre säkert än andra metoder eftersom vissa personer vet svaren på en annan användares frågor.

> [!NOTE] 
> Säkerhetsfrågor lagras säkert och privat på ett användarobjekt i katalogen och kan endast besvaras av användare under registreringen. Det går inte att en administratör att läsa eller ändra en användares frågor och svar.
>

### <a name="security-question-localization"></a>Översättning av säkerhet fråga

Alla fördefinierade frågor som följer är lokaliserade till den fullständiga uppsättningen av Office 365 språk baserat på användarens webbläsare språkinställningar.

* I vilken stad träffade du din första make/maka/partner?
* I vilken stad träffades dina föräldrar?
* I vilken stad bor ditt närmsta syskon?
* I vilken stad föddes din pappa?
* I vilken stad hade du ditt första jobb?
* I vilken stad föddes din mamma?
* Vilken stad befann du dig i på nyårsafton 2000?
* Vad är senaste namnet på din favoritlärare i hög * skola?
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

Anpassade säkerhetsfrågor är inte lokaliserade för olika språk. Alla anpassade frågor visas på samma språk som de anges i det administrativa gränssnittet även om användarens webbläsare språkinställningar är olika. Använd fördefinierade frågor om du behöver lokaliserade frågor.

Den maximala längden på en anpassad säkerhetsfråga är 200 tecken.

### <a name="security-question-requirements"></a>Fråga säkerhetskrav

* Minsta svaret tecken är 3 tecken
* Maximal svaret tecken är 40 tecken
* Användare kan inte svara på samma fråga mer än en gång
* Användare kan inte ange samma svaret på mer än en fråga
* Alla teckenuppsättningen kan användas för att definiera frågor och svar, inklusive Unicode-tecken
* Antalet frågor som definierats måste vara större än eller lika med antalet frågor som krävs för att registrera

## <a name="registration"></a>Registrering

### <a name="require-users-to-register-when-signing-in"></a>Kräv att användare registrerar sig vid inloggning

Aktivera det här alternativet måste en användare som har aktiverats för lösenordsåterställning för att slutföra lösenordet återställs registrering om de logga in program med Azure AD för att logga in som de som följer:

* Office 365
* Azure Portal
* Åtkomstpanel
* Federerade program
* Anpassade program med Azure AD

När alternativet är inaktiverat användare manuellt registrera kontaktuppgifter genom att besöka [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup) eller genom att klicka på den **registrera dig för lösenordsåterställning** länken under fliken profil i den åtkomstpanelen.

> [!NOTE]
> Användare kan stänga registreringsportalen för lösenordsåterställning genom att klicka på Avbryt eller stänger fönstret men uppmanas varje gång de loggar in förrän de har slutfört registreringen.
>
> Detta påverkar användarens anslutning om de är alreay loggat in.

### <a name="number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Antal dagar innan användare uppmanas att bekräfta sin autentiseringsinformation

Det här alternativet anger tidsperioden mellan inställningen och reconfirming autentiseringsinformationen och är endast tillgängligt om du aktiverar den **användarna att registrera när du loggar in** alternativet.

Giltiga värden är 0-730 dagar med 0 vilket innebär att aldrig be användare att bekräfta sin autentiseringsinformation

## <a name="notifications"></a>Meddelanden

### <a name="notify-users-on-password-resets"></a>Meddela användare om lösenordsåterställning

Om det här alternativet är inställt på Ja, får den användare som återställa sina lösenord ett e-postmeddelande till dem om att lösenordet har ändrats via SSPR-portalen till sina primära och alternativa e-postadresser på filen i Azure AD. Ingen annan får ett meddelande om återställning av den här händelsen.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Meddela alla administratörer när andra administratörer återställa sina lösenord

Om det här alternativet är inställt på Ja, sedan **alla administratörer** får ett e-postmeddelande till sina primära e-postadress på filen i Azure AD meddela dem om att en annan administratör har ändrat sitt lösenord med hjälp av SSPR.

Exempel: Det finns fyra administratörer i en miljö. Administratören ”A” återställa sina lösenord med hjälp av SSPR. Administratörer B och C D får ett e-postmeddelande Varna dem om det fortfarande händer.

## <a name="on-premises-integration"></a>Lokal integrering

Om du har installerat, konfigurerats och aktiverats Azure AD Connect, har du följande ytterligare alternativ för lokal integreringar. Om dessa alternativ är avmarkerad ut och sedan tillbakaskrivning inte har konfigurerats korrekt [konfigurera tillbakaskrivning av lösenord](active-directory-passwords-writeback.md#configuring-password-writeback) för mer information.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Skriv tillbaka lösenord till din lokala katalog

Styr huruvida tillbakaskrivning av lösenord är aktiverat för den här katalogen och om tillbakaskrivning finns på, visar status för tjänsten lokalt tillbakaskrivning. Detta är användbart om du tillfälligt vill inaktivera tillbakaskrivning av lösenord utan att konfigurera om Azure AD Connect.

* Om växeln anges till Ja, sedan tillbakaskrivning är aktiverad och federerad och lösenord hash-synkroniserade användarna ska kunna återställa sina lösenord.
* Om växeln anges Nej sedan tillbakaskrivning är inaktiverat och federerad och lösenord hash-synkroniserade användare är inte kan återställa sina lösenord.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Tillåt användare att låsa upp konton utan att återställa sina lösenord

Anger huruvida användare som besöker portalen för återställning av lösenord bör ges möjlighet att låsa upp sina lokala Active Directory-konton utan att återställa sina lösenord. Standard Azure AD låser upp konton när du utför en återställning av lösenord, den här inställningen kan du dela upp de två åtgärderna. 

* Om värdet ”yes”, sedan användarna möjlighet att återställa sina lösenord och låsa upp kontot, eller att låsa upp utan att återställa lösenordet.
* Om inställd på ”Nej” och sedan är endast möjligt att användare att utföra en kombinerad lösenord återställs och kontoupplåsning igen.

## <a name="how-does-password-reset-work-for-b2b-users"></a>Hur lösenordsåterställning för B2B-användare?
Återställning av lösenord och ändra stöds helt med alla B2B-konfigurationer. Följande tre fall stöds för lösenordsåterställning för B2B-användare.

1. **Användare från en partnerorganisationen med en befintlig Azure AD-klient** - om du samarbetar med organisationen har en befintlig Azure AD-klient vi **respektera oavsett principer för återställning av lösenord är aktiverade i den klienten**. För lösenord för att fungera, partner organisation bara behöver kontrollera att Azure AD SSPR är aktiverat som är utan extra kostnad för O365 kunder, och kan aktiveras genom att följa stegen i vår [komma igång med lösenordshantering](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords)guide.
2. **Användare som har registrerat med [självbetjäning registrering](active-directory-self-service-signup.md)**  – om organisationen du samarbetar med med används den [självbetjäning anmälan](active-directory-self-service-signup.md) funktion för att få till en klient, vi låter dem återställa med den e-post när de har registrerats.
3. **B2B användare** -B2B användare skapas med den nya [Azure AD B2B-funktioner](active-directory-b2b-what-is-azure-ad-b2b.md) kommer även att kunna återställa sina lösenord med e-postmeddelandet som de registrerade under inbjudan.

Om du vill testa det här scenariot, går du till http://passwordreset.microsoftonline.com med något av dessa partner-användare. Så länge som de har en alternativ e-postadress eller autentisering e-definitionen för lösenordsåterställning fungerar som förväntat.

> [!NOTE]
> Microsoft-konton som har beviljats åtkomst till din Azure AD-klient, till exempel nätverksresurser från Outlook.com, Hotmail.com eller andra personliga e-postadresser kan inte använda Azure AD SSPR och behöver återställa sina lösenord med hjälp av informationen i den artikel [när du inte logga in till ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).

## <a name="next-steps"></a>Nästa steg

Följande länkar ger ytterligare information om lösenordsåterställning med Azure AD

* [Hur gör jag för att slutföra en lyckad distribution av SSPR?](active-directory-passwords-best-practices.md)
* [Återställ eller ändra ditt lösenord](active-directory-passwords-update-your-own-password.md).
* [Registrera för återställning av lösenord för självbetjäning](active-directory-passwords-reset-register.md).
* [Har du en fråga om licensiering?](active-directory-passwords-licensing.md)
* [Vilka data används av SSPR och vilka data bör du fylla i för dina användare?](active-directory-passwords-data.md)
* [Vilka autentiseringsmetoder är tillgängliga för användarna?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Vilka principalternativ finns för SSPR?](active-directory-passwords-policy.md)
* [Vad är tillbakaskrivning av lösenord och vad är intresserat med det?](active-directory-passwords-writeback.md)
* [Hur gör jag för att rapportera på aktivitet i SSPR?](active-directory-passwords-reporting.md)
* [Vad är alla alternativ i SSPR och vad betyder de?](active-directory-passwords-how-it-works.md)
* [Jag tror att något har gått sönder. Hur gör jag för att felsöka SSPR?](active-directory-passwords-troubleshoot.md)
* [Jag har en fråga som inte besvarades någon annanstans](active-directory-passwords-faq.md)

[Authentication]: ./media/active-directory-passwords-how-it-works/sspr-authentication-methods.png "Azure AD-autentiseringsmetoder som är tillgängliga och kvantitet som krävs"