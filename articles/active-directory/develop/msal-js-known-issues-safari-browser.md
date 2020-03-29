---
title: Kända problem med webbläsaren Safari (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om problem med microsoft authentication library for JavaScript (MSAL.js) med webbläsaren Safari.
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
ms.openlocfilehash: edb995e31c2872c1541e29fee09dd66aafc8f9e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696120"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>Kända problem på Webbläsaren Safari med MSAL.js 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>Tyst token förnyelse på Safari 12 och ITP 2.0

Apple iOS 12 och MacOS 10.14 operativsystem ingår en utgåva av [Webbläsaren Safari 12](https://developer.apple.com/safari/whats-new/). För säkerhet och integritet innehåller Safari 12 [Intelligent Tracking Prevention 2.0](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/). Detta orsakar i huvudsak webbläsaren att släppa cookies från tredje part som ställs in. ITP 2.0 behandlar även cookies som ställs in av identitetsleverantörer som cookies från tredje part.

### <a name="impact-on-msaljs"></a>Inverkan på MSAL.js

MSAL.js använder en dold Iframe för att utföra `acquireTokenSilent` tyst tokeninsamling och förnyelse som en del av anropen. De tysta tokenbegärandena är beroende av att Iframe har åtkomst till den autentiserade användarsessionen som representeras av cookies som angetts av Azure AD. Med ITP 2.0 förhindrar åtkomst till dessa cookies, misslyckas MSAL.js att `acquireTokenSilent` tyst förvärva och förnya tokens och detta resulterar i fel.

Det finns ingen lösning på denna fråga i detta skede och vi utvärderar alternativ med standarderna gemenskap.

### <a name="work-around"></a>Arbeta runt

Som standard är ITP-inställningen aktiverad i webbläsaren Safari. Du kan inaktivera den här inställningen genom att navigera till **Inställningar** -> **Sekretess** och avmarkera alternativet Förhindra spårning av **flera webbplatser.**

![safari-inställning](./media/msal-js-known-issue-safari-browser/safari.png)

Du måste hantera `acquireTokenSilent` felen med ett interaktivt hämtningstokenanrop, vilket uppmanar användaren att logga in.
För att undvika upprepade inloggningar är en metod `acquireTokenSilent` som du kan implementera att hantera felet och ge användaren ett alternativ för att inaktivera ITP-inställningen i Safari innan du fortsätter med det interaktiva samtalet. När inställningen har inaktiverats bör efterföljande tysta tokenförnyelser lyckas.
