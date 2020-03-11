---
title: Autentiseringsmetoder – Azure Active Directory
description: Autentiseringsmetoder som är tillgängliga i Azure AD för MFA och SSPR
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a82c69575e82a7cf397955f08c3f114e449ba6b
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2020
ms.locfileid: "78968779"
---
# <a name="what-are-authentication-methods"></a>Vad är autentiseringsmetoder?

Som administratör väljer du autentiseringsmetoder för Azure Multi-Factor Authentication och lösen ords återställning via självbetjäning (SSPR) Vi rekommenderar att du kräver att användare registrerar flera autentiseringsmetoder. När en autentiseringsmetod inte är tillgänglig för en användare kan de välja att autentisera med en annan metod.

Administratörer kan definiera i princip vilka autentiseringsmetoder som är tillgängliga för användare av SSPR och MFA. Vissa autentiseringsmetoder kanske inte är tillgängliga för alla funktioner. Mer information om hur du konfigurerar principer finns i artikeln [så här distribuerar du självbetjäning för återställning av lösen ord](howto-sspr-deployment.md) och [planerar en molnbaserad Azure-Multi-Factor Authentication](howto-mfa-getstarted.md)

Microsoft rekommenderar starkt att administratörer låter användare välja fler än det minsta antal autentiseringsmetoder som krävs, om de inte har åtkomst till en.

|Autentiseringsmetod|Användning|
| --- | --- |
| lösenord | MFA och SSPR |
| Säkerhetsfrågor | Endast SSPR |
| E-postadress | Endast SSPR |
| Microsoft Authenticator-appen | MFA och SSPR |
| OATH-token för maskin vara | Offentlig för hands version för MFA och SSPR |
| SMS | MFA och SSPR |
| Röst samtal | MFA och SSPR |
| Applösenord | MFA i vissa fall |

![Autentiseringsmetoder som används på inloggnings skärmen](media/concept-authentication-methods/overview-login.png)

|     |
| --- |
| OATH-maskinvaru-token för MFA och SSPR är offentliga för hands versions funktioner i Azure Active Directory. Mer information om för hands versionerna finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) versionerna|
|     |

## <a name="password"></a>lösenord

Ditt Azure AD-lösenord betraktas som en autentiseringsmetod. Det är en metod som **inte kan inaktive ras**.

## <a name="security-questions"></a>Säkerhetsfrågor

Säkerhets frågor är **bara tillgängliga i Azure AD** självbetjänings återställning av lösen ord till konton som inte är administratörer.

Om du använder säkerhets frågor rekommenderar vi att du använder dem tillsammans med en annan metod. Säkerhets frågor kan vara mindre säkra än andra metoder eftersom vissa personer kanske känner till svaren på en annan användares frågor.

> [!NOTE]
> Säkerhets frågor lagras privat och säkert på ett användar objekt i katalogen och kan bara besvaras av användare under registreringen. Det finns inget sätt för en administratör att läsa eller ändra en användares frågor eller svar.
>

### <a name="predefined-questions"></a>Fördefinierade frågor

* I vilken stad uppfyller du din första make/maka/partner?
* I vilken stad uppfyllde dina föräldrar?
* I vilken stad har du närmaste aktiva syskon?
* I vilken stad föddes din far?
* I vilken stad var ditt första jobb?
* I vilken stad föddes din mamma?
* Vilken stad var du på årets nya år 2000?
* Vad är det sista namnet på din favorit lärare i gymnasie skolan?
* Vad är namnet på en skola som du har använt till men inte var?
* Vad är namnet på den plats där du höll din första bröllops mottagning?
* Vad är ditt fars mellan namn?
* Vad är ditt favorit mat?
* Vad är din mormor för för-och efter namn?
* Vad är din mammas mellan namn?
* Vad är ditt äldsta syskons födelsedags månad och år? (t. ex. november 1985)
* Vad är ditt äldsta syskons mellan namn?
* Vad är din farfar-farfar för för-och efter namn?
* Vad är ditt unga syskons mellan namn?
* Vilken skola deltog du i sjätte klass?
* Vad hette din barns bästa vänner?
* Vad hette det första och sista namnet på din första större?
* Vilket är det sista namnet på din favorit lärare i din favorit klass?
* Vad är märke och modell för din första bil eller motorcykel?
* Vad hette den första skolan som du deltog i?
* Vad var namnet på det sjukhus där du föddes?
* Vad är namnet på gatan för ditt första barn hemma?
* Vad hette din barns hjälte?
* Vad är namnet på ditt favorit fyllda djur?
* Vad hette ditt första hus djur?
* Vad hade ditt barn smek namn?
* Vad var din favorit sport i gymnasie skola?
* Vad var ditt första jobb?
* Vilka var de sista fyra siffrorna i ditt barns telefonnummer?
* Vad gjorde du när du var ung, vad gjorde du när du blev vuxna?
* Vilken är den mest berömda person som du någonsin har uppfyllt?

Alla fördefinierade säkerhets frågor översätts och lokaliseras till en fullständig uppsättning Office 365-språk baserat på användarens språk i webbläsaren.

### <a name="custom-security-questions"></a>Anpassade säkerhets frågor

Anpassade säkerhets frågor är inte lokaliserade. Alla anpassade frågor visas på samma språk som de anges i det administrativa användar gränssnittet, även om användarens webbläsare är annorlunda. Om du behöver lokaliserade frågor bör du använda de fördefinierade frågorna.

Den maximala längden för en anpassad säkerhets fråga är 200 tecken.

### <a name="security-question-requirements"></a>Krav för säkerhets fråga

* Gränsen för minsta antal svars tecken är tre tecken.
* Gränsen för maximalt antal svars tecken är 40 tecken.
* Användare kan inte besvara samma fråga mer än en gången.
* Användare kan inte ange samma svar på fler än en fråga.
* Alla teckenuppsättningar kan användas för att definiera frågor och svar, inklusive Unicode-tecken.
* Antalet frågor som definieras måste vara större än eller lika med antalet frågor som krävs för registrering.

## <a name="email-address"></a>E-postadress

E-postadress är **bara tillgänglig i Azure AD-återställning av lösen ord för självbetjäning**.

Microsoft rekommenderar att du använder ett e-postkonto som inte kräver att användarens Azure AD-lösenord får åtkomst.

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator-appen

Microsoft Authenticator-appen ger dig ytterligare en säkerhets nivå för ditt Azure AD-arbets-eller skol konto eller din Microsoft-konto.

Microsoft Authenticator-appen finns för [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) och [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6).

> [!NOTE]
> Användarna kan inte registrera sin mobilapp när de registrerar sig för lösen ords återställning via självbetjäning. I stället kan användarna registrera sin mobilapp på [https://aka.ms/mfasetup](https://aka.ms/mfasetup) eller i förhands granskningen av säkerhets information på [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo).
>

### <a name="notification-through-mobile-app"></a>Meddelande via mobilapp

Microsoft Authenticator-appen kan hjälpa till att förhindra obehörig åtkomst till konton och stoppa bedrägliga transaktioner genom att skicka ett meddelande till din smartphone eller surfplatta. Användare visar meddelandet, och om det är legitimt väljer du verifiera. Annars kan de välja neka.

> [!WARNING]
> För lösen ords återställning via självbetjäning när endast en metod krävs för återställning är verifierings koden det enda alternativ som är tillgängligt för användarna **för att säkerställa den högsta säkerhets nivån**.
>
> När två metoder krävs kommer användare att kunna återställa med hjälp av **antingen** meddelande- **eller** verifierings kod förutom andra aktiverade metoder.
>

Om du aktiverar användningen av både meddelanden via mobilappen och verifierings koden från mobilappen kan användare som registrerar Microsoft Authenticator-appen med hjälp av ett meddelande använda både meddelande och kod för att verifiera sin identitet.

> [!NOTE]
> Om din organisation har personal som arbetar i eller reser i Kina fungerar inte **meddelandet via mobilappen** på **Android-enheter** i det landet. Alternativa metoder bör göras tillgängliga för dessa användare.

### <a name="verification-code-from-mobile-app"></a>Verifierings kod från mobilapp

Microsoft Authenticator app eller andra appar från tredje part kan användas som en programvarubaserad token för att generera en OATH-verifierings kod. När du har angett ditt användar namn och lösen ord anger du koden från appen till inloggnings skärmen. Verifierings koden innehåller en andra form av autentisering.

> [!WARNING]
> För lösen ords återställning via självbetjäning när endast en metod krävs för att återställa verifierings koden är det enda alternativet som användarna kan använda för att **säkerställa den högsta säkerhets nivån**.
>

Användare kan ha en kombination av upp till fem OATH-token eller Authenticator-program, till exempel Microsoft Authenticator-appen som kon figurer ATS för användning när som helst.

## <a name="oath-hardware-tokens-public-preview"></a>OATH-token för maskin vara (offentlig för hands version)

OATH är en öppen standard som anger hur eng ång slö sen ord (eng ång slö sen ord) genereras. Azure AD har stöd för användning av OATH-TOTP mobilapp SHA-1-token i den 30-sekunders-eller 60-andra sorten. Kunder kan köpa dessa token från den valda leverantören. Hemliga nycklar är begränsade till 128 tecken, som kanske inte är kompatibla med alla tokens. Den hemliga nyckeln får bara innehålla tecknen *a-z* eller *a-z* och siffror *1-7*och måste kodas i Base32.

![Laddar upp OATH-token till bladet MFA OATH-token](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

OATH-token för OATH-enheter stöds som en del av en offentlig för hands version. Mer information om för hands versionerna finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) versionerna

När token har erhållits måste de överföras i ett fil format med kommaavgränsade värden (CSV), inklusive UPN, serie nummer, hemlig nyckel, tidsintervall, tillverkare och modell som visas i följande exempel:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Se till att du inkluderar rubrik raden i CSV-filen.

När den är korrekt formaterad som en CSV-fil kan en administratör logga in på Azure Portal, navigera till **Azure Active Directory** > **säkerhet** > **MFA** > **Oath-token**och ladda upp den resulterande CSV-filen.

Beroende på storleken på CSV-filen kan det ta några minuter att bearbeta. Klicka på knappen **Uppdatera** för att hämta aktuell status. Om det finns några fel i filen, har du möjlighet att ladda ned en CSV-fil som visar eventuella fel som du kan lösa. Fält namnen i den hämtade CSV-filen skiljer sig från den överförda versionen.

När eventuella fel har åtgärd ATS kan administratören aktivera varje nyckel genom att klicka på **Aktivera** för att aktivera token och ange det eng ång slö sen ord som visas i token.

Användare kan ha en kombination av upp till fem OATH-token eller Authenticator-program, till exempel Microsoft Authenticator-appen som kon figurer ATS för användning när som helst.

## <a name="phone-options"></a>Telefon alternativ

### <a name="mobile-phone"></a>Mobiltelefon

Det finns två alternativ för användare med mobil telefoner.

Om användarna inte vill att deras mobiltelefon nummer ska synas i katalogen, men ändå vill använda det för lösen ords återställning, ska administratörer inte fylla i katalogen. Användarna ska fylla i sina attribut för **autentisering** via [registrerings portalen för lösen ords återställning](https://aka.ms/ssprsetup). Administratörer kan se den här informationen i användarens profil, men den publiceras inte någon annan stans.

För att fungera korrekt måste telefonnumret vara i formatet *+ CountryCode telefonnummer*, till exempel + 1 4255551234.

> [!NOTE]
> Det måste finnas ett blank steg mellan lands koden och telefonnumret.
>
> Lösen ords återställning stöder inte telefon tillägg. Även i formatet + 1 4255551234X12345 tas tilläggen bort innan anropet placeras.

Microsoft garanterar inte konsekvent SMS eller Voice-baserad Multi-Factor Authentication uppleverans av samma nummer. Med hänsyn till våra användare kan Microsoft lägga till eller ta bort korta koder när vi gör väg justeringar för att förbättra SMS-leveranser. Microsoft stöder inte korta koder för länder/regioner utöver USA och Kanada.

#### <a name="text-message"></a>Textmeddelande

Ett SMS skickas till det mobiltelefon nummer som innehåller en verifierings kod. Fortsätt genom att ange verifierings koden som anges i inloggnings gränssnittet.

#### <a name="phone-call"></a>Telefonsamtal

Ett automatiskt röst samtal görs till det telefonnummer som du anger. Besvara anropet och tryck på # i telefon tangent bordet för att autentisera

> [!IMPORTANT]
> Från och med mars 2019 kommer Telefonsamtals alternativen inte att vara tillgängliga för MFA-och SSPR-användare i kostnads fria/utvärderings versioner av Azure AD-klienter. SMS-meddelanden påverkas inte av den här ändringen. Telefonsamtalet fortsätter att vara tillgängligt för användare i betalda Azure AD-klienter. Den här ändringen påverkar endast kostnads fria/utvärderings versioner av Azure AD.

### <a name="office-phone"></a>Arbetstelefon

Ett automatiskt röst samtal görs till det telefonnummer som du anger. Besvara anropet och tryck på # i telefon tangent bordet för att autentisera.

För att fungera korrekt måste telefonnumret vara i formatet *+ CountryCode telefonnummer*, till exempel + 1 4255551234.

Office Phone-attributet hanteras av administratören.

> [!IMPORTANT]
> Från och med mars 2019 kommer Telefonsamtals alternativen inte att vara tillgängliga för MFA-och SSPR-användare i kostnads fria/utvärderings versioner av Azure AD-klienter. SMS-meddelanden påverkas inte av den här ändringen. Telefonsamtalet fortsätter att vara tillgängligt för användare i betalda Azure AD-klienter. Den här ändringen påverkar endast kostnads fria/utvärderings versioner av Azure AD.

> [!NOTE]
> Det måste finnas ett blank steg mellan lands koden och telefonnumret.
>
> Lösen ords återställning stöder inte telefon tillägg. Även i formatet + 1 4255551234X12345 tas tilläggen bort innan anropet placeras.

### <a name="troubleshooting-phone-options"></a>Felsöka telefon alternativ

Vanliga problem som rör autentiseringsmetoder med hjälp av ett telefonnummer:

* Blockerat anropar-ID på en enskild enhet
   * Felsök enhet
* Fel telefonnummer, felaktig landskod, hem telefonnummer jämfört med telefonnummer till arbetet
   * Felsöka användar objekt och konfigurerade autentiseringsmetoder. Se till att rätt telefonnummer är registrerade.
* Felaktig PIN-kod angavs
   * Bekräfta att användaren har använt rätt PIN-kod registrerad i Azure MFA Server.
* Samtalet har vidarebefordrats till röst meddelande
   * Se till att användaren har aktiverat telefonen och att tjänsten är tillgänglig i sitt ställe eller Använd en annan metod.
* Användaren har blockerats
   * Be administratören att avblockera användaren i Azure Portal.
* SMS prenumererar inte på enheten
   * Låt användaren ändra metoder eller aktivera SMS på enheten.
* Defekta telekom-leverantörer (inga telefonin uppgifter identifierade, saknade problem med DTMF-toner, blockerat anrops-ID på flera enheter eller blockerat SMS över flera enheter)
   * Microsoft använder flera telekom-leverantörer för att skicka telefonsamtal och SMS-meddelanden för autentisering. Om du ser något av ovanstående problem kan en användare försöka använda metoden minst 5 gånger inom 5 minuter och ha den användarens information tillgänglig när de kontaktar Microsoft support.

## <a name="app-passwords"></a>Applösenord

Vissa icke-webbläsarbaserade appar stöder inte Multi-Factor Authentication, om en användare har Aktiver ATS för Multi-Factor Authentication och försöker använda icke-webbläsarbaserade appar, kan de inte autentisera sig. Med ett applösenord kan användarna fortsätta att autentisera

Om du tillämpar Multi-Factor Authentication via principer för villkorlig åtkomst och inte via användarspecifika MFA-användare kan du inte skapa applösenord. Program som använder principer för villkorlig åtkomst för att kontrol lera åtkomsten behöver inte applösenord.

Om din organisation är federerad för enkel inloggning med Azure AD och du kommer att använda Azure MFA bör du vara medveten om följande information:

* App-lösenordet verifieras av Azure AD och kringgår därför federationen. Federation används endast när du konfigurerar applösenord. För federerade (SSO) användare lagras lösen ord i organisations-ID: t. Om användaren lämnar företaget måste denna information flöda till organisations-ID med hjälp av DirSync. Det kan ta upp till tre timmar innan kontot har Aktiver ATS/tas bort, vilket fördröjer inaktive ring/borttagning av applösenord i Azure AD.
* Inställningar för lokal klientåtkomstkontroll respekteras inte av applösenord.
* Ingen lokal autentiserings-och gransknings funktion är tillgänglig för applösenord.
* Vissa avancerade arkitektur design kan kräva att du använder en kombination av organisationens användar namn och lösen ord och applösenord när du använder tvåstegsverifiering med-klienter, beroende på var de autentiserar sig. För klienter som autentiseras mot en lokal infrastruktur använder du ett organisations användar namn och lösen ord. För klienter som autentiseras mot Azure AD använder du appens lösen ord.
* Som standard kan användarna inte skapa applösenord. Om du behöver tillåta användare att skapa applösenord väljer du **alternativet Tillåt användare att skapa applösenord för att logga in på icke-webbläsarbaserade program** under tjänst inställningar.

## <a name="next-steps"></a>Nästa steg

[Aktivera lösen ords återställning via självbetjäning för din organisation](quickstart-sspr.md)

[Aktivera Azure Multi-Factor Authentication för din organisation](howto-mfa-getstarted.md)

[Aktivera kombinerad registrering i din klient organisation](howto-registration-mfa-sspr-combined.md)

[Dokumentation för konfiguration av autentiseringsmetod för slutanvändare](https://aka.ms/securityinfoguide)
