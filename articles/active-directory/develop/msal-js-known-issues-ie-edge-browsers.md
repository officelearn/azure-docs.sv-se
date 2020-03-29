---
title: Problem med Internet Explorer & Microsoft Edge (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om problem när du använder webbläsarna Microsoft Authentication Library for JavaScript (MSAL.js) med webbläsare i Internet Explorer och Microsoft Edge.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 5ae2dee68ec0da8e8a00d4f01583461462bc196c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696103"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Kända problem i webbläsare i Internet Explorer och Microsoft Edge (MSAL.js)

## <a name="issues-due-to-security-zones"></a>Problem på grund av säkerhetszoner
Vi hade flera rapporter om problem med autentisering i IE och Microsoft Edge (sedan uppdateringen av *Microsoft Edge webbläsarversion till 40.15063.0.0*). Vi spårar dessa och har informerat Microsoft Edge-teamet. Medan Microsoft Edge arbetar med en lösning, här är en beskrivning av de ofta förekommande problem och möjliga lösningar som kan genomföras.

### <a name="cause"></a>Orsak
Orsaken till de flesta av dessa frågor är följande. Sessionslagringen och den lokala lagringen partitioneras efter säkerhetszoner i Webbläsaren Microsoft Edge. I den här versionen av Microsoft Edge rensas sessionslagringen och den lokala lagringen när programmet omdirigeras mellan zoner. Specifikt rensas sessionslagringen i den vanliga webbläsarnavigeringen och både sessionen och den lokala lagringen rensas i webbläsarens InPrivate-läge. MSAL.js sparar vissa tillstånd i sessionslagringen och förlitar sig på att kontrollera det här tillståndet under autentiseringsflödena. När sessionslagringen rensas går det här tillståndet förlorat och resulterar därför i brutna upplevelser.

### <a name="issues"></a>Problem

- **Oändlig omdirigeringsloopar och sidreloadingar under autentisering**. När användare loggar in på programmet på Microsoft Edge omdirigeras de tillbaka från AAD-inloggningssidan och har fastnat i en oändlig omdirigeringsloop som resulterar i upprepade sidinläsningar. Detta åtföljs vanligtvis `invalid_state` av ett fel i sessionslagringen.

- **Oändlig förvärva token loopar och AADSTS50058 fel**. När ett program som körs på Microsoft Edge försöker hämta en token för en resurs kan programmet fastna i en oändlig loop av det inhämtningstoken-anropet tillsammans med följande fel från AAD i nätverksspårningen:

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- **Popup-fönstret stängs inte eller har fastnat när du använder inloggning via Popup för att autentisera**. När du autentiserar via popup-fönstret i Microsoft Edge eller IE(InPrivate), efter att ha angett autentiseringsuppgifter och loggat in, om flera domäner över säkerhetszoner är involverade i navigeringen, stängs popup-fönstret inte eftersom MSAL.js förlorar handtaget till popup-fönstret.  

### <a name="update-fix-available-in-msaljs-023"></a>Uppdatering: Fix finns i MSAL.js 0.2.3
Korrigeringar för problem med autentiseringsomdirigeringsloopen har släppts i [MSAL.js 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases). Aktivera flaggan `storeAuthStateInCookie` i MSAL.js config för att dra nytta av den här korrigeringsfilen. Som standard är den här flaggan inställd på false.

När `storeAuthStateInCookie` flaggan är aktiverad använder MSAL.js webbläsarcookies för att lagra det begärandetillstånd som krävs för validering av auth-flödena.

> [!NOTE]
> Den här korrigeringsfilen är ännu inte tillgänglig för msal-vinkel och msal-angularjs omslag. Den här korrigeringsfilen åtgärdar inte problemet med Popup-fönster.

Använd lösningar nedan.

#### <a name="other-workarounds"></a>Andra lösningar
Se till att testa att problemet bara uppstår i den specifika versionen av Webbläsaren Microsoft Edge och fungerar i andra webbläsare innan du antar dessa lösningar.  
1. Som ett första steg för att komma runt dessa problem, se till att programdomänen, och alla andra platser som deltar i omdirigeringar av autentiseringsflödet läggs till som betrodda platser i webbläsarens säkerhetsinställningar, så att de tillhör samma säkerhetszon.
Det gör du på följande sätt:
    - Öppna **Internet Explorer** och klicka på **inställningarna** (kugghjulsikonen) i det övre högra hörnet
    - Välj **Internet-alternativ**
    - Välj fliken **Säkerhet**
    - Under alternativet **Betrodda platser** klickar du på **platsknappen** och lägger till webbadresserna i dialogrutan som öppnas.

2. Som tidigare nämnts kan du konfigurera MSAL.js så att den lokala lagringen används i stället, eftersom endast sessionslagringen rensas under den vanliga navigeringen. Detta kan ställas `cacheLocation` in som konfigurationsparameter när DEN initierar MSAL.

Detta löser inte problemet för InPrivate-surfning eftersom både session och lokal lagring rensas.

## <a name="issues-due-to-popup-blockers"></a>Problem på grund av popup-blockerare

Det finns fall när popups blockeras i IE eller Microsoft Edge, till exempel när en andra popup inträffar under multifaktorautentisering. Du kommer att få en varning i webbläsaren för att möjliggöra popup en gång eller alltid. Om du väljer att tillåta, öppnar webbläsaren popup-fönstret automatiskt och returnerar ett `null` handtag för det. Därför har biblioteket inget handtag för fönstret och det går inte att stänga popup-fönstret. Samma problem uppstår inte i Chrome när du uppmanas att tillåta popup-fönster eftersom det inte automatiskt öppnar ett popup-fönster.

Som en **lösning**måste utvecklare tillåta popup-fönster i IE och Microsoft Edge innan de börjar använda sin app för att undvika det här problemet.

## <a name="next-steps"></a>Nästa steg
Läs mer om [hur du använder MSAL.js i Internet Explorer](msal-js-use-ie-browser.md).
