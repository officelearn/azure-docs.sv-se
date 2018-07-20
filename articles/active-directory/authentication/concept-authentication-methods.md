---
title: Azure AD-autentiseringsmetoder
description: Vilka autentiseringsmetoder är tillgängliga i Azure AD för MFA och SSPR
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry, michmcla
ms.openlocfilehash: 48f3a77d2aa81cda62f8206709268bae8e7c8737
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2018
ms.locfileid: "39164022"
---
# <a name="what-are-authentication-methods"></a>Vad är autentiseringsmetoder?

Azure AD för återställning av lösenord via självbetjäning (SSPR) och Multi-Factor Authentication (MFA) kan begära ytterligare information, kallas autentiseringsmetoder eller säkerhetsinformation för att bekräfta att du som du anta att du är när du använder de associera funktionerna.

Administratörer kan definiera i vilka autentiseringsmetoder är tillgängliga för användare av SSPR och MFA-principen. Vissa autentiseringsmetoder är kanske inte tillgänglig för alla funktioner.

Microsoft rekommenderar starkt att administratörer aktivera användare att välja fler än det minsta antalet nödvändiga autentiseringsmetoder om de inte har åtkomst till en.

|Autentiseringsmetod|Användning|
| --- | --- |
| Lösenord | MFA och SSPR |
| Säkerhetsfrågor | Endast SSPR |
| E-postadress | Endast SSPR |
| Microsoft Authenticator-appen | Endast MFA |
| SMS | MFA och SSPR |
| Röstsamtal | MFA och SSPR |
| Applösenord | MFA endast i vissa fall |

## <a name="password"></a>Lösenord

Azure AD-lösenord betraktas som en autentiseringsmetod. Det är en metod som **går inte att inaktivera**.

## <a name="security-questions"></a>Säkerhetsfrågor

Säkerhetsfrågor är tillgängliga **endast i Azure AD-självbetjäning lösenord återställa** till icke-administratörskonton.

Om du använder säkerhetsfrågor, bör du använda dem tillsammans med en annan metod. Säkerhetsfrågor kan vara mindre säkert än andra metoder, eftersom vissa kanske känner till svar på en annan användares frågor.

> [!NOTE]
> Säkerhetsfrågor lagras säkert och privat på ett användarobjekt i katalogen och kan endast besvaras av användare under registreringen. Det går inte att en administratör att läsa eller ändra en användares frågorna och svaren.
>

### <a name="predefined-questions"></a>Fördefinierade frågor

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

Alla de fördefinierade säkerhetsfrågorna är översättas och lokaliserad till den fullständiga uppsättningen Office 365-språk baserat på användarens webbläsare språkinställningar.

### <a name="custom-security-questions"></a>Anpassade säkerhetsfrågor

Anpassade säkerhetsfrågor är inte lokaliserade. Alla anpassade frågor visas på samma språk när det anges i gränssnittet administrativ användare, även om användarens webbläsare språkinställningar är olika. Om du behöver lokaliserade frågor, bör du använda fördefinierade frågor.

Den maximala längden på en anpassad säkerhetsfråga är 200 tecken.

### <a name="security-question-requirements"></a>Säkerhetskrav för fråga

* Teckengränsen minsta svar är tre tecken.
* Gränsen för högsta svar tecken är 40 tecken.
* Användare kan inte svara på samma fråga mer än en gång.
* Användare kan inte ange samma svar på fler än en fråga.
* Alla teckenuppsättningen kan användas för att definiera frågorna och svar, inklusive Unicode-tecken.
* Antalet frågor som definierats måste vara större än eller lika med antalet frågor som krävs för att registrera.

## <a name="email-address"></a>E-postadress

E-postadress finns **endast i Azure AD-självbetjäning lösenord återställa**.

Microsoft rekommenderar användningen av ett e-postkonto som inte kräver användarens Azure AD-lösenord för att få åtkomst till.

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator-appen

Microsoft Authenticator-appen innehåller en extra nivå av säkerhet för din Azure AD-arbets- eller skolkonto eller ditt Microsoft-konto.

Microsoft Authenticator-appen finns för [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) och [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071).

### <a name="notification-through-mobile-app"></a>Meddelande via mobilapp

Microsoft Authenticator-appen kan hjälpa att förhindra obehörig åtkomst till konton och stoppa olagliga transaktioner genom att skicka ett meddelande till din smartphone eller surfplatta. Användare visa meddelandet och välj kontrollera om det är tillförlitligt. I annat fall kan de välja neka.

### <a name="verification-code-from-mobile-app"></a>Verifieringskod från mobilapp

Microsoft Authenticator-appen eller andra appar från tredje part kan användas som en programvarutoken för att skapa en OAuth-Verifieringskod. När du har angett ditt användarnamn och lösenord, kan du ange koden som tillhandahålls av appen i inloggning-skärmen. Verifieringskoden ger ett andra formen av autentisering.

## <a name="mobile-phone"></a>Mobiltelefon

Det finns två alternativ för användare med mobiltelefoner.

Om användarna inte vill ha sina mobila telefonnummer som ska visas i katalogen, men fortfarande vill använda den för återställning av lösenord, bör administratörer inte fyller den i katalogen. Användare ska fylla i sina **Autentiseringstelefon** attributet den [registreringsportalen för lösenordsåterställning](https://aka.ms/ssprsetup). Administratörer kan se den här informationen i användarens profil, men det publiceras inte någon annanstans.

För att fungera korrekt telefonnummer måste vara i formatet *+ CountryCode PhoneNumber*, till exempel + 1 4255551234.

> [!NOTE]
> Det måste finnas ett blanksteg mellan landskoden och telefonnumret.
>
> Återställning av lösenord har inte stöd för anknytningsnummer. Även i formatet + 1 4255551234 X 12345 tas tillägg bort innan de görs.

### <a name="text-message"></a>Textmeddelande

Ett SMS har skickats till mobiltelefonnumret som innehåller en Verifieringskod. Ange verifieringskoden som angetts i gränssnittet logga in för att fortsätta.

### <a name="phone-call"></a>Telefonsamtal

Ett automatiserat röstsamtal upprättas till det telefonnummer som du anger. Besvara samtalet och tryck på # tangenten för att autentisera

## <a name="office-phone"></a>Arbetstelefon

Ett automatiserat röstsamtal upprättas till det telefonnummer som du anger. Besvara samtalet och trycka på #-tangenten för att autentisera.

För att fungera korrekt telefonnummer måste vara i formatet *+ CountryCode PhoneNumber*, till exempel + 1 4255551234.

Telefonattributet office hanteras av din administratör.

> [!NOTE]
> Det måste finnas ett blanksteg mellan landskoden och telefonnumret.
>
> Återställning av lösenord har inte stöd för anknytningsnummer. Även i formatet + 1 4255551234 X 12345 tas tillägg bort innan de görs.

## <a name="app-passwords"></a>Applösenord

Vissa icke-webbläsarbaserade appar inte stöder multifaktorautentisering, om en användare har aktiverats för multifaktorautentisering och försöker använda icke-webbläsarbaserade appar och är inte autentisera. Ett applösenord gör att användare ska fortsätta att autentisera

Om du upprätthålla Multi-Factor Authentication via principer för villkorlig åtkomst och inte via MFA per användare, kan du inte skapa applösenord. Program som använder principer för villkorlig åtkomst för åtkomstkontroll behöver inte applösenord.

Om din organisation är federerad för enkel inloggning med Azure AD och du kommer att använda Azure MFA kan vara medveten om följande information:

* Applösenord verifieras av Azure AD och kringgår därför federation. Federation används endast när du konfigurerar applösenord. För federerade (SSO)-användare lagras lösenord i organisations-ID. Om användaren lämnar företaget, har den info flöda till organisationens ID via DirSync. Inaktiveringen eller borttagningen kan ta upp till tre timmar att sync, vilket försenar inaktiveringen eller borttagningen av applösenord i Azure AD.
* Inställningar för lokal klientåtkomstkontroll respekteras inte av applösenord.
* Ingen lokal autentisering loggning/granskning funktionen är tillgänglig för applösenord.
* Vissa avancerade arkitekturritningar kan behöva använda en kombination av organisationens användarnamn och lösenord och lösenord när du använder tvåstegsverifiering med klienter, beroende på där de autentiseras. För klienter som autentiseras mot en lokal infrastruktur, skulle du använda ett organisationens användarnamn och lösenord. För klienter som autentiseras mot Azure AD, använder du applösenordet.
* Som standard det går inte att användare skapar applösenord. Om du vill tillåta användare att skapa applösenord, väljer den **Tillåt användare att skapa applösenord för att logga in på icke-webbläsarprogram alternativet** under inställningar för tjänsten.

## <a name="next-steps"></a>Nästa steg

[Aktivera lösenordsåterställning för din organisation via självbetjäning](quickstart-sspr.md)

[Aktivera Azure Multi-Factor Authentication för din organisation](howto-mfa-getstarted.md)