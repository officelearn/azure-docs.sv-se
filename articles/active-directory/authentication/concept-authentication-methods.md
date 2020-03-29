---
title: Autentiseringsmetoder - Azure Active Directory
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968779"
---
# <a name="what-are-authentication-methods"></a>Vad är autentiseringsmetoder?

Som administratör rekommenderar du att du registrerar autentiseringsmetoder för Azure Multi-Factor Authentication och självbetjäningslösenordsåterställning (SSPR) att du kräver att användarna registrerar flera autentiseringsmetoder. När en autentiseringsmetod inte är tillgänglig för en användare kan de välja att autentisera med en annan metod.

Administratörer kan i princip definiera vilka autentiseringsmetoder som är tillgängliga för användare av SSPR och MFA. Vissa autentiseringsmetoder kanske inte är tillgängliga för alla funktioner. Mer information om hur du konfigurerar dina principer finns i artiklarna [Så här distribuerar du självbetjäningslösenordsåterställning](howto-sspr-deployment.md) och [planera en molnbaserad Azure Multi-Factor-autentisering](howto-mfa-getstarted.md)

Microsoft rekommenderar starkt administratörer att göra det möjligt för användare att välja mer än det minsta antal autentiseringsmetoder som krävs om de inte har tillgång till en.

|Autentiseringsmetod|Användning|
| --- | --- |
| lösenord | MFA och SSPR |
| Säkerhetsfrågor | Endast SSPR |
| E-postadress | Endast SSPR |
| Microsoft Authenticator-appen | MFA och SSPR |
| OATH-maskinvarutoken | Offentlig förhandsversion för MFA och SSPR |
| SMS | MFA och SSPR |
| Röstsamtal | MFA och SSPR |
| Applösenord | Makroekonomiskt stöd endast i vissa fall |

![Autentiseringsmetoder som används på inloggningsskärmen](media/concept-authentication-methods/overview-login.png)

|     |
| --- |
| OATH-maskinvarutoken för MFA och SSPR är offentliga förhandsversionsfunktioner i Azure Active Directory. Mer information om förhandsgranskningar finns i [Tilläggsvillkor för användning för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="password"></a>lösenord

Ditt Azure AD-lösenord betraktas som en autentiseringsmetod. Det är en metod som **inte kan inaktiveras**.

## <a name="security-questions"></a>Säkerhetsfrågor

Säkerhetsfrågor är endast tillgängliga **i Azure AD-självbetjäningslösenordsåterställning** till konton som inte är administratörskonton.

Om du använder säkerhetsfrågor rekommenderar vi att du använder dem tillsammans med en annan metod. Säkerhetsfrågor kan vara mindre säkra än andra metoder eftersom vissa personer kanske känner till svaren på en annan användares frågor.

> [!NOTE]
> Säkerhetsfrågor lagras privat och säkert på ett användarobjekt i katalogen och kan endast besvaras av användare under registreringen. Det finns inget sätt för en administratör att läsa eller ändra en användares frågor eller svar.
>

### <a name="predefined-questions"></a>Fördefinierade frågor

* I vilken stad träffade du din första make / partner?
* I vilken stad träffade dina föräldrar?
* I vilken stad bor ditt närmaste syskon?
* I vilken stad föddes din far?
* I vilken stad var ditt första jobb?
* I vilken stad föddes din mamma?
* Vilken stad var du i på nyår 2000?
* Vad är efternamnet på din favoritlärare i gymnasiet?
* Vad heter ett college du sökte till men deltog inte?
* Vad heter den plats där du höll din första bröllopsfest?
* Vad är din fars mellannamn?
* Vilken är din favoritmat?
* Vad är din mormors för- och efternamn?
* Vad är din mammas mellannamn?
* Vilken är ditt äldsta syskons födelsedag månad och år? (t.ex. november 1985)
* Vilket är ditt äldsta syskons mellannamn?
* Vad är din farfars för- och efternamn?
* Vilket är ditt yngsta syskons mellannamn?
* Vilken skola gick du i sexan?
* Vilket var för- och efternamn på din barndoms bästa vän?
* Vilket var för- och efternamn på din första betydande andra?
* Vad hette din favoritlärare?
* Vad var märke och modell av din första bil eller motorcykel?
* Vad hette den första skolan du gick på?
* Vad hette sjukhuset där du föddes?
* Vad hette gatan i ditt första barndomshem?
* Vad hette din barndomshjälte?
* Vad hette ditt favoritfyllda djur?
* Vad hette ditt första husdjur?
* Vad var ditt barndomsmeknamn?
* Vilken var din favoritsport i high school?
* Vilket var ditt första jobb?
* Vilka var de fyra sista siffrorna i ditt barndomstelefonnummer?
* När du var ung, vad ville du bli när du växte upp?
* Vem är den mest kända person du någonsin träffat?

Alla fördefinierade säkerhetsfrågor översätts och lokaliseras till hela uppsättningen Office 365-språk baserat på användarens webbläsares språk.

### <a name="custom-security-questions"></a>Anpassade säkerhetsfrågor

Anpassade säkerhetsfrågor är inte lokaliserade. Alla anpassade frågor visas på samma språk som de anges i det administrativa användargränssnittet, även om användarens webbläsarspråk är annorlunda. Om du behöver lokaliserade frågor bör du använda de fördefinierade frågorna.

Den maximala längden på en anpassad säkerhetsfråga är 200 tecken.

### <a name="security-question-requirements"></a>Krav på säkerhetsfråga

* Minsta gräns för svarstecken är tre tecken.
* Den maximala svarsteckengränsen är 40 tecken.
* Användare kan inte svara på samma fråga mer än en gång.
* Användare kan inte ge samma svar på mer än en fråga.
* Alla teckenuppsättningar kan användas för att definiera frågorna och svaren, inklusive Unicode-tecken.
* Antalet frågor som definieras måste vara större än eller lika med det antal frågor som krävdes för att registrera sig.

## <a name="email-address"></a>E-postadress

E-postadress är endast tillgänglig **i Azure AD självbetjäning lösenordsåterställning**.

Microsoft rekommenderar användning av ett e-postkonto som inte kräver användarens Azure AD-lösenord för åtkomst.

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator-appen

Microsoft Authenticator-appen ger en extra säkerhetsnivå till ditt Azure AD-arbets- eller skolkonto eller ditt Microsoft-konto.

Microsoft Authenticator-appen finns för [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) och [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6).

> [!NOTE]
> Användarna har inte möjlighet att registrera sin mobilapp när de registrerar sig för återställning av lösenord med självbetjäning. I stället kan användarna registrera [https://aka.ms/mfasetup](https://aka.ms/mfasetup) sin mobilapp på eller [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)i förhandsversionen av registrering av säkerhetsinformation på .
>

### <a name="notification-through-mobile-app"></a>Avisering via mobilapp

Microsoft Authenticator-appen kan hjälpa till att förhindra obehörig åtkomst till konton och stoppa bedrägliga transaktioner genom att skicka ett meddelande till din smartphone eller surfplatta. Användare visar meddelandet och om det är legitimt väljer du Verifiera. Annars kan de välja Neka.

> [!WARNING]
> För återställning av lösenord för självbetjäning när endast en metod krävs för återställning är verifieringskod det enda alternativ som är tillgängligt för användare **för att säkerställa högsta säkerhetsnivå**.
>
> När två metoder krävs användare kommer att kunna återställa med **antingen** anmälan **eller** verifieringskod utöver alla andra aktiverade metoder.
>

Om du aktiverar användningen av både meddelanden via mobilapp och verifieringskod från mobilappen kan användare som registrerar Microsoft Authenticator-appen med hjälp av ett meddelande använda både meddelande och kod för att verifiera sin identitet.

> [!NOTE]
> Om din organisation har personal som arbetar i eller reser till Kina fungerar inte **metoden Avisering via mobilapp** på **Android-enheter** i det landet. Alternativa metoder bör göras tillgängliga för dessa användare.

### <a name="verification-code-from-mobile-app"></a>Verifieringskod från mobilapp

Microsoft Authenticator-appen eller andra appar från tredje part kan användas som en programvarutoken för att generera en OATH-verifieringskod. När du har angett ditt användarnamn och lösenord anger du koden som appen tillhandahåller på inloggningsskärmen. Verifieringskoden utgör en andra form av autentisering.

> [!WARNING]
> För återställning av lösenord med självbetjäning när endast en metod krävs för återställning av verifieringskod är det enda alternativ som är tillgängligt för användare **för att säkerställa högsta säkerhetsnivå**.
>

Användare kan ha en kombination av upp till fem OATH-maskinvarutoken eller autentiseringsprogram, till exempel Microsoft Authenticator-appen som konfigurerats för användning när som helst.

## <a name="oath-hardware-tokens-public-preview"></a>OATH-maskinvarutoken (offentlig förhandsversion)

OATH är en öppen standard som anger hur engångskoder för lösenord (OTP) genereras. Azure AD stöder användningen av OATH-TOTP SHA-1-tokens för sorten 30 sekunder eller 60 sekunder. Kunder kan anskaffa dessa token från leverantören som de själva väljer. Hemliga nycklar är begränsade till 128 tecken, vilket kanske inte är kompatibelt med alla tokens. Den hemliga nyckeln kan bara innehålla tecknen *a-z* eller *AZ* och siffrorna *1-7*och måste kodas i Base32.

![Ladda upp OATH-tokens till tokensbladet MFA OATH](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

OATH-maskinvarutoken stöds som en del av en offentlig förhandsversion. Mer information om förhandsgranskningar finns i [Tilläggsvillkor för användning för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

När token har förvärvats måste de laddas upp i ett CSV-filformat (comma-separated values), inklusive UPN, serienummer, hemlig nyckel, tidsintervall, tillverkare och modell som visas i följande exempel:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Se till att du inkluderar rubrikraden i CSV-filen.

När en CSV-fil har formaterats korrekt som en CSV-fil kan han sedan logga in på Azure-portalen, navigera till **Azure Active Directory** > **Security** > **MFA** > **OATH-tokens**och ladda upp den resulterande CSV-filen.

Beroende på storleken på CSV-filen kan det ta några minuter att bearbeta. Klicka på knappen **Uppdatera** för att få aktuell status. Om det finns några fel i filen har du möjlighet att hämta en CSV-fil med eventuella fel som du kan lösa. Fältnamnen i den hämtade CSV-filen skiljer sig från den uppladdade versionen.

När eventuella fel har åtgärdats kan administratören aktivera varje nyckel genom att klicka på **Aktivera** för att token ska aktiveras och ange otp som visas på token.

Användare kan ha en kombination av upp till fem OATH-maskinvarutoken eller autentiseringsprogram, till exempel Microsoft Authenticator-appen som konfigurerats för användning när som helst.

## <a name="phone-options"></a>Telefonalternativ

### <a name="mobile-phone"></a>Mobiltelefon

Två alternativ är tillgängliga för användare med mobiltelefoner.

Om användarna inte vill att deras mobiltelefonnummer ska vara synligt i katalogen, men de ändå vill använda det för återställning av lösenord, bör administratörer inte fylla i det i katalogen. Användare bör fylla i **attributet Authentication Phone** via [registreringsportalen för lösenordsåterställning](https://aka.ms/ssprsetup). Administratörer kan se den här informationen i användarens profil, men den publiceras inte någon annanstans.

För att fungera korrekt måste telefonnummer vara i formatet *+CountryCode PhoneNumber*, till exempel +1 4255551234.

> [!NOTE]
> Det måste finnas ett mellanslag mellan landskoden och telefonnumret.
>
> Återställning av lösenord stöder inte telefontillägg. Även i +1 4255551234X12345 format, tillägg tas bort innan samtalet görs.

Microsoft garanterar inte konsekvent SMS- eller röstbaserad flerfaktorsautentiseringsprompt med samma nummer. I våra användares intresse kan Microsoft lägga till eller ta bort korta koder när som helst när vi gör ruttjusteringar för att förbättra SMS-leveransbarheten. Microsoft stöder inte korta koder för länder/regioner förutom USA och Kanada.

#### <a name="text-message"></a>Textmeddelande

Ett SMS skickas till mobiltelefonnumret som innehåller en verifieringskod. Ange verifieringskoden som finns i inloggningsgränssnittet för att fortsätta.

#### <a name="phone-call"></a>Telefonsamtal

Ett automatiskt röstsamtal görs till det telefonnummer du anger. Svara på samtalet och tryck på # i telefonens knappsats för att autentisera

> [!IMPORTANT]
> Från och med mars 2019 är samtalsalternativen inte tillgängliga för MFA- och SSPR-användare i Azure AD-klienter med kostnadsfri/utvärderingsversion. SMS-meddelanden påverkas inte av den här ändringen. Telefonsamtal fortsätter att vara tillgängligt för användare i betalda Azure AD-klienter. Den här ändringen påverkar endast azure AD-klienter som är kostnadsfria/prov.

### <a name="office-phone"></a>Arbetstelefon

Ett automatiskt röstsamtal görs till det telefonnummer du anger. Svara på samtalet och trycker på # i telefonens knappsats för att autentisera.

För att fungera korrekt måste telefonnummer vara i formatet *+CountryCode PhoneNumber*, till exempel +1 4255551234.

Office-telefonattributet hanteras av administratören.

> [!IMPORTANT]
> Från och med mars 2019 är samtalsalternativen inte tillgängliga för MFA- och SSPR-användare i Azure AD-klienter med kostnadsfri/utvärderingsversion. SMS-meddelanden påverkas inte av den här ändringen. Telefonsamtal fortsätter att vara tillgängligt för användare i betalda Azure AD-klienter. Den här ändringen påverkar endast azure AD-klienter som är kostnadsfria/prov.

> [!NOTE]
> Det måste finnas ett mellanslag mellan landskoden och telefonnumret.
>
> Återställning av lösenord stöder inte telefontillägg. Även i +1 4255551234X12345 format, tillägg tas bort innan samtalet görs.

### <a name="troubleshooting-phone-options"></a>Felsöka telefonalternativ

Vanliga problem relaterade till autentiseringsmetoder med hjälp av ett telefonnummer:

* Blockerat nummerpresentatör på en enda enhet
   * Felsöka enhet
* Fel telefonnummer, felaktig landskod, hemtelefonnummer kontra arbetstelefonnummer
   * Felsöka användarobjekt och konfigurerade autentiseringsmetoder. Kontrollera att korrekta telefonnummer är registrerade.
* Fel PIN-kod har angetts
   * Bekräfta att användaren har använt rätt PIN-kod som registrerats i Azure MFA Server.
* Samtal vidarebefordras till röstbrevlådan
   * Kontrollera att användaren har aktiverat telefonen och att tjänsten är tillgänglig i deras område eller använd alternativ metod.
* Användaren har blockerats
   * Låt administratören häva blockeringen av användaren i Azure-portalen.
* SMS prenumererar inte på enheten
   * Låt användaren ändra metoder eller aktivera SMS på enheten.
* Felaktiga telekomleverantörer (Ingen telefoningång har upptäckts, DTMF-toner saknas, blockerade nummerpresentatör på flera enheter eller blockerade SMS på flera enheter)
   * Microsoft använder flera telekomleverantörer för att dirigera telefonsamtal och SMS-meddelanden för autentisering. Om du ser något av ovanstående problem har en användare försöker använda metoden minst 5 gånger inom 5 minuter och har den användarens information tillgänglig när du kontaktar Microsoft-supporten.

## <a name="app-passwords"></a>Lösenord för appar

Vissa appar som inte är webbläsare stöder multifaktorautentisering, om en användare har aktiverats för multifaktorautentisering och försöker använda appar som inte är webbläsare, kan de inte autentisera. Med ett applösenord kan användarna fortsätta att autentisera

Om du framtvingar multifaktorautentisering via principer för villkorlig åtkomst och inte via MFA per användare kan du inte skapa applösenord. Program som använder principer för villkorlig åtkomst för att kontrollera åtkomsten behöver inte applösenord.

Om din organisation är federerad för SSO med Azure AD och du kommer att använda Azure MFA bör du vara medveten om följande information:

* Applösenordet verifieras av Azure AD och kringgår därför federationen. Federation används bara när du ställer in applösenord. För federerade (SSO) användare lagras lösenord i organisations-ID. Om användaren lämnar företaget måste den informationen flöda till organisations-ID med DirSync. Det kan ta upp till tre timmar att synkronisera kontot, vilket försenar inaktivera/ta bort applösenord i Azure AD.
* Inställningar för lokal klientåtkomstkontroll respekteras inte av applösenord.
* Det finns ingen lokal autentiseringsloggnings-/granskningsfunktion tillgänglig för applösenord.
* Vissa avancerade arkitektoniska konstruktioner kan kräva att du använder en kombination av organisationsanvändarnamn och lösenord och applösenord när du använder tvåstegsverifiering med klienter, beroende på var de autentiserar. För klienter som autentiserar mot en lokal infrastruktur använder du ett organisationsanvändarnamn och lösenord. För klienter som autentiserar mot Azure AD använder du applösenordet.
* Som standard kan användare inte skapa applösenord. Om du behöver tillåta användare att skapa applösenord väljer du **alternativet Tillåt användare att skapa applösenord för att logga in på program som inte är webbläsarprogram** under tjänstinställningar.

## <a name="next-steps"></a>Nästa steg

[Aktivera lösenordsåterställning av självbetjäning för din organisation](quickstart-sspr.md)

[Aktivera Azure Multi Factor-autentisering för din organisation](howto-mfa-getstarted.md)

[Aktivera kombinerad registrering i din klientorganisation](howto-registration-mfa-sspr-combined.md)

[Konfigurationsdokumentation för autentiseringsmetod för slutanvändare](https://aka.ms/securityinfoguide)
