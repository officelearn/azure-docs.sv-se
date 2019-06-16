---
title: Kända problem i webbläsare (Microsoft Authentication Library för JavaScript) | Azure
description: Lär dig om kända problem när du använder Microsoft Authentication Library för JavaScript (MSAL.js) med Internet Explorer och Microsoft Edge.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c57ed956ec50c8bac26720a27894c07353928336
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65873907"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-with-msaljs"></a>Kända problem i Internet Explorer och Microsoft Edge-webbläsare MSAL.js

## <a name="issues-due-to-security-zones"></a>Problem som orsakas av säkerhetszoner
Vi hade flera rapporter av problem med autentisering i Internet Explorer och Microsoft Edge (sedan uppdateringen av den *version av Microsoft Edge-webbläsaren till 40.15063.0.0*). Vi följer upp dem och har informerat Microsoft Edge-teamet. Och Microsoft Edge fungerar på en lösning, är här en beskrivning av de ofta återkommande problem och möjliga lösningar som kan implementeras.

### <a name="cause"></a>Orsak
För de flesta av de här problemen beror på följande sätt. Lagring av sessionen och lokal lagring partitioneras efter säkerhetszoner i Microsoft Edge-webbläsaren. Den här specifika versionen av Microsoft Edge när programmet omdirigeras i olika zoner, har lagring av sessionen och lokal lagring behörighet. Mer specifikt session lagringen tas bort i navigeringen till vanliga webbläsare och både sessionen och lokal lagring raderas i InPrivate-läge i webbläsaren. MSAL.js sparar viss status i sessionen storage och förlitar sig på att kontrollera det här tillståndet under autentiseringsflöden. När sessionen lagringen är avmarkerad tappas bort det här tillståndet och kan därför resulterar i bruten upplevelser.

### <a name="issues"></a>Problem

- **Oändlig omdirigering loopar och sidan läses in på nytt under autentiseringen**. När användare loggar in till programmet i Microsoft Edge, omdirigeras tillbaka från AAD-inloggningssidan och har fastnat i en oändlig omdirigerings-loop vilket resulterar i upprepade sidan läser in. Detta är vanligtvis åtföljs av en `invalid_state` fel i sessionen-lagring.

- **Oändlig hämta token loopar och AADSTS50058 fel**. När ett program som körs i Microsoft Edge försöker hämta en token för en resurs, kan programmet fastna i en oändlig loop samtalets hämta token tillsammans med följande fel från AAD i din nätverksspårning:

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- **Popup-fönster inte stänga eller fastnar när inloggningen via popup-fönstret för att autentisera**. Vid autentisering via popup-fönster i Microsoft Edge eller IE(InPrivate), när du anger autentiseringsuppgifter och logga in om flera domäner i säkerhetszoner är inblandade i navigeringsfönstret, stänga popup-fönster inte eftersom MSAL.js förlorar referensen till i popup-fönstret.  

    Här finns länkar till dessa problem i Microsoft Edge-felspårningsfunktionen:  
    - [Bugg 13861050](https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/13861050/)
    - [Bugg 13861663](https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/13861663/)

### <a name="update-fix-available-in-msaljs-023"></a>Uppdatering: En korrigering är tillgänglig i MSAL.js 0.2.3
Korrigeringar för autentiseringsproblem omdirigerings-slingan har frisläppts under [MSAL.js 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases). Aktivera flaggan `storeAuthStateInCookie` in MSAL.js konfigurationen att dra nytta av den här snabbkorrigeringen. Den här flaggan anges till false som standard.

När den `storeAuthStateInCookie` flaggan är aktiverad, MSAL.js använder cookies i webbläsaren för att lagra tillstånd för begäran som krävs för verifiering av auth-flöden.

> [!NOTE]
> Den här snabbkorrigeringen är inte tillgänglig för msal angular och msal angularjs omslutningar ännu. Den här snabbkorrigeringen åtgärdar inte problemet med popup-fönster.

Använd lösningar nedan.

#### <a name="other-workarounds"></a>Andra lösningar
Se till att testa att problemet sker endast på de specifika versionerna av Microsoft Edge-webbläsare och fungerar på andra webbläsare inför införandet av dessa lösningar.  
1. Som ett första steg för att undvika dessa problem, se till att programmet-domänen, och andra webbplatser som ingår i omdirigeringar av autentiseringsflödet läggs till som betrodda platser i säkerhetsinställningarna i webbläsaren så att de tillhör samma säkerhetszon.
Så här gör du:
    - Öppna **Internet Explorer** och klicka på den **inställningar** (kugghjulsikonen) i det övre högra hörnet
    - Välj **Internet-alternativ**
    - Välj den **Security** fliken
    - Under den **tillförlitliga platser** klickar du på på den **platser** knappen och Lägg till URL: er i dialogrutan som öppnas.

2. Som vi nämnde innan sedan endast sessionsnyckeln lagring tas bort under den vanliga navigeringen, kan du konfigurera MSAL.js om du vill använda den lokala lagringen i stället. Detta kan anges som den `cacheLocation` config parametern vid initiering av MSAL.

Observera att detta inte löser problemet för InPrivate-surfning eftersom både sessionen och lokal lagring rensas.

## <a name="issues-due-to-popup-blockers"></a>Problem som orsakas av popup-blockerare

Det finns fall när popup-fönster blockeras i Internet Explorer eller Microsoft Edge, till exempel när ett andra popup-fönster som uppstår under multifaktorautentisering. Du får en avisering i webbläsaren så att popup-fönstret när eller alltid. Om du väljer att tillåta webbläsaren öppnas automatiskt i popup-fönstret och returnerar en `null` hantera för den. Därför biblioteket har inte en referens till den i fönstret och det finns inget sätt att Stäng popup-fönstret. Samma problem sker inte i Chrome när uppmanas du att Tillåt popup-fönster eftersom den inte automatiskt öppnas ett popup-fönster.

Som en **lösning**, utvecklare behöver Tillåt popup-fönster i Internet Explorer och Microsoft Edge innan de börjar använda appen för att undvika det här problemet.

## <a name="next-steps"></a>Nästa steg
Läs mer om [med MSAL.js i Internet Explorer](msal-js-use-ie-browser.md).
