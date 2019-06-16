---
title: Rapporter för Azure MFA - Azure Active Directory åtkomst och användning
description: Här beskrivs hur du använder funktionen för Azure Multi-Factor Authentication - rapporter.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9cf0b848e551d3c0e7d7275af9eb2c8e970dbd9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67113389"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Rapporter i Azure Multi-Factor Authentication

Azure Multi-Factor Authentication innehåller flera rapporter som kan användas av dig och din organisation som är tillgängliga via Azure-portalen. I följande tabell visas de tillgängliga rapporterna:

| Rapport | Location | Beskrivning |
|:--- |:--- |:--- |
| Historik över blockerad användare | Azure AD > MFA Server > blockera/avblockera användare | Visar historiken över förfrågningar om att blockera eller avblockera användare. |
| Användning och bedrägeri aviseringar | Azure AD > inloggningar | Innehåller information om totala användning, Användarsammanfattning och information om användare; samt en historik över bedrägerivarningar som skickats under det angivna datumintervallet. |
| Användningen av lokala komponenter | Azure AD > MFA-servern > Aktivitetsrapport | Innehåller information om totala användning för MFA via NPS-tillägget, ADFS, och MFA-servern. |
| Historik över åsidosatt användare | Azure AD > MFA Server > engångsförbikoppling | Innehåller en historik över förfrågningar om förbikoppling Multi-Factor Authentication för en användare. |
| Serverstatus | Azure AD > MFA Server > serverstatus | Visar status för multi-Factor Authentication-servrar som är associerade med ditt konto. |

## <a name="view-mfa-reports"></a>Visa rapporter i MFA

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster, Välj **Azure Active Directory** > **MFA Server**.
3. Välj den rapport som du vill visa.

   ![Statusrapport för MFA Server i Azure portal](./media/howto-mfa-reporting/report.png)

## <a name="azure-ad-sign-ins-report"></a>Rapporten med Azure AD-inloggningar

Med den **aktivitetsrapport för inloggningar** i den [Azure-portalen](https://portal.azure.com), du kan få den information du behöver för att fastställa hur din miljö.

Rapporten inloggningar kan ge dig med information om användningen av hanterade program och användare logga in aktiviteter, som innehåller information om användning av multifaktorautentisering (MFA). MFA-data ger dig information om hur MFA fungerar i din organisation. Du får hjälp att besvara frågor som:

- Kontrollerades inloggningen med MFA?
- Hur slutförde användaren MFA?
- Varför kunde användaren inte slutföra MFA?
- Hur många användare kontrolleras av MFA?
- Hur många användare kan inte slutföra MFA-kontrollen?
- Vilka vanliga MFA-relaterade problem råkar slutanvändarna ut för?

Informationen är tillgänglig via den [Azure-portalen](https://portal.azure.com) och [reporting API](../reports-monitoring/concept-reporting-api.md).

![Azure AD rapporten inloggningar i Azure portal](./media/howto-mfa-reporting/sign-in-report.png)

### <a name="sign-ins-report-structure"></a>Inloggningar rapportens struktur

Rapporterna om inloggningsaktiviteter för MFA ger dig åtkomst till följande information:

**MFA krävs:** Om MFA krävs för att logga in eller inte. MFA kan krävas på grund av MFA per användare, villkorlig åtkomst eller andra orsaker. Möjliga värden är **Ja** eller **nr**.

**MFA-resultat:** Mer information om huruvida MFA uppfylldes eller nekades:

- Om MFA uppfylldes tillhandahåller den här kolumnen mer information om hur MFA uppfylldes.
   - Azure Multi-Factor Authentication
      - slutfört i molnet
      - har upphört på grund av de principer som konfigurerats på klienten
      - uppmaning om registrering
      - uppfyllt av anspråk i token
      - uppfyllt av anspråk som tillhandahållits av en extern provider
      - uppfyllt av stark autentisering
      - hoppades över eftersom flödet som utnyttjades var flödet för inloggning av asynkron Windows-meddelandekö
      - hoppades över på grund av applösenord
      - hoppades över på grund av plats
      - hoppades över på grund av en registrerad enhet
      - hoppades över på grund av en sparad enhet
      - slutfördes
   - Omdirigerad till extern provider för multifaktorautentisering

- Om MFA nekades innehåller den här kolumnen orsaken till nekandet.
   - Azure Multi-Factor Authentication nekad;
      - autentisering pågår
      - duplicera autentiseringsförsök
      - angett felaktig kod för många gånger
      - ogiltig autentisering
      - ogiltig verifieringskod från mobilapp
      - felaktig konfiguration
      - telefonsamtalet dirigerades till röstmeddelanden
      - telefonnumret har ett ogiltigt format
      - tjänstfel
      - kunde inte nå användarens telefon
      - kunde inte skicka meddelande via mobilapp till enheten
      - kunde inte skicka meddelande via mobilapp
      - användaren nekade autentiseringen
      - användaren svarade inte på meddelandet via mobilappen
      - användaren har inte några verifieringsmetoder registrerade
      - användaren har angett en felaktig kod
      - användaren har angett en felaktig PIN-kod
      - användaren avslutade telefonsamtalet utan att ha slutfört autentiseringen
      - användaren har blockerats
      - användaren angav aldrig verifieringskoden
      - användaren kunde inte hittas
      - verifieringskoden har redan använts en gång

**MFA-autentiseringsmetod:** Autentiseringsmetoden som användaren som används för att slutföra MFA. Möjliga värden omfattar:

- Textmeddelande
- Meddelanden via mobilapp
- Telefonsamtal (autentiseringstelefon)
- Verifieringskod från mobilapp
- Telefonsamtal (kontorstelefon)
- Telefonsamtal (alternativ autentiseringstelefon)

**Information för MFA-autentisering:** Gömd version av telefonnumret, till exempel: + X XXXXXXXX64.

**Villkorlig åtkomst** hitta information om principer för villkorlig åtkomst som påverkade inloggningsförsök, inklusive:

- Principnamn
- Bevilja kontroller
- Sessionskontroller
- Resultat

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>PowerShell rapportering om användare som har registrerats för MFA

Först måste se till att du har den [MSOnline V1 PowerShell-modulen](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0) installerad.

Identifiera användare som har registrerats för MFA med hjälp av PowerShell som följer.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Identifiera användare som inte har registrerats för MFA med hjälp av PowerShell som följer.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="possible-results-in-activity-reports"></a>Resultat i aktivitetsrapporter

I följande tabell kan användas för att felsöka multifaktorautentisering som använder den nedladdade versionen av aktivitetsrapporten multifaktorautentisering. De visas inte direkt i Azure-portalen.

| Samtalsresultat | Beskrivning | Bred beskrivning |
| --- | --- | --- |
| SUCCESS_WITH_PIN | Angiven PIN-kod | Användaren har angett en PIN-kod.  Om autentisering lyckades angett de rätt PIN-koden.  Om authentication nekas blir sedan de har angett fel PIN-kod eller användaren är inställd till standardläge. |
| SUCCESS_NO_PIN | Endast # som har angetts | Om användaren är inställd på att PIN-läge och autentiseringen nekades, innebär det användaren inte anger sin PIN-kod och endast angav #.  Om användaren är inställd till standardläge och autentiseringen lyckas det innebär att angett användaren bara # som är rätt du ska göra i standardläge. |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | # Inte tryckt efter inmatning | Användaren skickade inte några DTMF-siffror eftersom # inte har angetts.  Andra siffror som angav skickas inte om inte # har angetts som du anger för posten. |
|SUCCESS_NO_PIN_BUT_TIMEOUT | Inga Telefonindata – timeout | Samtalet har besvarats, men inget svar.  Detta anger vanligtvis anropet hämtades av röstmeddelanden. |
| SUCCESS_PIN_EXPIRED | PIN-kod har upphört att gälla och har ändrats inte | Användarens PIN-kod har upphört att gälla och de har uppmanats att ändra den, men ändra PIN-kod har inte slutförts. |
| SUCCESS_USED_CACHE | Använd Cache | Autentiseringen har slutförts utan Multi-Factor Authentication-samtal eftersom en tidigare lyckad autentisering för samma användarnamn inträffade inom den konfigurerade cacheplatsen tidsramen. |
| SUCCESS_BYPASSED_AUTH | Autentisering via förbikoppling | Autentisering lyckades med hjälp av en Engångsförbikoppling initieras för användaren.  Se kringgås historik över användare för mer information på förbikopplingen. |
| SUCCESS_USED_IP_BASED_CACHE | Använd IP-baserad Cache | Autentisering lyckades utan Multi-Factor Authentication-samtal eftersom en tidigare lyckad autentisering för samma användarnamn, autentiseringstyp, programnamn och IP inträffade inom den konfigurerade cacheplatsen tidsramen. |
| SUCCESS_USED_APP_BASED_CACHE | Använd App-baserade Cache | Autentiseringen har slutförts utan Multi-Factor Authentication-samtal eftersom en tidigare lyckad autentisering för samma användarnamn, autentiseringstyp och programnamn inom den konfigurerade cacheplatsen tidsramen. |
| SUCCESS_INVALID_INPUT | Ogiltig Telefoninmatning | Svaret från telefonen är inte giltig.  Detta kan vara från en fax eller modem eller användaren kan ha övergått * som en del av sin PIN-kod. |
| SUCCESS_USER_BLOCKED | Användaren är blockerad | Användarens telefonnummer är blockerad.  Ett tal som blockerade kan initieras av användaren under en authentication-samtal eller av en administratör med hjälp av Azure portal. <br> OBS!  Ett blockerat tal är också en byproduct av ett bedrägeriförsök. |
| SUCCESS_SMS_AUTHENTICATED | Autentiserat via textmeddelande | För dubbelriktad testmeddelande svarat användaren korrekt med deras engångskod (OTP) eller OTP och PINKOD. |
| SUCCESS_SMS_SENT | Textmeddelande skickat | För SMS-meddelanden och skickades den SMS med engångslösenord (OTP).  Användare anger OTP eller OTP + PIN-koden i programmet för att slutföra autentiseringen. |
| SUCCESS_PHONE_APP_AUTHENTICATED | Autentiserat via Mobilapp | Den användare som autentiserats via den mobila appen. |
| SUCCESS_OATH_CODE_PENDING | Väntande OATH-kod | Användaren uppmanades sina OATH-kod men svarade inte. |
| SUCCESS_OATH_CODE_VERIFIED | OATH-kod verifierad | Användaren har angett en giltig OATH-kod när du tillfrågas. |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | Reserv-OATH-kod verifierad | Användaren var nekas autentisering med hjälp av deras primära Multi-Factor Authentication-metoden och därefter får en giltig OATH-kod för återställning. |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | Reservsäkerhetsfrågor besvarade | Användaren nekades autentisering med hjälp av deras primära Multi-Factor Authentication-metoden och besvarat sina säkerhetsfrågor som reserv korrekt. |
| FAILED_PHONE_BUSY | Autentisering pågår redan | Multifaktorautentisering är redan igång en autentisering för den här användaren.  Detta beror ofta på RADIUS-klienter som skickar flera begäranden om autentisering under den samma inloggning. |
| CONFIG_ISSUE | Telefon kan inte nås | Anrop gjordes, men något gick inte att placera eller inte besvarades.  Detta inkluderar upptaget, snabb upptaget (frånkopplad), tre-toner (tal inte längre i tjänsten) nådde sin tidsgräns medan ska ringa, osv. |
| FAILED_INVALID_PHONENUMBER | Ogiltigt telefonnummerformat | Telefonnumret har ett ogiltigt format.  Telefonnummer måste anges i siffror och måste innehålla 10 siffror för landskod + 1 (USA och Kanada). |
| FAILED_USER_HUNGUP_ON_US | Användaren lade på | Användaren få svar på telefonen men sedan lade på utan att trycka på valfri knapp. |
| FAILED_INVALID_EXTENSION | Ogiltig anknytning | Tillägget innehåller ogiltiga tecken.  Endast siffror, kommatecken, *, och # är tillåtna.  Ett @-prefix kan även användas. |
| FAILED_FRAUD_CODE_ENTERED | Bedrägerikod angiven | Användaren valde att rapportera bedrägeri under samtalet vilket resulterar i nekas autentiseringen och ett telefonnummer som blockerade.| 
| FAILED_SERVER_ERROR | Det går inte att ringa | Det gick inte att ringa Multi-Factor Authentication-tjänsten. |
| FAILED_SMS_NOT_SENT | Det gick inte att skicka textmeddelande | SMS-meddelandet kunde inte skickas.  Autentiseringen nekades. |
| FAILED_SMS_OTP_INCORRECT | Textmeddelandets OTP felaktigt | Användaren har angett en felaktig engångskod (OTP) från SMS-meddelandet som de togs emot.  Autentiseringen nekades. |
| FAILED_SMS_OTP_PIN_INCORRECT | Engångskod för textmeddelande + PIN felaktiga | Användaren har angett en felaktig engångskod (OTP) och/eller en felaktig användarens PIN-kod.  Autentiseringen nekades. |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | Överskred maximalt Text Message OTP försök | Användaren har överskridit det maximala antalet försök för engångslösenord (OTP). |
| FAILED_PHONE_APP_DENIED | Mobilapp Avvisad | Användaren nekade autentiseringen i appen genom att trycka på Neka. |
| FAILED_PHONE_APP_INVALID_PIN | Mobilapp ogiltig PIN | Användaren har angett en ogiltig PIN-kod vid autentisering i appen. |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | Mobilappen PIN inte ändrad | Användaren slutfördes inte har en obligatorisk ändra PIN-kod i mobilappen. |
| FAILED_FRAUD_REPORTED | Bedrägeri rapporterat | Användarrapporterat bedrägeri i mobilappen. |
| FAILED_PHONE_APP_NO_RESPONSE | Mobilapp inget svar | Användaren svarade inte på begäran för autentisering av mobilappen. |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | Alla Mobilappenheter har blockerats | Mobilappenheter för den här användaren inte längre svarar på meddelanden och har blockerats. |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | Mobilappavisering misslyckades | Ett fel uppstod vid försök att skicka ett meddelande till mobilappen på användarens enhet. |
| FAILED_PHONE_APP_INVALID_RESULT | Mobilapp ogiltigt resultat | Mobilappen returnerade ett ogiltigt resultat. |
| FAILED_OATH_CODE_INCORRECT | Felaktig OATH-kod | Användaren har angett en felaktig OATH-kod.  Autentiseringen nekades. |
| FAILED_OATH_CODE_PIN_INCORRECT | OATH-kod + PIN inkorrekt | Användaren har angett en felaktig OATH-kod och/eller en felaktig användarens PIN-kod.  Autentiseringen nekades. |
| FAILED_OATH_CODE_DUPLICATE | Dubbel OATH-kod | Användaren har angett en OATH-kod som användes tidigare.  Autentiseringen nekades. |
| FAILED_OATH_CODE_OLD | OATH-koden är inaktuell | Användaren har angett en OATH-kod som efterföljs av en OATH-kod som användes tidigare.  Autentiseringen nekades. |
| FAILED_OATH_TOKEN_TIMEOUT | OATH-Kodresultat | Användaren tog för lång tid att ange OATH-koden och Multi-Factor Authentication försöket hade redan nådde tidsgränsen. |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | Säkerhetsfrågeresultat | Användaren tog för lång tid att ange svar på säkerhetsfrågor och Multi-Factor Authentication försöket hade redan nådde tidsgränsen. |
| FAILED_AUTH_RESULT_TIMEOUT | Autentiseringsresultat | Användaren tog för lång tid att slutföra Multifaktorautentisering försöket. |
| FAILED_AUTHENTICATION_THROTTLED | Autentisering begränsas | Multi-Factor Authentication försöket begränsades av tjänsten. |

## <a name="next-steps"></a>Nästa steg

* [För användare](../user-help/multi-factor-authentication-end-user.md)
* [Var du vill distribuera](concept-mfa-whichversion.md)
