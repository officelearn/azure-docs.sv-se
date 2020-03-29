---
title: Autentiseringsmetoder användning & insikter - Azure Active Directory
description: Rapportering om azure AD-självbetjäningslösenordsåterställning och användning av autentiseringsmetod för multifaktorautentisering
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: b448e6ce7c8b4522d5e7bdbafb39eccca982fdee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848518"
---
# <a name="authentication-methods-usage--insights-preview"></a>Användning av autentiseringsmetoder & insikter (förhandsgranskning)

Med & insikter kan du förstå hur autentiseringsmetoder för funktioner som Azure Multi-Factor Authentication och återställning av lösenord med självbetjäning fungerar i din organisation. Den här rapporteringsfunktionen ger din organisation möjlighet att förstå vilka metoder som registreras och hur de används.

## <a name="permissions-and-licenses"></a>Behörigheter och licenser

Följande roller kan komma åt användning och insikter:

- Global administratör
- Säkerhetsläsare
- Säkerhetsadministratör
- Rapporter Reader

Ingen ytterligare licensiering krävs för att komma åt användning och insikter. Azure Multi-Factor Authentication och självbetjäningslösenordsåterställning (SSPR) finns på [azure Active Directory-prisplatsen](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="how-it-works"></a>Hur det fungerar

Så här kommer du åt användning och insikter för autentiseringsmetod:

1. Bläddra till [Azure-portalen](https://portal.azure.com).
1. Bläddra till **Azure Active Directory** > **användning för lösenordsåterställning** > **& insikter**.
1. I översikterna **Registrering** **eller Användning** kan du välja att öppna de förfiltrerade rapporterna för att filtrera baserat på dina behov.

![Översikt över & insikter](./media/howto-authentication-methods-usage-insights/usage-insights-overview.png)

Om du vill komma åt användning [https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade)& insikter direkt går du till . Denna länk tar dig till registreringsöversikten.

De registrerade, aktiverade användarna och panelerna Användare visar följande registreringsdata för användarna:

- Registrerad: En användare anses vara registrerad om de (eller en administratör) har registrerat tillräckligt med autentiseringsmetoder för att uppfylla organisationens SSPR- eller Multifaktorautentiseringsprincip.
- Aktiverad: En användare anses aktiverad om de är i omfånget för SSPR-principen. Om SSPR är aktiverat för en grupp anses användaren vara aktiverad om de finns i den gruppen. Om SSPR är aktiverat för alla användare betraktas alla användare i klienten (exklusive gäster) som aktiverade.
- Kan: En användare anses kapabel om de är både registrerade och aktiverade. Den här statusen innebär att de kan utföra SSPR när som helst om det behövs.

Om du klickar på någon av dessa paneler eller de insikter som visas i dem kommer du till en förfiltrerade lista med registreringsuppgifter.

**Registreringsdiagrammet** på fliken **Registrering** visar antalet misslyckade och misslyckade registreringar av autentiseringsmetod med autentiseringsmetod. Diagrammet **Återställer** på fliken **Användning** visar antalet lyckade och misslyckade autentiseringar under lösenordsåterställningsflödet med autentiseringsmetod.

Om du klickar på något av diagrammen kommer du till en förfiltrerade lista över registrerings- eller återställningshändelser.

Med hjälp av kontrollen i det övre högra hörnet kan du ändra datumintervallet för granskningsdata som visas i diagrammen Registreringar och återställer till 24 timmar, 7 dagar eller 30 dagar.

### <a name="registration-details"></a>Registreringsuppgifter

Om du klickar på de **registrerade användarna,** **användare aktiverade**eller **användare som kan** paneler eller insikter kommer du till registreringsinformationen.

Registreringsinformationsrapporten visar följande information för varje användare:

- Namn
- Användarnamn
- Registreringsstatus (Alla, Registrerade, Ej registrerade)
- Aktiverad status (alla, aktiverade, inte aktiverade)
- Kapabel status (Alla, kan, inte kan)
- Metoder (App anmälan, Appkod, Telefonsamtal, SMS, E-post, Säkerhetsfrågor)

Med hjälp av kontrollerna högst upp i listan kan du söka efter en användare och filtrera listan över användare baserat på de kolumner som visas.

### <a name="reset-details"></a>Återställa information

Om du klickar på diagrammen Registreringar eller återställer kommer du till återställningsinformationen.

Rapporten återställningsinformation visar registrerings- och återställningshändelser från de senaste 30 dagarna, inklusive:

- Namn
- Användarnamn
- Funktion (alla, registrering, återställning)
- Autentiseringsmetod (appavisering, appkod, telefonsamtal, Office-samtal, SMS, E-post, säkerhetsfrågor)
- Status (alla, framgång, misslyckande)

Med hjälp av kontrollerna högst upp i listan kan du söka efter en användare och filtrera listan över användare baserat på de kolumner som visas.

## <a name="limitations"></a>Begränsningar

De data som visas i dessa rapporter kommer att försenas med upp till 60 minuter. Det finns ett fält "Senast uppdaterad" i Azure-portalen för att identifiera hur senaste dina data är.

Användnings- och insiktsdata ersätter inte aktivitetsrapporterna eller informationen i Azure AD-inloggningsrapporten.

## <a name="next-steps"></a>Nästa steg

- [Arbeta med API:et för användningsrapport för autentiseringsmetoder](https://docs.microsoft.com/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [Välja autentiseringsmetoder för din organisation](concept-authentication-methods.md)
- [Kombinerad registreringserfarenhet](concept-registration-mfa-sspr-combined.md)
