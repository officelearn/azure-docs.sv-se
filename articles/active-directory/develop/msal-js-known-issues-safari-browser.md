---
title: Kända problem i webbläsare (Microsoft Authentication Library för Java Script)
titleSuffix: Microsoft identity platform
description: Lär dig om kända problem när du använder Microsoft Authentication Library för Java Script (MSAL. js) med Safari-webbläsare.
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
ms.openlocfilehash: 51d800ea2fbbc733a6213d7bc4f61f955612aba0
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803063"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>Kända problem i Safari-webbläsare med MSAL. js 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>Förnyelse av tyst token i Safari 12 och ITP 2,0

Operativ systemen Apple iOS 12 och MacOS 10,14 innehöll en version av [Safari 12-webbläsaren](https://developer.apple.com/safari/whats-new/). I säkerhets-och sekretess syfte innehåller Safari 12 den [intelligenta spårnings förebyggande 2,0](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/). Detta leder till att webbläsaren släpper cookies från tredje part som anges. ITP 2,0 behandlar även cookies som anges av identitets leverantörer som cookies från tredje part.

### <a name="impact-on-msaljs"></a>Inverkan på MSAL. js

MSAL. js använder en dold iframe för att utföra insamling och förnyelse av tyst token som en del av de `acquireTokenSilent`-anropen. De tysta token-begärandena är beroende av den iframe som har åtkomst till den autentiserade användarsessionen som representeras av de cookies som anges av Azure AD. När ITP 2,0 förhindrar åtkomst till dessa cookies kan MSAL. js inte tyst hämta och förnya token och detta resulterar i `acquireTokenSilent` fel.

Det finns ingen lösning för det här problemet nu och vi utvärderar alternativ med communityn för standarder.

### <a name="work-around"></a>Undvik

Som standard aktive ras inställningen ITP i Safari-webbläsare. Du kan inaktivera den här inställningen genom att gå till **inställningar** -> **Sekretess** och avmarkera alternativet **förhindra spårning mellan platser** .

![Safari-inställning](./media/msal-js-known-issue-safari-browser/safari.png)

Du måste hantera `acquireTokenSilent`-felen med ett interaktivt anrop för att hämta token, vilket gör att användaren kan logga in.
För att undvika upprepade inloggningar är en metod som du kan implementera att hantera `acquireTokenSilent`-felen och ge användaren ett alternativ för att inaktivera inställningen ITP i Safari innan du fortsätter med det interaktiva anropet. När inställningen är inaktive rad bör efterföljande tysta token-förnyelsen lyckas.
