---
title: Rapporter för åtkomst och användning för Azure MFA | Microsoft Docs
description: Här beskrivs hur du använder funktionen för Azure Multi-Factor Authentication - rapporter.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: bb5a005ba553d6392bf1427a4c2bba9ac5aad191
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358674"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Rapporter i Azure Multi-Factor Authentication

Azure Multi-Factor Authentication innehåller flera rapporter som kan användas av dig och din organisation som är tillgängliga via Azure-portalen. I följande tabell visas de tillgängliga rapporterna:

| Rapport | Plats | Beskrivning |
|:--- |:--- |:--- |
| Blockeringshistorik för användare | Azure AD > MFA Server > blockera/avblockera användare | Visar historiken över förfrågningar om att blockera eller avblockera användare. |
| Användning och bedrägeri aviseringar | Azure AD > inloggningar | Innehåller information om totala användning, Användarsammanfattning och information om användare; samt en historik över bedrägerivarningar som skickats under det angivna datumintervallet. |
| Användningen av lokala komponenter | Azure AD > MFA-servern > Aktivitetsrapport | Innehåller information om totala användning för MFA via NPS-tillägget, ADFS, och MFA-servern. |
| Förbikopplingshistorik för användare | Azure AD > MFA Server > engångsförbikoppling | Innehåller en historik över förfrågningar om förbikoppling Multi-Factor Authentication för en användare. |
| Serverstatus | Azure AD > MFA Server > serverstatus | Visar status för multi-Factor Authentication-servrar som är associerade med ditt konto. |

## <a name="view-mfa-reports"></a>Visa rapporter i MFA

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster, Välj **Azure Active Directory** > **MFA Server**.
3. Välj den rapport som du vill visa.

   <center>![Moln](./media/howto-mfa-reporting/report.png)</center>

## <a name="azure-ad-sign-ins-report"></a>Rapporten med Azure AD-inloggningar

Med den **aktivitetsrapport för inloggningar** i den [Azure-portalen](https://portal.azure.com), du kan få den information du behöver för att fastställa hur din miljö.

Rapporten inloggningar kan ge dig med information om användningen av hanterade program och användare logga in aktiviteter, som innehåller information om användning av multifaktorautentisering (MFA). MFA-data ger dig information om hur MFA fungerar i din organisation. Du får hjälp att besvara frågor som:

- Kontrollerades inloggningen med MFA?
- Hur slutförde användaren MFA?
- Varför kunde användaren inte slutföra MFA?
- Hur många användare kontrolleras av MFA?
- Hur många användare kan inte slutföra MFA-kontrollen?
- Vilka vanliga MFA-relaterade problem råkar slutanvändarna ut för?

Informationen är tillgänglig via den [Azure-portalen](https://portal.azure.com) och [reporting API](../active-directory-reporting-api-getting-started-azure-portal.md).

![Molnet](./media/howto-mfa-reporting/sign-in-report.png)

### <a name="sign-ins-report-structure"></a>Inloggningar rapportens struktur

Rapporterna om inloggningsaktiviteter för MFA ger dig åtkomst till följande information:

**MFA krävs:** Om MFA krävs för inloggning eller inte. MFA kan krävas på grund av MFA per användare, villkorlig åtkomst eller andra orsaker. Möjliga värden är **Ja** eller **nr**.

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

**MFA-autentiseringsmetod:** Autentiseringsmetoden som användaren använde för att slutföra MFA. Möjliga värden omfattar:

- Textmeddelande
- Meddelanden via mobilapp
- Telefonsamtal (autentiseringstelefon)
- Verifieringskod från mobilapp
- Telefonsamtal (kontorstelefon)
- Telefonsamtal (alternativ autentiseringstelefon)

**Information om MFA-autentisering:**: Delvis gömd version av telefonnumret, till exempel: + X XXXXXXXX64.

**Villkorlig åtkomst** hitta information om principer för villkorlig åtkomst som påverkade inloggningsförsök, inklusive:

- Principnamn
- Bevilja kontroller
- Sessionskontroller
- Resultat

## <a name="powershell-reporting"></a>PowerShell-rapportering

Identifiera användare som har registrerats för MFA med hjälp av PowerShell som följer.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Identifiera användare som inte har registrerats för MFA med hjälp av PowerShell som följer.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="next-steps"></a>Nästa steg

* [För användare](../user-help/multi-factor-authentication-end-user.md)
* [Var du vill distribuera](concept-mfa-whichversion.md)
