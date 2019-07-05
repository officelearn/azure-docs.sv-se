---
title: Autentisering metoder användning och insikter reporting (förhandsversion) – Azure Active Directory
description: Rapportering om lösenord för självbetjäning i Azure AD lösenordsåterställning och Multifaktorautentisering autentisering metoden användning
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0f6a74308f1bc4a7b77576fb9f39f965de0a4f8
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561032"
---
# <a name="authentication-methods-usage--insights-preview"></a>Autentisering metoder användning och insights (förhandsversion)

Användning och insikter kan du förstå hur autentiseringsmetoder för funktioner som Azure Multi-Factor Authentication och återställning av lösenord fungerar i din organisation. Den här rapporteringsfunktionen ger din organisation att förstå vilka metoder registreras och hur de används.

## <a name="permissions-and-licenses"></a>Behörigheter och licenser

Följande roller kan komma åt användningen och insikter:

- Global administratör
- Säkerhetsläsare
- Säkerhetsadministratör
- Rapportläsare

Inga ytterligare licenser krävs att åtkomst användningen och insikter. Azure Multi-Factor Authentication och licensinformation för lösenord för självbetjäning (SSPR) för återställning kan hittas på den [priser platsen för Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="how-it-works"></a>Hur det fungerar

Öppna autentisering metoden användningen och insikter:

1. Bläddra till [Azure-portalen](https://portal.azure.com).
1. Bläddra till **Azure Active Directory** > **lösenordsåterställning** > **användning och insikter**.
1. Från den **registrering** eller **användning** översikter, kan du öppna förväg filtrerade rapporter för att filtrera efter dina behov.

![Översikt över användning och insikter](./media/howto-authentication-methods-usage-insights/usage-insights-overview.png)

Du kommer åt användning och insikter direkt genom att gå till [ https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade). Den här länken kommer du till översikten för registrering.

Användarna som har registrerats, användare aktiverat och paneler som användare kan visa följande registreringsdata för dina användare:

- Registrerad: En användare anses vara registrerad om de (eller en administratör) har registrerat dig tillräckligt med autentiseringsmetoder för att uppfylla organisationens princip för SSPR eller Multi-Factor Authentication.
- Aktiverad: En användare anses vara aktiverad om de är inom omfånget för SSPR-principen. Om SSPR har aktiverats för en grupp, anses användaren aktiverad om de finns i den gruppen. Om SSPR är aktiverad för alla användare, och sedan alla användare i klienten (exklusive gäster) anses vara aktiverat.
- Kompatibel: En användare anses vara kompatibla om de både registrerad och aktiverad. Denna status innebär att de kan utföra SSPR när som helst om det behövs.

När du klickar på någon av dessa paneler eller insikter som visas i dem kommer du till en förväg filtrerad lista över registreringsinformationen.

Den **registreringar** diagram på den **registrering** fliken visar antalet lyckade och misslyckade autentisering metoden registreringar efter autentiseringsmetod. Den **återställer** diagram på den **användning** fliken visar antalet lyckade och misslyckade autentiseringar under lösenordet återställa flödet av autentiseringsmetod.

När du klickar på något av diagrammen får du en förväg filtrerad lista över registrering eller återställa händelser.

Med kontrollen i det övre högra hörnet kan ändra du intervallet för granskningsdata visas i diagrammen registreringar och återställer till 24 timmar, 7 dagar eller 30 dagar.

Registreringsdata från den 

### <a name="registration-details"></a>Registreringsinformation

När du klickar på den **användare som har registrerats**, **användare som har aktiverats**, eller **användare som är kompatibla** paneler eller insikter kommer du till de här registreringsinformationen.

Rapporten information om registrering visar följande information för varje användare:

- Namn
- Användarnamn
- Registreringsstatus (är registrerad, inte registrerad)
- Aktiverad status (alla, är aktiverade, inte aktiverad)
- Kompatibel status (alla, kapabel, inte kompatibla)
- Metoder (appmeddelande, kod, telefonsamtal, SMS, e-post, säkerhetsfrågor)

Med kontrollerna längst ned i listan kan du söka efter en användare och filtrera listan över användare baserat på de kolumner som visas.

### <a name="reset-details"></a>Informationen för återställning

När du klickar på diagrammen registreringar eller återställer kommer du till information för återställning.

Återställning av information om rapporten innehåller registrering och återställning av händelser från de senaste 30 dagarna inklusive:

- Namn
- Användarnamn
- Funktionen (alla, registrering, återställa)
- Autentiseringsmetod (appmeddelande, kod, telefonsamtal, Office-anrop, SMS, e-post, säkerhetsfrågor)
- Status (allt är klart, fel)

Med kontrollerna längst ned i listan kan du söka efter en användare och filtrera listan över användare baserat på de kolumner som visas.

## <a name="limitations"></a>Begränsningar

De data som visas i dessa rapporter kommer att fördröjas med upp till 60 minuter. En ”senast uppdaterad” fält finns i Azure portal för att identifiera hur aktuella data är.

Användnings-och insikter är inte en ersättning för aktivitetsrapporter för Azure Multi-Factor Authentication eller informationen i rapporten för Azure AD-inloggningar.

## <a name="next-steps"></a>Nästa steg

- [Arbeta med autentisering metoder användningsrapporten API](https://docs.microsoft.com/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [Val av autentiseringsmetoder för din organisation](concept-authentication-methods.md)
- [Kombinerade registrerings-upplevelse](concept-registration-mfa-sspr-combined.md)
