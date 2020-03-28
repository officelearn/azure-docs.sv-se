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
ms.openlocfilehash: f121be4ec8c3d3ab618e2955d9dbd8ab5eea461d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "79128745"
---
## <a name="test-your-code"></a>Testa koden

Om du vill köra projektet väljer du **F5**i Visual Studio . Din ansökan **MainWindow** visas, som visas här:

![Testa ditt program](./media/active-directory-develop-guidedsetup-windesktop-test/samplescreenshot.png)

Första gången du kör programmet och väljer knappen **Anropa Microsoft Graph API** uppmanas du att logga in. Använd ett Azure Active Directory-konto (arbets- eller skolkonto) eller ett Microsoft-konto (live.com, outlook.com) för att testa det.

![Logga in på programmet](./media/active-directory-develop-guidedsetup-windesktop-test/signinscreenshot.png)

### <a name="provide-consent-for-application-access"></a>Ge samtycke till tillgång till program

Första gången du loggar in på din ansökan uppmanas du också att ge ditt samtycke till att programmet ska få åtkomst till din profil och logga in dig, som du ser här:

![Ge ditt samtycke till tillgång till program](./media/active-directory-develop-guidedsetup-windesktop-test/consentscreen.png)

### <a name="view-application-results"></a>Visa programresultat

När du har loggat in bör du se den information om användarprofilen som returneras av anropet till Microsoft Graph API. Resultaten visas i rutan **API-samtalsresultat.** Grundläggande information om token som förvärvades `AcquireTokenInteractive` `AcquireTokenSilent` via anropet till eller ska vara synlig i rutan **Token Info.** Resultaten innehåller följande egenskaper:

|Egenskap  |Format  |Beskrivning |
|---------|---------|---------|
|**Användarnamn** |<span>user@domain.com</span> |Användarnamnet som används för att identifiera användaren.|
|**Token upphör att gälla** |DateTime |Den tidpunkt då token upphör att gälla. MSAL förlänger utgångsdatumet genom att förnya token efter behov.|


<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mer information om scope och delegerade behörigheter

Microsoft Graph API kräver *att användaren.läser* omfång för att läsa en användares profil. Det här scopet läggs automatiskt till som standard i alla program som är registrerade i programregistreringsportalen. Andra API:er för Microsoft Graph, samt anpassade API:er för backend-servern, kan kräva ytterligare scope. Microsoft Graph API kräver *att calendars.Read-scopet* ska kunna lista användarens kalendrar.

Om du vill komma åt användarens kalendrar i ett program lägger du till den delegerade behörigheten *Calendars.Read* i programregistreringsinformationen. Lägg sedan till *calendars.Read-scopet* i anropet. `acquireTokenSilent`

>[!NOTE]
>Användaren kan uppmanas att ange ytterligare medgivanden när du ökar antalet scope.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
