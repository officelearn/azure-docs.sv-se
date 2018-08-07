---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: f70e0bcb68f059618f9b398a00e23498a10df23e
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39582930"
---
# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Anropa Microsoft Graph API från en enda JavaScript på en sida-program (SPA)

Den här guiden visar hur en JavaScript enda sidan program (SPA) kan logga in Mina, uppgifter och skolkonton, få en åtkomsttoken och anropa Microsoft Graph API eller andra API: er som kräver åtkomsttoken från Azure Active Directory v2-slutpunkten.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Så här fungerar exempelapp som genererats av den här guiden

![Så här fungerar exempelapp som genererats av den här guiden](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.png)

<!--start-collapse-->
### <a name="more-information"></a>Mer information

Exempelprogrammet som skapats av den här guiden kan ett JavaScript SPA att fråga Microsoft Graph API eller ett webb-API som accepterar token från Azure Active Directory v2-slutpunkten. I det här scenariot när en användare loggar in, har en åtkomst-token begärt och läggs till HTTP-förfrågningar via auktoriseringsrubriken. Tokenförvärv och förnyelse hanteras av Microsoft Authentication Library (MSAL).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Bibliotek

Den här guiden använder följande bibliotek:

|Bibliotek|Beskrivning|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Microsoft Authentication Library för JavaScript-förhandsversion|

> [!NOTE]
> *msal.js* mål den *Azure Active Directory v2-slutpunkten* – vilket gör att personliga, skolan och arbetsrelaterade konton att logga in och hämta token. Den *Azure Active Directory v2-slutpunkten* har [vissa begränsningar](..\articles\active-directory\develop\active-directory-v2-limitations.md). Om du är intresserad av att endast skolan och arbetsrelaterade konton kan använda *adal.js* och *V1-slutpunkten*. Att förstå skillnaderna mellan v1 och v2-slutpunkter läser den [v1 och v2-jämförelse](../articles/active-directory/develop/azure-ad-endpoint-comparison.md).

<!--end-collapse-->
