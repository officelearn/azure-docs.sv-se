---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: a11b291ab89dc9f8159e00e1f2304706f041068e
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "60297752"
---
## <a name="test-your-code"></a>Testa koden

Om du vill köra ditt projekt i Visual Studio, Välj **F5**. Programmets **MainWindow** visas som det visas här:

![Testa ditt program](./media/active-directory-develop-guidedsetup-windesktop-test/samplescreenshot.png)

Första gången du kör programmet och välj den **anropa Microsoft Graph API** knappen, uppmanas du för att logga in. Använda en Azure Active Directory-konto (arbets- eller skolkonto konto) eller ett Microsoft-konto (live.com, outlook.com) för att testa den.

![Logga in på programmet](./media/active-directory-develop-guidedsetup-windesktop-test/signinscreenshot.png)

### <a name="provide-consent-for-application-access"></a>Ge medgivande för programåtkomst

Första gången du loggar in på ditt program också uppmanas du att ange samtycka till att program kan komma åt din profil och logga in dig i som visas här:

![Ge ditt medgivande för programåtkomst](./media/active-directory-develop-guidedsetup-windesktop-test/consentscreen.png)

### <a name="view-application-results"></a>Visa program resultat

När du har loggat in visas den information om användarprofiler som returneras av anropet till Microsoft Graph API. Resultaten visas i den **API samtalsresultat** box. Grundläggande information om den token som köptes via anropet till `AcquireTokenInteractive` eller `AcquireTokenSilent` ska synas i den **tokeninformation** box. Resultaten innehåller följande egenskaper:

|Egenskap   |Format  |Beskrivning |
|---------|---------|---------|

|**Användarnamn**  | <span> user@domain.com </span> | Det användarnamn som används för att identifiera användaren. | | **Token upphör att gälla** | DateTime | Den tid då token upphör att gälla. MSAL förlänger utgångsdatumet genom att förnya token efter behov. |


<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mer information om scope och delegerade behörigheter

Microsoft Graph API kräver den *user.read* omfattning att läsa en användares profil. Det här omfånget läggs automatiskt som standard i alla program som har registrerats i portalen för registrering av programmet. Andra API: er för Microsoft Graph, samt anpassade API: er för backend-servern, kan kräva ytterligare scope. Microsoft Graph API kräver den *Calendars.Read* omfattning att lista användarens kalendrar.

Om du vill få åtkomst till användarkalendrar i kontexten för ett program måste du lägga till den *Calendars.Read* delegerad behörighet att registreringsinformation för programmet. Lägg sedan till den *Calendars.Read* begränsa omfånget till den `acquireTokenSilent` anropa.

>[!NOTE]
>Användaren uppmanas för ytterligare medgivanden när du ökar antalet omfång.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
