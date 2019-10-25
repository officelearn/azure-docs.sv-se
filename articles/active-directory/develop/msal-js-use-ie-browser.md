---
title: Använd Internet Explorer (Microsoft Authentication Library för Java Script)
titleSuffix: Microsoft identity platform
description: Lär dig mer om att använda Microsoft Authentication Library för Java Script (MSAL. js) med Internet Explorer-webbläsare.
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
ms.openlocfilehash: 7c79717b00cd9a4b5da00496bf2f1822f7f77032
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802983"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-with-msaljs"></a>Kända problem i Internet Explorer och Microsoft Edge-webbläsare med MSAL. js

Microsoft Authentication Library för Java Script (MSAL. js) genereras för [JavaScript-ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) så att det kan köras i Internet Explorer. Det finns dock några saker att känna till.

## <a name="run-an-app-in-internet-explorer"></a>Köra en app i Internet Explorer
Om du tänker använda MSAL. js i program som kan köras i Internet Explorer, måste du lägga till en referens till en Promise POLYfill innan du refererar till MSAL. js-skriptet.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

Detta beror på att Internet Explorer inte har stöd för JavaScript-löfte internt.

## <a name="debugging-an-application-running-in-internet-explorer"></a>Felsöka ett program som körs i Internet Explorer

### <a name="running-in-production"></a>Körs i produktion
Att distribuera ditt program till produktion (till exempel i Azure Web Apps) fungerar normalt bra, förutsatt att slutanvändaren har accepterat popup-fönster. Vi testade det med Internet Explorer 11.

### <a name="running-locally"></a>Körs lokalt
Om du vill köra och felsöka lokalt program som körs i Internet Explorer måste du vara medveten om följande överväganden (anta att du vill köra programmet som *http://localhost:1234* ):

- Internet Explorer har en säkerhetsmekanism med namnet "skyddat läge", vilket förhindrar att MSAL. js fungerar korrekt. När du har loggat in kan du bland symtomen omdirigera sidan till http://localhost:1234/null.

- Om du vill köra och felsöka ditt program lokalt måste du inaktivera det här "skyddade läget". För detta:

    1. Klicka på Internet Explorer- **verktyg** (kugg hjuls ikonen).
    1. Välj **Internet alternativ** och sedan fliken **säkerhet** .
    1. Klicka på zonen **Internet** och avmarkera **Aktivera skyddat läge (kräver att Internet Explorer startas om)** . Internet Explorer varnar att datorn inte längre är skyddad. Klicka på **OK**
    1. Starta om Internet Explorer.
    1. Kör och Felsök ditt program.

När du är färdig återställer du säkerhets inställningarna för Internet Explorer.  Välj **inställningar** -> **Internet alternativ** -> **säkerhet** -> **återställa alla zoner till standard nivån**.

## <a name="next-steps"></a>Nästa steg
Läs mer om [kända problem när du använder MSAL. js i Internet Explorer](msal-js-use-ie-browser.md).