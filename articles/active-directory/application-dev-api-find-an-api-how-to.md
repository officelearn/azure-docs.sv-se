---
title: "Hur du söker efter en specifik API som behövs för ett anpassat utvecklade program | Microsoft Docs"
description: "Så här konfigurerar du de behörigheter som du behöver åtkomst till ett visst API i dina anpassade utvecklade Azure AD-program"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: e0c07fd030339d025894520500d2cd948d31af45
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Hur du söker efter en specifik API som behövs för ett anpassat utvecklade program

Konfiguration av åtkomstscope och roller krävs för åtkomst till API: er. Om du vill visa resursen programmet webbplatsen API: er för klientprogram måste du konfigurera åtkomstscope och roller för API: et. Om du vill att ett klientprogram att komma åt ett webb-API, måste du konfigurera behörigheter för åtkomst till API i app-registreringen.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Konfigurera en resursprogram exponera web API: er

När du exponera ditt webb-API, API visas i den **väljer en API** listan när du lägger till behörigheter för en appregistrering. Om du vill lägga till scope, Följ stegen som beskrivs i [att lägga till scope till tillämpningsprogrammet resurs](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-access-scopes-to-your-resource-application).

## <a name="configuring-a-client-application-to-access-web-apis"></a>Konfigurera ett klientprogram att komma åt web API: er

När du lägger till behörigheter till appregistrering, kan du **lägga till API-åtkomst** exponerade webben API: er. För att komma åt web API: er, Följ stegen som beskrivs i [lägga till autentiseringsuppgifter eller behörighet att komma åt web API: er](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#to-add-credentials-or-permissions-to-access-web-apis).

## <a name="next-steps"></a>Nästa steg

-   [Integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

-   [Förstå Azure Active Directory-programmanifestet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)


