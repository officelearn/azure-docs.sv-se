---
title: Kända problem i webbläsare (Microsoft Authentication Library för JavaScript) | Azure
description: Lär dig om kända problem när du använder Microsoft Authentication Library för JavaScript (MSAL.js) med Safari-webbläsaren.
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
ms.openlocfilehash: cb89b1ef4dbbef234fba3152d7f85bbadfbdc64a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65873892"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>Kända problem på Safari-webbläsaren med MSAL.js 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>Tyst token förnyelse på 12 Safari och ITP 2.0

Apple iOS 12- och MacOS 10.14 operativsystem med en version av den [webbläsaren Safari 12](https://developer.apple.com/safari/whats-new/). För säkerhet och sekretess, Safari 12 innehåller den [Intelligent spåra Dataförlustskydd 2.0](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/). Detta innebär att webbläsaren om du vill ta bort cookies från tredje part som har angetts. ITP 2.0 behandlar även de cookies som angetts av identitetsleverantörer som cookies från tredje part.

### <a name="impact-on-msaljs"></a>Påverkan på MSAL.js

MSAL.js använder en dold Iframe för att utföra tyst tokenförvärv och förnyelse som en del av den `acquireTokenSilent` anrop. Tyst tokenbegäranden förlitar sig på Iframe att ha åtkomst till den autentiserade användarsessionen som representeras av cookies som skapats av Azure AD. ITP 2.0 förhindrar åtkomst till dessa cookies MSAL.js misslyckas tyst hämta och att förnya token och detta resulterar i `acquireTokenSilent` fel.

Det finns ingen lösning på problemet nu och vi håller på att utvärdera alternativ standarder-communityn.

### <a name="work-around"></a>Kringgå

Som standard är inställningen ITP aktiveras på Safari-webbläsaren. Du kan inaktivera den här inställningen genom att gå till **inställningar** -> **sekretess** och avmarkera de **förhindra skriptkörning spårning** alternativet.

![Safari-inställning](./media/msal-js-known-issue-safari-browser/safari.png)

Du måste hantera den `acquireTokenSilent` fel med en interaktiv hämta token-anrop som uppmanar användaren att logga in.
För att undvika upprepade inloggningar, en metod som du kan implementera är att hantera den `acquireTokenSilent` fel och ge användaren en möjlighet att inaktivera inställningen ITP i Safari innan du fortsätter med det interaktiva anropet. När inställningen är inaktiverat ska efterföljande tyst token förnyelser lyckas.
