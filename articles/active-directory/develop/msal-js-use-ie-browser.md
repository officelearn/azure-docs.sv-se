---
title: Problem i Internet Explorer (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Använd microsoft authentication library for JavaScript (MSAL.js) med webbläsaren Internet Explorer.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: c4f3c4153e1404a5576427be7ef218f5a669387e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76695865"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Kända problem i webbläsare i Internet Explorer och Microsoft Edge (MSAL.js)

Microsoft Authentication Library for JavaScript (MSAL.js) genereras för [JavaScript ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) så att det kan köras i Internet Explorer. Det finns dock några saker att veta.

## <a name="run-an-app-in-internet-explorer"></a>Köra en app i Internet Explorer
Om du tänker använda MSAL.js i program som kan köras i Internet Explorer måste du lägga till en referens till en promise polyfill innan du refererar till MSAL.js-skriptet.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

Detta beror på att Internet Explorer inte stöder JavaScript-löften internt.

## <a name="debugging-an-application-running-in-internet-explorer"></a>Felsöka ett program som körs i Internet Explorer

### <a name="running-in-production"></a>Kör i produktion
Distribuera ditt program till produktion (till exempel i Azure Web apps) fungerar normalt bra, förutsatt att slutanvändaren har accepterat popups. Vi testade det med Internet Explorer 11.

### <a name="running-locally"></a>Köra lokalt
Om du vill köra och felsöka lokalt ditt program som körs i Internet Explorer, måste du vara *http://localhost:1234*medveten om följande överväganden (anta att du vill köra ditt program som):

- Internet Explorer har en säkerhetsmekanism med namnet "skyddat läge", vilket förhindrar att MSAL.js fungerar korrekt. Bland symptomen, när du loggar in, kan http://localhost:1234/nullsidan omdirigeras till .

- Om du vill köra och felsöka ditt program lokalt måste du inaktivera det här "skyddade läget". För detta:

    1. Klicka på Verktyg **för** Internet Explorer (kugghjulsikonen).
    1. Välj **Internetalternativ** och sedan fliken **Säkerhet.**
    1. Klicka på **zonen Internet** och avmarkera Aktivera **skyddat läge (kräver omstart av Internet Explorer)**. Internet Explorer varnar för att datorn inte längre är skyddad. Klicka på **OK**.
    1. Starta om Internet Explorer.
    1. Kör och felsöka ditt program.

När du är klar återställer du säkerhetsinställningarna för Internet Explorer.  Välj **Inställningar** -> **InternetAlternativ** -> **Säkerhet** -> Återställ alla**zoner till standardnivå**.

## <a name="next-steps"></a>Nästa steg
Läs mer om [Kända problem när du använder MSAL.js i Internet Explorer](msal-js-use-ie-browser.md).