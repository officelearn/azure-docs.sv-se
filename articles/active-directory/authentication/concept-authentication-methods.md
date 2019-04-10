---
title: Autentiseringsmetoder - Azure Active Directory
description: Vilka autentiseringsmetoder är tillgängliga i Azure AD för MFA och SSPR
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0c9af1a9ad8b816809f661d368133997f55329d
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2019
ms.locfileid: "59360647"
---
# <a name="what-are-authentication-methods"></a>Vad är autentiseringsmetoder?

Som administratör kan du välja autentiseringsmetoder för Azure Multi-Factor Authentication och lösenordsåterställning via självbetjäning (SSPR) rekommenderar vi att du kräver att användare registrerar flera autentiseringsmetoder. När en autentiseringsmetod inte är tillgänglig för en användare, kan de välja att autentisera med någon annan metod.

Administratörer kan definiera i vilka autentiseringsmetoder är tillgängliga för användare av SSPR och MFA-principen. Vissa autentiseringsmetoder är kanske inte tillgänglig för alla funktioner. Mer information om hur du konfigurerar principer finns i artiklar [hur distribuera lösenordsåterställning via självbetjäning](howto-sspr-deployment.md) och [planera en molnbaserad Azure Multi-Factor Authentication](howto-mfa-getstarted.md)

Microsoft rekommenderar starkt att administratörer aktivera användare att välja fler än det minsta antalet nödvändiga autentiseringsmetoder om de inte har åtkomst till en.

|Autentiseringsmetod|Användning|
| --- | --- |
| Lösenord | MFA och SSPR |
| Säkerhetsfrågor | Endast SSPR |
| E-postadress | Endast SSPR |
| Microsoft Authenticator-appen | MFA och förhandsversion för SSPR |
| OATH-token för maskinvara | Offentlig förhandsversion för MFA och SSPR |
| SMS | MFA och SSPR |
| Röstsamtal | MFA och SSPR |
| Applösenord | MFA endast i vissa fall |

![Autentiseringsmetoder som används på skärmen inloggning](media/concept-authentication-methods/overview-login.png)

|     |
| --- |
| OATH-maskinvarutoken för MFA- och SSPR- och Mobile app-meddelanden eller kod för mobilapp som metoder för lösenord för självbetjäning i Azure AD lösenordsåterställning är allmänt tillgänglig förhandsversionsfunktioner i Azure Active Directory. Mer information om förhandsversioner finns [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="password"></a>Lösenord

Azure AD-lösenord betraktas som en autentiseringsmetod. Det är en metod som **går inte att inaktivera**.

## <a name="security-questions"></a>Säkerhetsfrågor

Säkerhetsfrågor är tillgängliga **endast i Azure AD-självbetjäning lösenord återställa** till icke-administratörskonton.

Om du använder säkerhetsfrågor, bör du använda dem tillsammans med en annan metod. Säkerhetsfrågor kan vara mindre säkert än andra metoder, eftersom vissa kanske känner till svar på en annan användares frågor.

> [!NOTE]
> Säkerhetsfrågor lagras säkert och privat på ett användarobjekt i katalogen och kan endast besvaras av användare under registreringen. Det går inte att en administratör att läsa eller ändra en användares frågorna och svaren.
>

### <a name="predefined-questions"></a>Fördefinierade frågor

* I vilken stad träffade du din första partner?
* I vilken stad träffades dina föräldrar?
* I vilken stad bor ditt närmsta syskon?
* I vilken stad föddes din far?
* I vilken stad var ditt första jobb?
* I vilken stad föddes din mor?
* I vilken stad var du på Nyårsafton 2000?
* Vad hette din favoritlärare på gymnasiet i efternamn?
* Vad är namnet på ett universitet du ansökte till men inte gick på?
* Vad heter stället där du höll din första bröllopsmottagning?
* Vad heter din far i mellannamn?
* Vad är din favoriträtt?
* Vad heter din mormor i för- och efternamn?
* Vad var din mors namn som ogift?
* Vad är ditt äldsta syskon månad och år? (t.ex. November 1985)
* Vad heter ditt äldsta syskon i mellannamn?
* Vad heter din farfar i för- och efternamn?
* Vad heter ditt yngsta syskon i mellannamn?
* I vilken skola gick du i sjätte klass?
* Vad var för- och efternamnet på din bästa barndomsvän?
* Vad var för- och efternamnet på din första partner?
* Vad hette din favoritlärare i grundskolan i efternamn?
* Vilket märke och modell hade din första bil eller motorcykel?
* Vad hette den första skolan du gick på?
* Vad hette sjukhuset där du föddes?
* Vilken gata låg ditt första barndomshem på?
* Vad hette din barndomshjälte?
* Vad hette ditt favoritgosedjur?
* Vad hette ditt första husdjur?
* Vad var ditt smeknamn som barn?
* Vad var din favoritsport i gymnasiet?
* Vad var ditt första jobb?
* Vad var de sista fyra siffrorna i telefonnumret du hade som barn?
* När du var ung, vad ville du bli när du växte upp?
* Vem är den mest berömda personen du någonsin träffat?

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

Microsoft Authenticator-appen finns för [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) och [Windows Phone](https://go.microsoft.com/fwlink/?Linkid=825071).

> [!NOTE]
> Användare kommer inte att ha möjlighet att registrera sina mobila app vid registrering av lösenordsåterställning via självbetjäning. I stället användare kan registrera sina mobila appar på [ https://aka.ms/mfasetup ](https://aka.ms/mfasetup) eller i security info registrering förhandsversionen, enligt [ https://aka.ms/setupsecurityinfo ](https://aka.ms/setupsecurityinfo).
>

### <a name="notification-through-mobile-app"></a>Meddelande via mobilapp

Microsoft Authenticator-appen kan hjälpa att förhindra obehörig åtkomst till konton och stoppa olagliga transaktioner genom att skicka ett meddelande till din smartphone eller surfplatta. Användare visa meddelandet och välj kontrollera om det är tillförlitligt. I annat fall kan de välja neka.

> [!WARNING]
> För lösenordsåterställning via självbetjäning när bara en metod krävs för återställning, verifieringskoden är det enda alternativet som är tillgängliga för användare **att säkerställa högsta säkerhetsnivå**.
>
> När två metoder krävs användare kommer att kunna återställa med hjälp av **antingen** meddelande **eller** Verifieringskod utöver eventuella övriga aktiverat metoder.
>

Om du aktiverar användningen av både meddelande via mobilapp och Verifieringskod från mobilappen, användare som registrerar Microsoft Authenticator-appen med hjälp av ett meddelande kan använda både meddelande och kod för att verifiera sin identitet.

> [!NOTE]
> Om din organisation har personal arbeta i eller reser till Kina, den **meddelande via mobilapp** metoden på **Android-enheter** fungerar inte i det landet. Alternativa metoder ska göras tillgängliga för dessa användare.

### <a name="verification-code-from-mobile-app"></a>Verifieringskod från mobilapp

Microsoft Authenticator-appen eller andra appar från tredje part kan användas som en programvarutoken för att generera en OATH-Verifieringskod. När du har angett ditt användarnamn och lösenord, kan du ange koden som tillhandahålls av appen i inloggning-skärmen. Verifieringskoden ger ett andra formen av autentisering.

> [!WARNING]
> För lösenordsåterställning via självbetjäning när bara en metod krävs för återställning av verifieringskoden är det enda alternativet som är tillgängliga för användare **att säkerställa högsta säkerhetsnivå**.
>

Användare kan ha en kombination av upp till fem OATH-maskinvarutoken eller authenticator-program, till exempel Microsoft Authenticator-appen som konfigurerats för användning när som helst.

## <a name="oath-hardware-tokens-public-preview"></a>OATH-maskinvarutoken (offentlig förhandsversion)

OATH är en öppen standard som anger hur enstaka lösenord (OTP) koder genereras. Azure AD stöder användning av OATH-TOTP SHA-1-token på 30 sekunder eller 60-sekunders olika. Kunder kan skaffa dessa token från leverantören av valfri. Hemliga nycklar är begränsad till 128 tecken, vilket inte kanske är kompatibel med alla token.

![Ladda upp OATH-token till bladet MFA Server OATH-token](media/concept-authentication-methods/oath-tokens-azure-ad.png)

OATH-token för maskinvara som stöds som en del av en offentlig förhandsversion. Mer information om förhandsversioner finns [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

När token är upptagna måste de laddas upp i en fil med kommaavgränsade värden (CSV)-filformat, inklusive UPN, serienummer, hemlig nyckel, tidsintervall, tillverkare och modell som exemplet nedan visar.

```csv
upn,serial number,secret key,timeinterval,manufacturer,model
Helga@contoso.com,1234567,1234567890abcdef1234567890abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Kontrollera att du inkluderar rubrikraden i CSV-filen enligt ovan.

En gång korrekt formaterade som en CSV-fil, en administratör kan logga in på Azure-portalen och gå till **Azure Active Directory**, **MFA Server**, **OATH-token**, och ladda upp den resulterande CSV-filen.

Beroende på storleken på CSV-filen kan dröja det några minuter att bearbeta. Klicka på den **uppdatera** för att visa aktuell status. Om det finns några fel i filen, kommer du har möjlighet att hämta en CSV-fil som innehåller alla fel för dig att lösa.

När du har åtgärdat eventuella fel administratören kan aktivera varje nyckel genom att klicka på **aktivera** för token som ska aktiveras och anger OTP visas i token.

Användare kan ha en kombination av upp till fem OATH-maskinvarutoken eller authenticator-program, till exempel Microsoft Authenticator-appen som konfigurerats för användning när som helst.

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

> [!IMPORTANT]
> Från och med mars 2019 telefonsamtal alternativen kommer inte att MFA och SSPR-användare i kostnadsfria/utvärderingsversion Azure AD-klienter. SMS-meddelanden som inte påverkas av den här ändringen. Telefonsamtal fortsätter att vara tillgängliga för användare i betalda Azure AD-klienter. Den här ändringen påverkar endast kostnadsfria/utvärderingsversion Azure AD-klienter.

## <a name="office-phone"></a>Arbetstelefon

Ett automatiserat röstsamtal upprättas till det telefonnummer som du anger. Besvara samtalet och trycka på #-tangenten för att autentisera.

För att fungera korrekt telefonnummer måste vara i formatet *+ CountryCode PhoneNumber*, till exempel + 1 4255551234.

Telefonattributet office hanteras av din administratör.

> [!IMPORTANT]
> Från och med mars 2019 telefonsamtal alternativen kommer inte att MFA och SSPR-användare i kostnadsfria/utvärderingsversion Azure AD-klienter. SMS-meddelanden som inte påverkas av den här ändringen. Telefonsamtal fortsätter att vara tillgängliga för användare i betalda Azure AD-klienter. Den här ändringen påverkar endast kostnadsfria/utvärderingsversion Azure AD-klienter.

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

[Aktivera kombinerade registrering i din klient](howto-registration-mfa-sspr-combined.md)

[Autentisering metoden configuration användardokumentation](https://aka.ms/securityinfoguide)
