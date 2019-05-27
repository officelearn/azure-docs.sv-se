---
title: Använd Internet Explorer (Microsoft Authentication Library för JavaScript) | Azure
description: Lär dig mer om hur du använder Microsoft Authentication Library för JavaScript (MSAL.js) med webbläsaren Internet Explorer.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8cf8c84120f4c90d3943cfc31ffbf9aafcec0ba3
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873922"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-with-msaljs"></a>Kända problem i Internet Explorer och Microsoft Edge-webbläsare MSAL.js

Microsoft Authentication Library för JavaScript (MSAL.js) genereras för [JavaScript ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) så att den kan köras i Internet Explorer. Det finns dock några saker att känna till.

## <a name="run-an-app-in-internet-explorer"></a>Kör en app i Internet Explorer
Om du planerar att använda MSAL.js i program som kan köras i Internet Explorer, behöver du lägga till en referens till ett löfte polyfill innan du refererar till skriptet MSAL.js.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

Det beror på att Internet Explorer inte stöder JavaScript löften internt.

## <a name="debugging-an-application-running-in-internet-explorer"></a>Felsökning av program som körs i Internet Explorer

### <a name="running-in-production"></a>Som körs i produktionsmiljön
Distribuera ditt program till produktion (till exempel i Azure Web apps) normalt fungerar bra, under förutsättning att användaren har accepterat popup-fönster. Vi har testat den med Internet Explorer 11.

### <a name="running-locally"></a>Kör programmet lokalt
Om du vill köra och felsöka lokalt ditt program som körs i Internet Explorer, måste du vara medveten om följande överväganden (förutsätter att du vill köra programmet när *http://localhost:1234*):

- Internet Explorer har en säkerhetsmekanism med namnet ”skyddat läge”, vilket förhindrar att MSAL.js fungerar korrekt. Bland problem när du loggar in, på sidan kan omdirigeras till http://localhost:1234/null.

- Om du vill köra och felsöka programmet lokalt, måste du inaktivera den här ”skyddat läge”. För detta:

    1. Klicka på Internet Explorer **verktyg** (kugghjulsikonen).
    1. Välj **Internetalternativ** och sedan den **Security** fliken.
    1. Klicka på den **Internet** zon och avmarkera **Aktivera skyddat läge (kräver omstart av Internet Explorer)**. En varning om att datorn inte längre är skyddad. Klicka på **OK**.
    1. Starta om Internet Explorer.
    1. Köra och felsöka programmet.

När du är klar kan du återställa säkerhetsinställningarna för Internet Explorer.  Välj **inställningar** -> **Internetalternativ** -> **Security** -> **Återställ alla zoner till standardnivån**.

## <a name="next-steps"></a>Nästa steg
Läs mer om [kända problem när du använder MSAL.js i Internet Explorer](msal-js-use-ie-browser.md).