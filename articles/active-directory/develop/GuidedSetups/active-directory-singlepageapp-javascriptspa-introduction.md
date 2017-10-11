---
title: Azure AD v2 JS SPA interaktiv installation - introduktion | Microsoft Docs
description: "Hur JavaScript SPA program anropar en API som kräver åtkomst-token i Azure Active Directory v2 slutpunkten"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/01/2017
ms.author: andret
ms.openlocfilehash: 3d195d0d67f8f82c9450ffd93767917698addee3
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Anropa Microsoft Graph API från ett JavaScript Single-Page Application (SPA)

Den här guiden visar hur en JavaScript enda sidan program (SPA) kan logga in personliga arbete och skolkonton, hämta en åtkomst-token och anropa Microsoft Graph API eller andra API: er som kräver åtkomst-token från slutpunkten för Azure Active Directory-v2.

### <a name="how-this-guide-works"></a>Hur den här guiden fungerar

![Så här fungerar sample-appen som genererats av den här guiden](media/active-directory-singlepageapp-javascriptspa-introduction/javascriptspa-intro.png)

<!--start-collapse-->
### <a name="more-information"></a>Mer information

Det exempelprogram som skapats av den här guiden kan en JavaScript-SPA frågar Microsoft Graph API eller ett webb-API som accepterar token från Azure Active Directory v2-slutpunkten. För det här scenariot, när en användare loggar in, begärt en åtkomst-token och lägga till HTTP-förfrågningar via authorization-huvud. Token förvärv och förnyelse hanteras av Microsoft Authentication Library (MSAL).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Bibliotek

Den här guiden använder följande biblioteket:

|Bibliotek|Beskrivning|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Microsoft-Autentiseringsbibliotek för JavaScript-förhandsgranskning|

> [!NOTE]
> *msal.js* mål i *Azure Active Directory v2 endpoint* -vilket innebär att personliga, skolan och arbetsrelaterade konton att logga in och hämta token. Den *Azure Active Directory v2 endpoint* har [vissa begränsningar](..\active-directory-v2-limitations.md). Om du bara vill skolan och arbetsrelaterade konton använder *adal.js* och *V1 endpoint*. Att förstå skillnaderna mellan v1 och v2-slutpunkter läsa den [v1 v2 jämförelse](..\active-directory-v2-compare.md).

<!--end-collapse-->
