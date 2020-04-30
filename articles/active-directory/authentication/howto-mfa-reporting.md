---
title: Åtkomst-och användnings rapporter för Azure MFA – Azure Active Directory
description: Här beskrivs hur du använder Azure Multi-Factor Authentication Feature-Reports.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/30/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2df562d65ad064efb1be337e0b68cb8638536981
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82112770"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Rapporter i Azure Multi-Factor Authentication

Azure Multi-Factor Authentication innehåller flera rapporter som du och din organisation kan använda för att komma åt via Azure Portal. I följande tabell visas tillgängliga rapporter:

| Rapport | Plats | Beskrivning |
|:--- |:--- |:--- |
| Blockerad användar historik | Azure AD >-säkerhet > MFA > blockera/avblockera användare | Visar historiken för förfrågningar om att blockera eller avblockera användare. |
| Användnings-och bedrägeri varningar | Inloggnings program för Azure AD > | Innehåller information om allmän användning, användar Sammanfattning och användar information. samt en historik över bedrägeri aviseringar som skickats under det angivna datum intervallet. |
| Användning för lokala komponenter | Azure AD > säkerhets > MFA > aktivitets rapport | Innehåller information om den övergripande användningen av MFA via NPS-tillägget, ADFS-och MFA-servern. |
| Förhoppad användar historik | Azure AD >-säkerhet > MFA-> eng ång slö tiden | Innehåller en historik över förfrågningar om att kringgå Multi-Factor Authentication för en användare. |
| Server status | Azure AD > säkerhets > MFA > Server status | Visar statusen för Multi-Factor Authentication servrar som är kopplade till ditt konto. |

## <a name="view-mfa-reports"></a>Visa MFA-rapporter

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Till vänster väljer du **Azure Active Directory** > **säkerhets** > -**MFA**.
3. Välj den rapport som du vill visa.

   ![Status rapport för MFA Server-Server i Azure Portal](./media/howto-mfa-reporting/report.png)

## <a name="azure-ad-sign-ins-report"></a>Inloggnings rapport för Azure AD

Med **rapporten inloggnings aktiviteter** i [Azure Portal](https://portal.azure.com)kan du få den information som du behöver för att avgöra hur din miljö fungerar.

Inloggnings rapporten kan ge dig information om användningen av hanterade program och användar inloggnings aktiviteter, som innehåller information om Multi-Factor Authentication (MFA) användning. MFA-data ger dig information om hur MFA fungerar i din organisation. Du får hjälp att besvara frågor som:

- Kontrollerades inloggningen med MFA?
- Hur slutförde användaren MFA?
- Varför kunde användaren inte slutföra MFA?
- Hur många användare kontrolleras av MFA?
- Hur många användare kan inte slutföra MFA-kontrollen?
- Vilka vanliga MFA-relaterade problem råkar slutanvändarna ut för?

Dessa data är tillgängliga via [Azure Portal](https://portal.azure.com) och [rapporterings-API: et](../reports-monitoring/concept-reporting-api.md).

![Rapporten Azure AD-inloggningar i Azure Portal](./media/howto-mfa-reporting/sign-in-report.png)

### <a name="sign-ins-report-structure"></a>Rapport struktur för inloggningar

Rapporterna om inloggningsaktiviteter för MFA ger dig åtkomst till följande information:

**MFA krävs:** Om MFA krävs för inloggning eller inte. MFA kan krävas på grund av MFA per användare, villkorlig åtkomst eller andra orsaker. Möjliga värden är **Ja** eller **Nej**.

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
      - Det gick inte att komma åt användarens telefon
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

**MFA-autentiseringsmetod:** Autentiseringsmetoden som användaren använde för att slutföra MFA. Möjliga värden är:

- Textmeddelande
- Meddelanden via mobilapp
- Telefonsamtal (autentiseringstelefon)
- Verifieringskod från mobilapp
- Telefonsamtal (kontorstelefon)
- Telefonsamtal (alternativ autentiseringstelefon)

**Information om MFA-autentisering:**: Delvis gömd version av telefonnumret, till exempel: + X XXXXXXXX64.

**Villkorlig åtkomst** Hitta information om principer för villkorlig åtkomst som påverkat inloggnings försöket, inklusive:

- Principnamn
- Bevilja kontroller
- Kontroller av sessioner
- Resultat

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>PowerShell-rapportering för användare som registrerats för MFA

Se först till att du har installerat [MSOnline v1 PowerShell-modulen](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0) .

Identifiera användare som har registrerat sig för MFA med hjälp av PowerShell som följer. Den här uppsättningen kommandon utesluter inaktiverade användare eftersom dessa konton inte kan autentisera mot Azure AD.

```powershell
Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods -ne $null -and $_.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName
```

Identifiera användare som inte har registrerat sig för MFA med hjälp av PowerShell som följer. Den här uppsättningen kommandon utesluter inaktiverade användare eftersom dessa konton inte kan autentisera mot Azure AD.

```powershell
Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods.Count -eq 0 -and $_.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName
```

Identifiera användare och utmatnings metoder som registrerats. 

```powershell
Get-MsolUser -All | Select-Object @{N='UserPrincipalName';E={$_.UserPrincipalName}},

@{N='MFA Status';E={if ($_.StrongAuthenticationRequirements.State){$_.StrongAuthenticationRequirements.State} else {"Disabled"}}},

@{N='MFA Methods';E={$_.StrongAuthenticationMethods.methodtype}} | Export-Csv -Path c:\MFA_Report.csv -NoTypeInformation
```

## <a name="possible-results-in-activity-reports"></a>Möjliga resultat i aktivitets rapporter

Följande tabell kan användas för att felsöka Multi-Factor Authentication med den nedladdade versionen av Multi-Factor Authentication-aktivitets rapporten. De visas inte direkt i Azure Portal.

| Anrops resultat | Beskrivning | Bred beskrivning |
| --- | --- | --- |
| SUCCESS_WITH_PIN | PIN-kod angiven | Användaren angav en PIN-kod. Om autentiseringen lyckades angavs rätt PIN-kod. Om autentisering nekas anges en felaktig PIN-kod eller användaren är inställd på standard läge. |
| SUCCESS_NO_PIN | Endast antal angivna | Om användaren är inställd på PIN-läge och autentiseringen nekas innebär det att användaren inte angav sin PIN-kod och bara angav #.  Om användaren är inställd på standard läge och autentiseringen lyckas innebär det att användaren bara angav # vilket är rätt att göra i standard läge. |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | # Har inte tryckts ned efter posten | Användaren skickade inga DTMF-siffror eftersom # inte angavs. Andra siffror som anges skickas inte om # anges i slutet av posten. |
|SUCCESS_NO_PIN_BUT_TIMEOUT | Ingen telefonin text-tids gränsen uppnåddes | Anropet besvarades, men det fanns inget svar. Detta indikerar vanligt vis att samtalet hämtades av röst brev. |
| SUCCESS_PIN_EXPIRED | PIN-koden har upphört och har inte ändrats | Användarens PIN-kod har upphört att gälla och du uppmanas att ändra den, men PIN-koden har ändrats. |
| SUCCESS_USED_CACHE | Använt cacheminne | Autentiseringen lyckades utan ett Multi-Factor Authentication-anrop eftersom en tidigare lyckad autentisering för samma användar namn inträffade inom den konfigurerade cache-tidsramen. |
| SUCCESS_BYPASSED_AUTH | Förhoppad autentisering | Autentiseringen lyckades med en eng ång slö fördröjning för användaren. Se rapporten över ignorerade användar historik för mer information om att kringgå. |
| SUCCESS_USED_IP_BASED_CACHE | Använd IP-baserad cache | Autentiseringen lyckades utan ett Multi-Factor Authentication-anrop sedan en tidigare lyckad autentisering för samma användar namn, autentiseringstyp, program namn och IP inträffade inom den konfigurerade cache-tidsramen. |
| SUCCESS_USED_APP_BASED_CACHE | Använd app-baserad cache | Autentiseringen lyckades utan ett Multi-Factor Authentication-anrop sedan en tidigare lyckad autentisering för samma användar namn, autentiseringstyp och program namn inom den konfigurerade cache-tidsramen. |
| SUCCESS_INVALID_INPUT | Ogiltig telefonin ingång | Svaret som skickas från telefonen är inte giltigt. Detta kan vara från en fax maskin eller ett modem, eller så kan användaren ha angett * som en del av PIN-koden. |
| SUCCESS_USER_BLOCKED | Användaren är blockerad | Användarens telefonnummer är blockerat. Ett blockerat nummer kan initieras av användaren under ett autentiserings anrop eller av en administratör med hjälp av Azure Portal. <br> Obs: ett blockerat nummer är också en byproduct av en bedrägeri avisering. |
| SUCCESS_SMS_AUTHENTICATED | Textmeddelande autentiserat | För tvåvägs test meddelande svarar användaren korrekt med eng ång slö sen ord (eng ång slö sen ord) eller eng ång slö sen ord + PIN-kod. |
| SUCCESS_SMS_SENT | Textmeddelande har skickats | SMS-meddelandet som innehåller eng ång slö sen ordet (eng ång slö sen ord) har skickats. Användaren kommer att ange eng ång slö sen ord eller eng ång slö sen ord i programmet för att slutföra autentiseringen. |
| SUCCESS_PHONE_APP_AUTHENTICATED | Mobilapp autentiserad | Användaren har autentiserats via mobilappen. |
| SUCCESS_OATH_CODE_PENDING | Väntande OATH-kod | Användaren uppmanades att ange sin OATH-kod men svarade inte. |
| SUCCESS_OATH_CODE_VERIFIED | OATH-kod verifierad | Användaren angav en giltig OATH-kod när den uppmanas till det. |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | Återställnings-OATH-kod verifierad | Användaren nekades autentisering med hjälp av den primära Multi-Factor Authentication metoden och angav sedan en giltig OATH-kod för återställning. |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | Svar på reserv säkerhets frågor | Användaren nekades autentisering med hjälp av den primära Multi-Factor Authentication metoden och besvarade sedan sina säkerhets frågor korrekt för reserven. |
| FAILED_PHONE_BUSY | Autentisering pågår redan | Multi-Factor Authentication bearbetar redan en autentisering för den här användaren. Detta orsakas ofta av RADIUS-klienter som skickar flera autentiseringsbegäranden vid samma inloggning. |
| CONFIG_ISSUE | Det går inte att komma åt telefonen | Anropet gjordes, men kunde inte placeras eller besvarades inte. Detta omfattar upptaget signal, snabb, upptaget signal (frånkopplad), tre toner (antalet finns inte längre i tjänsten), tids gränsen nåddes vid ringning osv. |
| FAILED_INVALID_PHONENUMBER | Ogiltigt telefonnummer format | Telefonnumret har ett ogiltigt format. Telefonnummer måste vara numeriska och måste vara 10 siffror för lands kod + 1 (USA & Kanada). |
| FAILED_USER_HUNGUP_ON_US | Användaren lade på telefonen | Användaren besvarade telefonen, men stannade sedan utan att trycka på några knappar. |
| FAILED_INVALID_EXTENSION | Ogiltigt tillägg | Tillägget innehåller ogiltiga tecken. Endast siffror, kommatecken, * och # tillåts. Ett @-prefix kan också användas. |
| FAILED_FRAUD_CODE_ENTERED | Bedrägeri kod har angetts | Användaren valde att rapportera bedrägerier under anropet, vilket resulterade i en nekad autentisering och ett blockerat telefonnummer.| 
| FAILED_SERVER_ERROR | Det gick inte att ringa | Det gick inte att placera anropet i Multi-Factor Authentications tjänsten. |
| FAILED_SMS_NOT_SENT | Det gick inte att skicka textmeddelande | Det gick inte att skicka textmeddelandet. Autentiseringen nekas. |
| FAILED_SMS_OTP_INCORRECT | Felaktigt eng ång slö sen ord | Användaren angav ett felaktigt lösen ord (eng ång slö sen ord) från det textmeddelande som de fick. Autentiseringen nekas. |
| FAILED_SMS_OTP_PIN_INCORRECT | Textmeddelandets eng ång slö sen ord + PIN-kod | Användaren angav ett felaktigt lösen ord (eng ång slö sen ord) och/eller en felaktig PIN-kod för användaren. Autentiseringen nekas. |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | Max antal försök för eng ång slö sen ord | Användaren har överskridit det maximala antalet eng ång slö sen ord (eng ång slö sen ord). |
| FAILED_PHONE_APP_DENIED | Nekad mobilapp | Användaren nekade autentiseringen i mobilappen genom att trycka på neka-knappen. |
| FAILED_PHONE_APP_INVALID_PIN | Ogiltig PIN-kod för mobilapp | Användaren angav en ogiltig PIN-kod vid autentisering i mobilappen. |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | Mobil Programets PIN-kod har inte ändrats | Användaren kunde inte slutföra en nödvändig PIN-ändring i mobilappen. |
| FAILED_FRAUD_REPORTED | Bedrägeri har rapporter ATS | Användaren rapporterade bedrägerier i mobilappen. |
| FAILED_PHONE_APP_NO_RESPONSE | Mobilapp saknar svar | Användaren svarade inte på autentiseringsbegäran för mobilappen. |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | Alla enheter har blockerats av mobilappen | Mobile App-enheter för den här användaren svarar inte längre på meddelanden och har blockerats. |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | Avisering om mobilapp misslyckades | Ett fel uppstod vid försök att skicka ett meddelande till mobilappen på användarens enhet. |
| FAILED_PHONE_APP_INVALID_RESULT | Ogiltigt resultat för mobilapp | Mobilappen returnerade ett ogiltigt resultat. |
| FAILED_OATH_CODE_INCORRECT | Felaktig OATH-kod | Användaren angav en felaktig OATH-kod.  Autentiseringen nekas. |
| FAILED_OATH_CODE_PIN_INCORRECT | OATH-kod + PIN-kod felaktigt | Användaren angav en felaktig OATH-kod och/eller en felaktig PIN-kod för användaren.  Autentiseringen nekas. |
| FAILED_OATH_CODE_DUPLICATE | Duplicera OATH-kod | Användaren angav en OATH-kod som tidigare har använts.  Autentiseringen nekas. |
| FAILED_OATH_CODE_OLD | OATH-koden är inaktuell | Användaren angav en OATH-kod som föregår en OATH-kod som tidigare har använts.  Autentiseringen nekas. |
| FAILED_OATH_TOKEN_TIMEOUT | Timeout för OATH-kods resultat | Användaren tog för lång tid att ange OATH-koden och Multi-Factor Authentication försök hade redan nått tids gränsen. |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | Timeout för säkerhets frågor | Användaren tog för lång tid att ange svar på säkerhets frågor och det Multi-Factor Authentication försöket hade redan nått tids gränsen. |
| FAILED_AUTH_RESULT_TIMEOUT | Timeout för autentiserings resultat | Användaren tog för lång tid att slutföra Multi-Factor Authentication försöket. |
| FAILED_AUTHENTICATION_THROTTLED | Begränsad autentisering | Multi-Factor Authentication-försöket begränsades av tjänsten. |

## <a name="next-steps"></a>Nästa steg

* [SSPR och MFA-användning och insikts rapportering](howto-authentication-methods-usage-insights.md)
* [För användare](../user-help/multi-factor-authentication-end-user.md)
* [Distributions plats](concept-mfa-whichversion.md)
