---
title: Autentiseringsmetoder och funktioner – Azure Active Directory
description: Lär dig mer om de olika autentiseringsmetoder och-funktioner som finns i Azure Active Directory för att förbättra och säkra inloggnings händelser
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: 642f2705f54fe8f84cfde7ff039c9a723be59595
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "83770967"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Vilka autentiserings- och verifieringsmetoder är tillgängliga i Azure Active Directory?

Som en del av inloggnings upplevelsen för konton i Azure Active Directory (Azure AD) finns det olika sätt som en användare kan autentisera sig på. Användar namn och lösen ord är det vanligaste sättet som en användare tidigare angav autentiseringsuppgifter. Med moderna autentiserings-och säkerhetsfunktioner i Azure AD kan det grundläggande lösen ordet kompletteras eller ersättas med ytterligare autentiseringsmetoder.

En användare i Azure AD kan välja att autentisera med någon av följande autentiseringsmetoder:

* Traditionellt användar namn och lösen ord
* Microsoft Authenticator inloggning med lösen ord för applösenord
* Säkerhets nyckel för OATH-maskinvara eller FIDO2
* SMS-baserad inloggning med lösen ord

Många konton i Azure AD är aktiverade för självbetjäning för återställning av lösen ord (SSPR) eller Azure Multi-Factor Authentication. Dessa funktioner omfattar ytterligare verifierings metoder, till exempel telefonsamtal eller säkerhets frågor. Vi rekommenderar att du kräver att användarna registrerar flera verifierings metoder. När en metod inte är tillgänglig för en användare kan de välja att autentisera med en annan metod.

I följande tabell beskrivs vilka metoder som är tillgängliga för primär eller sekundär autentisering:

| Metod | Primär autentisering | Sekundär autentisering |
| --- | --- | --- |
| [Lösenord](#password) | Ja | |
| [Microsoft Authenticator-appen](#microsoft-authenticator-app) | Ja (för hands version) | MFA och SSPR |
| [Säkerhets nycklar för FIDO2 (för hands version)](#fido2-security-keys) | Ja | Endast MFA |
| [OATH-programvaru-token](#oath-software-tokens) | No | MFA |
| [OATH-token för maskin vara (för hands version)](#oath-hardware-tokens-preview) | Ja | MFA |
| [SMS](#phone-options) | Ja (för hands version) | MFA och SSPR |
| [Röstsamtal](#phone-options) | No | MFA och SSPR |
| [Säkerhetsfrågor](#security-questions) | No | SSPR – endast |
| [E-postadress](#email-address) | No | SSPR – endast |
| [Applösenord](#app-passwords) | No | MFA i vissa fall |

Den här artikeln beskriver dessa olika autentiserings-och verifierings metoder som är tillgängliga i Azure AD och eventuella specifika begränsningar eller begränsningar.

![Autentiseringsmetoder som används på inloggnings skärmen](media/concept-authentication-methods/overview-login.png)

## <a name="password"></a>lösenordsinställning

Ett Azure AD-lösenord är ofta en av de primära autentiseringsmetoderna. Du kan inte inaktivera autentiseringsmetoden för lösen ord.

Även om du använder en autentiseringsmetod, till exempel [SMS-baserad inloggning](howto-authentication-sms-signin.md) när användaren inte använder sitt lösen ord för att signera, är ett lösen ord kvar som en tillgänglig autentiseringsmetod.

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator-appen

Authenticator-appen ger ytterligare en säkerhets nivå för ditt Azure AD-arbets-eller skol konto eller din Microsoft-konto och är tillgänglig för [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594)och [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6). Med Microsoft Authenticator-appen kan användare autentiseras på ett lösen ords effektivt sätt under inloggningen eller som ett ytterligare verifierings alternativ under självbetjäning för återställning av lösen ord (SSPR) eller Azure Multi-Factor Authentication-händelser.

Användare kan få ett meddelande via mobilappen för att godkänna eller neka eller använda Authenticator-appen för att generera en OATH-verifierings kod som kan anges i ett inloggnings gränssnitt. Om du aktiverar både en meddelande-och verifierings kod kan användare som registrerar Authenticator-appen använda någon av metoderna för att verifiera sin identitet.

Information om hur du använder Authenticator-appen i en inloggnings tolk i stället för en kombination av användar namn och lösen ord finns i [Aktivera lösen ords lös inloggning med Microsoft Authenticator-appen (för hands version)](howto-authentication-passwordless-phone.md).

> [!NOTE]
> Användarna har inte möjlighet att registrera sina mobilappar när de aktiverar SSPR. I stället kan användarna registrera sin mobilapp på [https://aka.ms/mfasetup](https://aka.ms/mfasetup) eller som en del av den kombinerade registreringen av säkerhets information på [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

### <a name="notification-through-mobile-app"></a>Meddelande via mobilapp

Authenticator-appen kan hjälpa till att förhindra obehörig åtkomst till konton och stoppa bedrägliga transaktioner genom att skicka ett meddelande till din smartphone eller surfplatta. Användare visar meddelandet, och om det är legitimt väljer du **Verifiera**. Annars kan de välja **neka**.

![Skärm bild av exempel på en webbläsares fråga om meddelandeautentisering för att slutföra inloggnings processen](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> Om din organisation har personal som arbetar i eller reser till Kina fungerar inte *meddelandet via mobilappen* på Android-enheter i det landet/den regionen. Alternativa autentiseringsmetoder bör göras tillgängliga för dessa användare.

### <a name="verification-code-from-mobile-app"></a>Verifierings kod från mobilapp

Authenticator-appen kan användas som en programvarubaserad token för att generera en OATH-verifierings kod. När du har angett ditt användar namn och lösen ord anger du koden från Authenticator-appen i inloggnings gränssnittet. Verifieringskoden utgör en andra form av autentisering.

Användare kan ha en kombination av upp till fem OATH-token eller Authenticator-program, till exempel Microsoft Authenticator-appen, som har kon figurer ATS för användning när som helst.

> [!WARNING]
> För att säkerställa den högsta säkerhets nivån för lösen ords återställning via självbetjäning när endast en metod krävs för återställning är en verifierings kod det enda alternativet som är tillgängligt för användarna.
>
> När två metoder krävs kan användare återställa med hjälp av ett meddelande eller en verifierings kod utöver andra aktiverade metoder.

## <a name="fido2-security-keys"></a>FIDO2 säkerhets nycklar

Alliansen FIDO (snabb identitet Online) hjälper till att främja Open Authentication-standarder och minska användaren av lösen ord som en form av autentisering. FIDO2 är den senaste standarden som införlivar webbautentiseringen (webauthn)-standarden.

Om du vill använda FIDO2 säkerhets nycklar i stället för en kombination av användar namn och lösen ord, se [Aktivera lösen ords lös FIDO2-inloggning (för hands version)](howto-authentication-passwordless-security-key.md).

Användare kan registrera sig och sedan välja en säkerhets nyckel för FIDO2 i inloggnings gränssnittet som deras huvudsakliga autentiserings sätt. Dessa säkerhets nycklar för FIDO2 är vanligt vis USB-enheter, men kan också använda Bluetooth eller NFC. Med en maskin varu enhet som hanterar autentiseringen ökar säkerheten för ett konto eftersom det inte finns något lösen ord som kan exponeras eller gissas.

FIDO2 säkerhets nycklar i Azure AD är för närvarande en för hands version. Mer information om för hands versionerna finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versionerna.

## <a name="oath-tokens"></a>OATH-token

OATH-TOTP mobilapp (Time-based ett Time-lösenordet) är en öppen standard som anger hur eng ång slö sen ord (eng ång slö sen ord) genereras. OATH-TOTP mobilapp kan implementeras med hjälp av antingen program vara eller maskin vara för att generera koderna. Azure AD har inte stöd för OATH-HOTP, en annan kodgenerering standard.

### <a name="oath-software-tokens"></a>OATH-programvaru-token

OATH-token för program vara är vanligt vis program som Microsoft Authenticator-appen och andra Authenticator-appar. Azure AD genererar den hemliga nyckeln eller dirigeringen som inmatas i appen och används för att generera varje eng ång slö sen ord.

Authenticator-appen genererar automatiskt koder när de konfigureras att utföra push-meddelanden så att en användare har en säkerhets kopia även om deras enheter inte har någon anslutning. Program från tredje part som använder OATH-TOTP mobilapp för att generera koder kan också användas.

Vissa TOTP mobilapp-token för OATH är programmerbara, vilket innebär att de inte har en hemlig nyckel eller ett förprogrammerat dirigerings-. Dessa programmerbara maskinvaru-token kan konfigureras med hjälp av den hemliga nyckeln eller dirigeringen som hämtats från installations flödet för programtoken. Kunder kan köpa dessa token från den leverantör de väljer och använda den hemliga nyckeln eller dirigeringen i deras leverantörs konfigurations process.

### <a name="oath-hardware-tokens-preview"></a>OATH-token för maskin vara (för hands version)

Azure AD stöder användningen av OATH-TOTP mobilapp SHA-1-token som uppdaterar koder var 30: e sekund eller 60 sekunder. Kunder kan köpa dessa token från den leverantör de väljer.

OATH TOTP mobilapp-token levereras vanligt vis med en hemlig nyckel eller dirigeras, förprogrammeras i token. De här nycklarna måste vara inmatade i Azure AD enligt beskrivningen i följande steg. Hemliga nycklar är begränsade till 128 tecken, som kanske inte är kompatibla med alla tokens. Den hemliga nyckeln får bara innehålla tecknen *a-z* eller *a-z* och siffror *1-7*och måste kodas i *Base32*.

Programmerbara OATH TOTP mobilapp-maskinvaru-token som kan dirigeras om kan också konfigureras med Azure AD i installations flödet för programtoken.

OATH-token för OATH-enheter stöds som en del av en offentlig för hands version. Mer information om för hands versionerna finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) versionerna

![Laddar upp OATH-token till bladet MFA OATH-token](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

När token har erhållits måste de överföras i ett fil format med kommaavgränsade värden (CSV), inklusive UPN, serie nummer, hemlig nyckel, tidsintervall, tillverkare och modell som visas i följande exempel:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Se till att du inkluderar rubrik raden i CSV-filen.

När den är korrekt formaterad som en CSV-fil kan en administratör logga in på Azure Portal, navigera till **Azure Active Directory > säkerhet > MFA > Oath-token**och ladda upp den resulterande CSV-filen.

Beroende på storleken på CSV-filen kan det ta några minuter att bearbeta. Välj knappen **Uppdatera** för att hämta aktuell status. Om det finns fel i filen kan du ladda ned en CSV-fil som visar eventuella fel som du kan lösa. Fält namnen i den hämtade CSV-filen skiljer sig från den överförda versionen.

När eventuella fel har åtgärd ATS kan administratören aktivera varje nyckel genom att välja **Aktivera** för token och ange det eng ång slö sen ord som visas i token.

Användare kan ha en kombination av upp till fem OATH-token eller Authenticator-program, till exempel Microsoft Authenticator-appen, som har kon figurer ATS för användning när som helst.

## <a name="phone-options"></a>Telefon alternativ

För direkt autentisering med textmeddelande kan du [Konfigurera och aktivera användare för SMS-baserad autentisering (för hands version)](howto-authentication-sms-signin.md). SMS-baserad inloggning är perfekt för anställda. Med SMS-baserad inloggning behöver användarna inte känna till ett användar namn och lösen ord för att få åtkomst till program och tjänster. Användaren anger i stället sitt registrerade mobiltelefon nummer, tar emot ett SMS med en verifierings kod och anger det i inloggnings gränssnittet.

Användarna kan också kontrol lera att de använder en mobil telefon eller en arbets telefon som sekundär form av autentisering som används under Azure Multi-Factor Authentication eller lösen ords återställning via självbetjäning (SSPR).

För att fungera korrekt måste telefonnumret vara i formatet *+ CountryCode telefonnummer*, till exempel *+ 1 4251234567*.

> [!NOTE]
> Det måste finnas ett blank steg mellan lands-/region koden och telefonnumret.
>
> Lösen ords återställning stöder inte telefon tillägg. Även i formatet *+ 1 4251234567X12345* tas tilläggen bort innan anropet placeras.

### <a name="mobile-phone-verification"></a>Mobil telefon verifiering

För Azure Multi-Factor Authentication eller SSPR kan användarna välja att ta emot ett SMS med en verifierings kod för att komma in i inloggnings gränssnittet eller ta emot ett telefonsamtal med en uppmaning om att ange deras definierade PIN-kod.

Om användarna inte vill att deras mobiltelefon nummer ska synas i katalogen, men vill använda det för lösen ords återställning, behöver inte administratörerna fylla i telefonnumret i katalogen. I stället ska användarna fylla i sina attribut för **autentisering** via den kombinerade registreringen av säkerhets information på [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) . Administratörer kan se den här informationen i användarens profil, men den publiceras inte någon annan stans.

![Skärm bild av Azure Portal som visar autentiseringsmetoder med ett telefonnummer som är ifyllt](media/concept-authentication-methods/user-authentication-methods.png)

Microsoft garanterar inte konsekvent SMS eller Voice-baserad Azure Multi-Factor Authentication Prompt-leverans med samma nummer. Vi kan när som helst lägga till eller ta bort korta koder när vi gör väg justeringar för att förbättra SMS-leveranser. Microsoft stöder inte korta koder för länder/regioner utöver USA och Kanada.

#### <a name="text-message-verification"></a>Verifiering av textmeddelande

Med text meddelande verifiering under SSPR eller Azure Multi-Factor Authentication skickas ett SMS till det mobiltelefon nummer som innehåller en verifierings kod. För att slutföra inloggnings processen anges den angivna verifierings koden i inloggnings gränssnittet.

#### <a name="phone-call-verification"></a>Verifiering av telefonsamtal

Med Telefonsamtals verifiering under SSPR eller Azure Multi-Factor Authentication görs ett automatiskt röst samtal till det telefonnummer som registrerats av användaren. För att slutföra inloggnings processen uppmanas användaren att ange sina PIN-nummer följt av # på deras knapps ATS.

### <a name="office-phone-verification"></a>Office Phone-verifiering

Office Phone-attributet hanteras av Azure AD-administratören och kan inte registreras av en användare själva.

Med Telefonsamtals verifiering under SSPR eller Azure Multi-Factor Authentication görs ett automatiskt röst samtal till det telefonnummer som registrerats av användaren. För att slutföra inloggnings processen uppmanas användaren att ange sina PIN-nummer följt av # på deras knapps ATS.

### <a name="troubleshooting-phone-options"></a>Felsöka telefonalternativ

Om du har problem med autentiseringen av telefonen för Azure AD kan du läsa följande fel söknings steg:

* Blockerat anropar-ID på en enskild enhet.
   * Granska eventuella blockerade tal som kon figurer ATS på enheten.
* Fel telefonnummer eller felaktig landskod, eller förvirring mellan personligt telefonnummer och arbets telefonnummer.
   * Felsök objektet användare och konfigurerade autentiseringsmetoder. Kontrol lera att rätt telefonnummer har registrerats.
* Fel PIN-kod har angetts.
   * Bekräfta att användaren har använt rätt PIN-kod som registrerad för sitt konto.
* Samtalet har vidarebefordrats till röst meddelanden.
   * Se till att användaren har aktiverat telefonen och att tjänsten är tillgänglig i deras Area eller Använd en annan metod.
* Användaren har blockerats
   * Låt en Azure AD-administratör häva blockeringen av användaren i Azure Portal.
* SMS prenumererar inte på enheten.
   * Låt användaren ändra metoder eller aktivera SMS på enheten.
* Defekta tele leverantörer, till exempel inga telefonin uppgifter identifierade, problem med DTMF-toner som saknas, blockerat anrops-ID på flera enheter eller blockerat SMS över flera enheter.
   * Microsoft använder flera telekom-leverantörer för att skicka telefonsamtal och SMS-meddelanden för autentisering. Om du ser något av ovanstående problem, kan en användare försöka använda metoden minst fem gånger inom 5 minuter och ha den användarens information tillgänglig när de kontaktar Microsoft support.

## <a name="security-questions"></a>Säkerhetsfrågor

Säkerhets frågor används inte som autentiseringsmetod under en inloggnings händelse. I stället kan säkerhets frågor användas under processen för självbetjäning för återställning av lösen ord (SSPR) för att bekräfta vem du är. Administratörs konton kan inte använda säkerhets frågor som verifieringsmetod med SSPR.

När användarna registrerar sig för SSPR uppmanas de att välja de autentiseringsmetoder som ska användas. Om de väljer att använda säkerhets frågor, väljs de från en uppsättning frågor att fråga efter och ger sina egna svar.

![Skärm bild av Azure Portal som visar autentiseringsmetoder och alternativ för säkerhets frågor](media/concept-authentication-methods/security-questions-authentication-method.png)

> [!NOTE]
> Säkerhets frågor lagras privat och säkert på ett användar objekt i katalogen och kan bara besvaras av användare under registreringen. Det finns inget sätt för en administratör att läsa eller ändra en användares frågor eller svar.

Säkerhets frågor kan vara mindre säkra än andra metoder eftersom vissa personer kanske känner till svaren på en annan användares frågor. Om du använder säkerhets frågor med SSPR rekommenderar vi att du använder dem tillsammans med en annan metod. En användare kan uppmanas att använda Microsoft Authenticator appen eller telefonen för att verifiera sin identitet under SSPR-processen och välja säkerhets frågor enbart om de inte har sin telefon eller registrerade enhet.

### <a name="predefined-questions"></a>Fördefinierade frågor

Följande fördefinierade säkerhets frågor är tillgängliga för användning som en verifieringsmetod med SSPR. Alla dessa säkerhets frågor översätts och lokaliseras till en fullständig uppsättning Office 365-språk baserat på användarens språk:

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
* Vad är ditt äldsta syskons födelsedags månad och år? (till exempel november 1985)
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

### <a name="custom-security-questions"></a>Anpassade säkerhets frågor

För ytterligare flexibilitet kan du definiera egna säkerhets frågor. Den maximala längden för en anpassad säkerhets fråga är 200 tecken.

Anpassade säkerhets frågor lokaliseras inte automatiskt som med standard säkerhets frågorna. Alla anpassade frågor visas på samma språk som de är angivna i det administrativa användar gränssnittet, även om användarens webbläsare är annorlunda. Om du behöver lokaliserade frågor bör du använda de fördefinierade frågorna.

### <a name="security-question-requirements"></a>Krav för säkerhets fråga

För både standard-och anpassade säkerhets frågor gäller följande krav och begränsningar:

* Gränsen för minsta antal svars tecken är tre tecken.
* Gränsen för maximalt antal svars tecken är 40 tecken.
* Användare kan inte besvara samma fråga mer än en gången.
* Användare kan inte ange samma svar på fler än en fråga.
* Alla teckenuppsättningar kan användas för att definiera frågor och svar, inklusive Unicode-tecken.
* Antalet frågor som definieras måste vara större än eller lika med antalet frågor som krävs för registrering.

## <a name="email-address"></a>E-postadress

Det går inte att använda en e-postadress som metod för direkt autentisering. E-postadressen är bara tillgänglig som ett verifierings alternativ för lösen ords återställning via självbetjäning (SSPR). När e-postadress väljs under SSPR skickas ett e-postmeddelande till användaren för att slutföra autentiserings-/verifierings processen.

Vid registrering för SSPR tillhandahåller en användare den e-postadress som ska användas. Vi rekommenderar att de använder ett annat e-postkonto än företagets konto för att se till att de kan komma åt det under SSPR.

## <a name="app-passwords"></a>Applösenord

Vissa äldre appar för icke-webbläsare förstår inte pauser eller avbrott i autentiseringsprocessen. Om en användare är aktive rad för Multi-Factor Authentication och försöker använda en av dessa äldre appar från en annan webbläsare, kan de vanligt vis inte autentisera sig. Ett applösenord gör att användarna kan fortsätta att autentisera med äldre appar som inte är webbläsare utan avbrott.

Som standard kan användarna inte skapa applösenord. Om du behöver tillåta användare att skapa applösenord väljer du alternativet **Tillåt användare att skapa applösenord för att logga in i appar som inte är webbläsare** under *tjänst inställningar* för användares Azure Multi-Factor Authentication egenskaper.

![Skärm bild av Azure Portal som visar tjänst inställningarna för Multi-Factor Authentication för att tillåta användare av applösenord](media/concept-authentication-methods/app-password-authentication-method.png)

Om du tillämpar Azure Multi-Factor Authentication att använda principer för villkorlig åtkomst och inte via användarspecifika MFA-användare kan du inte skapa applösenord. Moderna program som använder principer för villkorlig åtkomst för att kontrol lera åtkomsten behöver inte applösenord.

Om din organisation är federerad för enkel inloggning (SSO) med Azure AD och du använder Azure Multi-Factor Authentication gäller följande aspekter:

* Appens lösen ord verifieras av Azure AD, så kringgår federationen. Federation används endast när du konfigurerar applösenord. För federerade (SSO) användare lagras lösen ord i organisations-ID: t. Om användaren lämnar företaget måste denna information flöda till organisations-ID med hjälp av DirSync. Kontot för att inaktivera eller ta bort händelser kan ta upp till tre timmar att synkronisera, vilket fördröjer inaktive ring/borttagning av applösenord i Azure AD.
* Lokala klient Access Controls inställningar hanteras inte av applösenord.
* Ingen lokal autentiserings-eller gransknings funktion är tillgänglig för applösenord.
* Vissa avancerade arkitektur design kan kräva att du använder en kombination av organisationens användar namn och lösen ord och applösenord när du använder Multi-Factor Authentication, beroende på var de autentiserar sig.
    * För klienter som autentiseras mot en lokal infrastruktur använder du ett organisations användar namn och lösen ord.
    * För klienter som autentiseras mot Azure AD använder du appens lösen ord.

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång finns i [självstudien för självbetjäning för återställning av lösen ord (SSPR)][tutorial-sspr] och [Azure Multi-Factor Authentication][tutorial-azure-mfa].

Mer information om SSPR-koncept finns i [hur Azure AD självbetjäning för återställning av lösen ord fungerar][concept-sspr].

Mer information om MFA-koncept finns i [hur Azure Multi-Factor Authentication fungerar][concept-mfa].

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md