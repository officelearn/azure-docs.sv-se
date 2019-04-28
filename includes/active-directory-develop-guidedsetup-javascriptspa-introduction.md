---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 8c2dc41fde9387da291b6e4a6c8a6220ae62b514
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60298023"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Logga in användare och anropa Microsoft Graph API från ett JavaScript ensidesapplikation (SPA)

Den här guiden visar hur en JavaScript ensidesapplikation (SPA) som kan logga in Mina, uppgifter och skolkonton, få en åtkomsttoken och anropa Microsoft Graph API eller andra API: er som kräver åtkomst-token från slutpunkten för Microsoft identity-plattformen.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Så här fungerar exempelapp som genererats av den här guiden

![Visar hur exempelappen genereras av den här självstudier fungerar](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>Mer information

Exempelprogrammet som skapats av den här guiden kan ett JavaScript SPA att fråga Microsoft Graph API eller ett webb-API som accepterar token från slutpunkten för Microsoft identity-plattformen. Det här scenariot när en användare loggar in, har en åtkomsttoken begärt och läggs till HTTP-förfrågningar via auktoriseringsrubriken. Tokenförvärv och förnyelse hanteras av Microsoft Authentication Library (MSAL).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Bibliotek

Den här guiden använder följande bibliotek:

|Bibliotek|Beskrivning|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Microsoft Authentication Library för JavaScript-förhandsversion|

> [!NOTE]
> *msal.js* mål den *Microsoft identity-plattformen endpoint* – vilket gör att personliga, skolan och arbetsrelaterade konton att logga in och hämta token. Den *Microsoft identity-plattformen endpoint* har [vissa begränsningar](../articles/active-directory/develop/active-directory-v2-limitations.md).
> Att förstå skillnaderna mellan slutpunkterna v1.0 och v2.0 läsa den [Endpoint-Jämförelseguide](../articles/active-directory/develop/azure-ad-endpoint-comparison.md).

<!--end-collapse-->
