---
title: Simulera riskidentifieringar i Azure AD Identity Protection
description: Lär dig hur du simulerar riskidentifieringar i Identitetsskydd
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: e50e406e74c0b78f41830c4ea7c5b10830002ed3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72886940"
---
# <a name="simulating-risk-detections-in-identity-protection"></a>Simulera riskidentifieringar i identitetsskydd

Administratörer kanske vill simulera risker i sin miljö för att utföra följande objekt:

- Fylla i data i identitetsskyddsmiljön genom att simulera riskidentifieringar och sårbarheter.
- Ställ in riskbaserade principer för villkorlig åtkomst och testa effekten av dessa principer.

Den här artikeln innehåller steg för att simulera följande typer av riskidentifiering:

- Anonym IP-adress (lätt)
- Okända inloggningsegenskaper (måttlig)
- Atypiska resor (svårt)

Andra riskidentifieringar kan inte simuleras på ett säkert sätt.

Mer information om varje riskdetektering finns i artikeln, [Vad är risk](concept-identity-protection-risks.md).

## <a name="anonymous-ip-address"></a>Anonym IP-adress

För att slutföra följande procedur måste du använda:

- [Tor-webbläsaren](https://www.torproject.org/projects/torbrowser.html.en) för att simulera anonyma IP-adresser. Du kan behöva använda en virtuell dator om organisationen begränsar användningen av Tor-webbläsaren.
- Ett testkonto som ännu inte har registrerats för Azure Multi-Factor Authentication.

**Så här simulerar du en inloggning från en anonym IP:s anvisningar:**

1. Med [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en)navigerar du till [https://myapps.microsoft.com](https://myapps.microsoft.com).   
2. Ange autentiseringsuppgifterna för det konto som du vill ska visas i rapporten **Inloggningar från anonyma IP-adresser.**

Inloggningen visas på instrumentpanelen för identitetsskydd inom 10- 15 minuter. 

## <a name="unfamiliar-sign-in-properties"></a>Okända inloggningsegenskaper

Om du vill simulera okända platser måste du logga in från en plats och enheten som ditt testkonto inte har loggat in från tidigare.

Proceduren nedan använder en nyskapade:

- VPN-anslutning, för att simulera ny plats.
- Virtuell dator, för att simulera en ny enhet.

För att slutföra följande procedur måste du använda ett användarkonto som har:

- Minst en 30-dagars inloggningshistorik.
- Azure Multi-Factor Autentisering aktiverat.

**Så här simulerar du en inloggning från en okänd plats:**

1. När du loggar in med ditt testkonto misslyckas MFA-utmaningen (Multi Factor Authentication) genom att inte klara MFA-utmaningen.
2. Med din nya VPN [https://myapps.microsoft.com](https://myapps.microsoft.com) navigerar du till och anger autentiseringsuppgifterna för ditt testkonto.

Inloggningen visas på instrumentpanelen för identitetsskydd inom 10- 15 minuter.

## <a name="atypical-travel"></a>Atypiska resor

Det är svårt att simulera det atypiska resetillståndet eftersom algoritmen använder maskininlärning för att såa ut falska positiva identifieringar som atypiska resor från välbekanta enheter eller inloggningar från VPN som används av andra användare i katalogen. Dessutom kräver algoritmen en inloggningshistorik på 14 dagar och 10 inloggningar av användaren innan den börjar generera riskidentifieringar. På grund av de komplexa maskininlärningsmodellerna och ovanstående regler finns det en risk för att följande steg inte leder till riskidentifiering. Du kanske vill replikera dessa steg för flera Azure AD-konton för att simulera den här identifieringen.

**Så här simulerar du en atypisk reseriskdetektering:**

1. Navigera till [https://myapps.microsoft.com](https://myapps.microsoft.com)med din vanliga webbläsare .  
2. Ange autentiseringsuppgifterna för det konto som du vill generera en atypisk reseriskidentifiering för.
3. Ändra din användaragent. Du kan ändra användaragenten i Microsoft Edge från Developer Tools (F12).
4. Ändra din IP-adress. Du kan ändra din IP-adress med hjälp av ett VPN-tillägg, ett Tor-tillägg eller skapa en ny virtuell dator i Azure i ett annat datacenter.
5. Logga in [https://myapps.microsoft.com](https://myapps.microsoft.com) på samma autentiseringsuppgifter som före och inom några minuter efter föregående inloggning.

Inloggningen visas på instrumentpanelen för identitetsskydd inom 2-4 timmar.

## <a name="testing-risk-policies"></a>Testa riskpolicyer

I det här avsnittet får du möjlighet att testa användaren och de inloggningsriskprinciper som skapas i artikeln [How To: Configure och enable riskprinciper](howto-identity-protection-configure-risk-policies.md).

### <a name="user-risk-policy"></a>Policy för användarrisk

Så här testar du en säkerhetsprincip för användarrisker:

1. Navigera till [Azure-portalen](https://portal.azure.com).
1. Bläddra till **Azure Active Directory** > **Security** > **Overview**.
1. Välj **Konfigurera användarriskprincip**.
   1. Under **Uppdrag**
      1. **Användare** – Välj **Alla användare** eller Välj personer **och grupper** om du begränsar distributionen.
         1. Du kan också välja att utesluta användare från principen.
      1. **Villkor** - **Användarrisk** Microsofts rekommendation är att ställa in det här alternativet till **Hög**.
   1. Under **Kontroller**
      1. **Access** - Microsofts rekommendation är att **tillåta åtkomst** och **kräv lösenordsändring**.
   1. **Tvinga bort principen** - **Off**
   1. **Spara** - Den här åtgärden kommer att gå tillbaka till **översiktssidan.**
1. Öka användarrisken för ett testkonto genom att till exempel simulera en av riskidentifieringarna några gånger.
1. Vänta några minuter och kontrollera sedan att risken har förhöjts för användaren. Om inte, simulera fler riskidentifieringar för användaren.
1. Återgå till din riskprincip och ange **Tvinga princip** **till På** och **Spara** din principändring.
1. Du kan nu testa användarriskbaserad villkorlig åtkomst genom att logga in med en användare med en förhöjd risknivå.

### <a name="sign-in-risk-security-policy"></a>Säkerhetspolicy för inloggningsrisk

Så här testar du en inloggningsriskprincip:

1. Navigera till [Azure-portalen](https://portal.azure.com).
1. Bläddra till **Azure Active Directory** > **Security** > **Overview**.
1. Välj **Konfigurera inloggningsriskprincip**.
   1. Under **Uppdrag**
      1. **Användare** – Välj **Alla användare** eller Välj personer **och grupper** om du begränsar distributionen.
         1. Du kan också välja att utesluta användare från principen.
      1. **Villkor** - **Inloggningsrisk** Microsofts rekommendation är att ställa in det här alternativet till **Medium och högre**.
   1. Under **Kontroller**
      1. **Access** - Microsofts rekommendation är att **tillåta åtkomst** och **kräva multifaktorautentisering**.
   1. **Framtvinga princip** - **på**
   1. **Spara** - Den här åtgärden kommer att gå tillbaka till **översiktssidan.**
1. Du kan nu testa inloggningsriskbaserad villkorlig åtkomst genom att logga in med hjälp av en riskfylld session (till exempel med hjälp av tor-webbläsaren). 

## <a name="next-steps"></a>Nästa steg

- [Vad är risk?](concept-identity-protection-risks.md)

- [Så här konfigurerar och aktiverar du riskprinciper](howto-identity-protection-configure-risk-policies.md)

- [Identitetsskydd för Azure Active Directory](overview-identity-protection.md)
