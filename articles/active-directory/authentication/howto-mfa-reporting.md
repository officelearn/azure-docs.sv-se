---
title: Åtkomst- och användningsrapporter för Azure MFA - Azure Active Directory
description: Här beskrivs hur du använder Azure Multi-Factor Authentication-funktionen - rapporter.
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
ms.openlocfilehash: c5d5354f5bca7a4c9ab00066167ad19890536629
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653608"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Rapporter i Azure Multi Factor-autentisering

Azure Multi-Factor Authentication innehåller flera rapporter som kan användas av dig och din organisation som är tillgängliga via Azure-portalen. I följande tabell visas tillgängliga rapporter:

| Rapport | Location | Beskrivning |
|:--- |:--- |:--- |
| Blockerad användarhistorik | Azure AD > Security > MFA > Blockera/avblockera användare | Visar historiken för begäranden om att blockera eller häva blockeringen av användare. |
| Användnings- och bedrägerivarningar | Azure AD > inloggningar | Innehåller information om övergripande användning, användarsammanfattning och användarinformation. samt en historik över bedrägerivarningar som lämnats in under det angivna datumintervallet. |
| Användning för lokala komponenter | Azure AD > > MFA-> aktivitetsrapport | Innehåller information om övergripande användning för MFA via NPS-tillägget, ADFS och MFA-servern. |
| Förbigående användarhistorik | Azure AD > Security > MFA > Engångsföregång | Innehåller en historik över begäranden om att kringgå multifaktorautentisering för en användare. |
| Serverstatus | Azure AD > Security > MFA > Server status | Visar status för multifaktorautentiseringsservrar som är associerade med ditt konto. |

## <a name="view-mfa-reports"></a>Visa MFA-rapporter

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Till vänster väljer du **Azure Active Directory** > **Security** > **MFA**.
3. Välj den rapport som du vill visa.

   ![Statusrapport för MFA-serverserver i Azure-portalen](./media/howto-mfa-reporting/report.png)

## <a name="azure-ad-sign-ins-report"></a>Azure AD-inloggningsrapport

Med **aktivitetsrapporten för inloggningar i** [Azure-portalen](https://portal.azure.com)kan du få den information du behöver för att avgöra hur det går för din miljö.

Inloggningsrapporten kan ge dig information om användningen av hanterade program och användarinloggningsaktiviteter, vilket innehåller information om MFA-användning (Multi Factor Authentication). MFA-data ger dig information om hur MFA fungerar i din organisation. Du får hjälp att besvara frågor som:

- Kontrollerades inloggningen med MFA?
- Hur slutförde användaren MFA?
- Varför kunde användaren inte slutföra MFA?
- Hur många användare kontrolleras av MFA?
- Hur många användare kan inte slutföra MFA-kontrollen?
- Vilka vanliga MFA-relaterade problem råkar slutanvändarna ut för?

Dessa data är tillgängliga via [Azure-portalen](https://portal.azure.com) och [rapporterings-API: et](../reports-monitoring/concept-reporting-api.md).

![Azure AD-inloggningsrapport i Azure-portalen](./media/howto-mfa-reporting/sign-in-report.png)

### <a name="sign-ins-report-structure"></a>Rapportstruktur för inloggningar

Rapporterna om inloggningsaktiviteter för MFA ger dig åtkomst till följande information:

**MFA krävs:** Om MFA krävs för inloggning eller inte. MFA kan krävas på grund av MFA, villkorlig åtkomst eller andra orsaker. Möjliga värden är **Ja** eller **Nej**.

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
      - inte kan nå användarens telefon
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

**MFA-autentiseringsmetod:** Autentiseringsmetoden som användaren använde för att slutföra MFA. Möjliga värden inkluderar:

- Textmeddelande
- Meddelanden via mobilapp
- Telefonsamtal (autentiseringstelefon)
- Verifieringskod från mobilapp
- Telefonsamtal (kontorstelefon)
- Telefonsamtal (alternativ autentiseringstelefon)

**Information om MFA-autentisering:**: Delvis gömd version av telefonnumret, till exempel: + X XXXXXXXX64.

**Villkorlig åtkomst** Hitta information om principer för villkorlig åtkomst som påverkade inloggningsförsöket, inklusive:

- Principnamn
- Bevilja kontroller
- Sessionskontroller
- Resultat

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>PowerShell-rapportering om användare som är registrerade för MFA

Kontrollera först att du har [MSOnline V1 PowerShell-modulen](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0) installerad.

Identifiera användare som har registrerat sig för MFA med hjälp av PowerShell som följer. Den här uppsättningen kommandon utesluter inaktiverade användare eftersom dessa konton inte kan autentisera mot Azure AD.

```Get-MsolUser -All | Where-Object {$.StrongAuthenticationMethods -ne $null -and $.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName```

Identifiera användare som inte har registrerat sig för MFA med hjälp av PowerShell som följer. Den här uppsättningen kommandon utesluter inaktiverade användare eftersom dessa konton inte kan autentisera mot Azure AD.

```Get-MsolUser -All | Where-Object {$.StrongAuthenticationMethods.Count -eq 0 -and $.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName```

Identifiera användare och produktionsmetoder registrerade. 

```PowerShell
Get-MsolUser -All | Select-Object @{N='UserPrincipalName';E={$_.UserPrincipalName}},

@{N='MFA Status';E={if ($_.StrongAuthenticationRequirements.State){$_.StrongAuthenticationRequirements.State} else {"Disabled"}}},

@{N='MFA Methods';E={$_.StrongAuthenticationMethods.methodtype}} | Export-Csv -Path c:\MFA_Report.csv -NoTypeInformation
```

## <a name="possible-results-in-activity-reports"></a>Möjliga resultat i aktivitetsrapporter

Följande tabell kan användas för att felsöka multifaktorautentisering med den nedladdade versionen av aktivitetsrapporten för multifaktorautentisering. De visas inte direkt i Azure-portalen.

| Samtalsresultat | Beskrivning | Bred beskrivning |
| --- | --- | --- |
| SUCCESS_WITH_PIN | Vald PIN-kod | Användaren angav en PIN-kod. Om autentiseringen lyckades angav de rätt PIN-kod. Om autentisering nekas har de angett en felaktig PIN-kod eller så är användaren inställd på standardläge. |
| SUCCESS_NO_PIN | Endast # Inmatad | Om användaren är inställd på PIN-kod och autentiseringen nekas, betyder det att användaren inte angav sin PIN-kod och endast angav #.  Om användaren är inställd på standardläge och autentiseringen lyckas innebär det att användaren bara angett # vilket är rätt sak att göra i standardläge. |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | # Inte tryckt efter inträde | Användaren har inte skickat några DTMF-siffror eftersom # inte angavs. Andra angivna siffror skickas inte om inte # anges som anger att transaktionen är slutförd. |
|SUCCESS_NO_PIN_BUT_TIMEOUT | Ingen telefoninmatning - timed out | Samtalet besvarades, men det fanns inget svar. Detta indikerar vanligtvis att samtalet plockades upp med röstbrevlåda. |
| SUCCESS_PIN_EXPIRED | PIN-koden har upphört att gälla och har inte ändrats | Användarens PIN-kod har upphört att gälla och de uppmanades att ändra den, men PIN-koden slutfördes inte. |
| SUCCESS_USED_CACHE | Använd cache | Autentiseringen lyckades utan ett multifaktorautentiseringsanrop sedan en tidigare lyckad autentisering för samma användarnamn inträffade inom den konfigurerade cachetidsramen. |
| SUCCESS_BYPASSED_AUTH | Förbi Auth | Autentiseringen lyckades med hjälp av en engångsföregång som initierats för användaren. Mer information om förbikopplingen finns i rapporten Förbi användarhistorik. |
| SUCCESS_USED_IP_BASED_CACHE | Använd IP-baserad cache | Autentiseringen lyckades utan ett multifaktorautentiseringsanrop sedan en tidigare lyckad autentisering för samma användarnamn, autentiseringstyp, programnamn och IP inträffade inom den konfigurerade cachetidsramen. |
| SUCCESS_USED_APP_BASED_CACHE | Använd appbaserad cache | Autentiseringen lyckades utan ett multifaktorautentiseringsanrop sedan en tidigare lyckad autentisering för samma användarnamn, autentiseringstyp och programnamn inom den konfigurerade cachetidsramen. |
| SUCCESS_INVALID_INPUT | Ogiltig telefoninmatning | Svaret som skickas från telefonen är ogiltigt. Detta kan vara från en fax eller modem eller användaren kan ha angett * som en del av sin PIN-kod. |
| SUCCESS_USER_BLOCKED | Användaren är blockerad | Användarens telefonnummer är blockerat. Ett blockerat nummer kan initieras av användaren under ett autentiseringsanrop eller av en administratör som använder Azure-portalen. <br> Ett blockerat nummer är också en biprodukt av en bedrägerivarning. |
| SUCCESS_SMS_AUTHENTICATED | Autentiserat textmeddelande | För tvåvägstestmeddelande svarade användaren korrekt med sin engångslösenord (OTP) eller OTP + PIN. |
| SUCCESS_SMS_SENT | Sms:et skickat | För SMS skickades sms:et med engångslösenordet. Användaren kommer att ange OTP eller OTP + PIN i programmet för att slutföra autentiseringen. |
| SUCCESS_PHONE_APP_AUTHENTICATED | Mobilapp autentiserat | Användaren har autentiserats via mobilappen. |
| SUCCESS_OATH_CODE_PENDING | OATH-kod väntar | Användaren blev tillfrågad om sin OATH-kod men svarade inte. |
| SUCCESS_OATH_CODE_VERIFIED | OATH-kod verifierad | Användaren angav en giltig OATH-kod när den uppmanas. |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | ED-kod för återgång verifierad | Användaren nekades autentisering med sin primära multifaktorautentiseringsmetod och tillhandahöll sedan en giltig OATH-kod för reserv. |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | Besvarade säkerhetsfrågor | Användaren nekades autentisering med sin primära multifaktorautentiseringsmetod och svarade sedan på sina säkerhetsfrågor korrekt för reserv. |
| FAILED_PHONE_BUSY | Redan pågående | Multifaktorautentisering bearbetar redan en autentisering för den här användaren. Detta orsakas ofta av RADIUS-klienter som skickar flera autentiseringsbegäranden under samma inloggning. |
| CONFIG_ISSUE | Telefonen kan inte nås | Samtalet gjordes, men antingen kunde inte placeras eller besvarades inte. Detta inkluderar upptagen signal, snabb upptagen signal (frånkopplad), tri-toner (nummer inte längre i drift), timed out medan ringningar, etc. |
| FAILED_INVALID_PHONENUMBER | Ogiltigt telefonnummerformat | Telefonnumret har ett ogiltigt format. Telefonnummer måste vara numeriska och måste vara 10 siffror för landskod +1 (USA & Kanada). |
| FAILED_USER_HUNGUP_ON_US | Användaren lade på luren | Användaren svarade i telefonen, men lade sedan på utan att trycka på några knappar. |
| FAILED_INVALID_EXTENSION | Ogiltigt tillägg | Tillägget innehåller ogiltiga tecken. Endast siffror, kommatecken, *, och # är tillåtna. Ett @ prefix kan också användas. |
| FAILED_FRAUD_CODE_ENTERED | Bedrägerikod har angetts | Användaren valde att rapportera bedrägeri under samtalet vilket resulterade i en nekad autentisering och ett blockerat telefonnummer.| 
| FAILED_SERVER_ERROR | Det går inte att ringa | Tjänsten Multifaktorautentisering kunde inte ringa samtalet. |
| FAILED_SMS_NOT_SENT | Det gick inte att skicka sms | Det gick inte att skicka sms:et. Autentiseringen nekas. |
| FAILED_SMS_OTP_INCORRECT | Felaktigt felmeddelande | Användaren angav en felaktig engångslösenord (OTP) från sms:et de fick. Autentiseringen nekas. |
| FAILED_SMS_OTP_PIN_INCORRECT | Felaktig islagning av sms - felaktigt i pinkoden | Användaren angav en felaktig engångslösenord (OTP) och/eller en felaktig användar-PIN.The user entered an incorrect one-time passcode (OTP) and/or an incorrect user PIN. Autentiseringen nekas. |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | Överskred maximalt antal otp-försök för sms | Användaren har överskridit det maximala antalet engångslösenordsförsök.The user has exceeded the maximum number of one-time passcode (OTP) attempts. |
| FAILED_PHONE_APP_DENIED | Mobil app nekad | Användaren nekade autentiseringen i mobilappen genom att trycka på knappen Neka. |
| FAILED_PHONE_APP_INVALID_PIN | Ogiltig PIN-kod för mobilapp | Användaren angav en ogiltig PIN-kod när den autentiserades i mobilappen. |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | PIN-koden för mobilappen har inte ändrats | Användaren slutförde inte en nödvändig PIN-ändring i mobilappen. |
| FAILED_FRAUD_REPORTED | Rapporterade bedrägerier | Användaren rapporterade bedrägeri i mobilappen. |
| FAILED_PHONE_APP_NO_RESPONSE | Inget svar i mobilappen | Användaren svarade inte på begäran om autentisering av mobilappar. |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | Mobilapp alla enheter blockerade | Mobilappsenheterna för den här användaren svarar inte längre på aviseringar och har blockerats. |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | Det gick inte att meddela mobilappen | Ett fel uppstod när ett meddelande skulle skickas till mobilappen på användarens enhet. |
| FAILED_PHONE_APP_INVALID_RESULT | Ogiltigt resultat för mobilappen | Mobilappen returnerade ett ogiltigt resultat. |
| FAILED_OATH_CODE_INCORRECT | OATH-kod felaktig | Användaren angav en felaktig OATH-kod.  Autentiseringen nekas. |
| FAILED_OATH_CODE_PIN_INCORRECT | OATH-kod + felaktig PIN-kod | Användaren angav en felaktig OATH-kod och/eller en felaktig användar-PIN-kod.  Autentiseringen nekas. |
| FAILED_OATH_CODE_DUPLICATE | Dubblett ED-kod | Användaren angav en OATH-kod som tidigare användes.  Autentiseringen nekas. |
| FAILED_OATH_CODE_OLD | OATH-koden inaktuella | Användaren angav en OATH-kod som föregår en OATH-kod som tidigare användes.  Autentiseringen nekas. |
| FAILED_OATH_TOKEN_TIMEOUT | TIMEOUT för OATH-kodresultat | Användaren tog för lång tid på sig att ange OATH-koden och multifaktorautentiseringsförsöket hade redan tagit time out. |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | Tidsgränsen för säkerhetsfrågor resultat | Användaren tog för lång tid att ange svar på säkerhetsfrågor och multifaktorautentiseringsförsöket hade redan tagit time out. |
| FAILED_AUTH_RESULT_TIMEOUT | Timeout för auth-resultat | Det tog för lång tid för användaren att slutföra multifaktorautentiseringsförsöket. |
| FAILED_AUTHENTICATION_THROTTLED | Begränsa autentisering | Multifaktorautentiseringsförsöket begränsades av tjänsten. |

## <a name="next-steps"></a>Nästa steg

* [SSPR och MFA användning och insikter rapportering](howto-authentication-methods-usage-insights.md)
* [För användare](../user-help/multi-factor-authentication-end-user.md)
* [Var du ska distribuera](concept-mfa-whichversion.md)
