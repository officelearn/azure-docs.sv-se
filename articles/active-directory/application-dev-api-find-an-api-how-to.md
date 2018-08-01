---
title: Hitta ett visst API som behövs för ett egenutvecklat program | Microsoft Docs
description: Så här konfigurerar du de behörigheter som du behöver komma åt ett visst API i dina anpassade utvecklade Azure AD-program
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: f15e84d6efa30d2e66c7ff1d92551bece94938e7
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2018
ms.locfileid: "39363354"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Hitta ett visst API som behövs för ett egenutvecklat program

Åtkomst till API: er kräver konfiguration av åtkomstscope och roller. Om du vill exponera din resurs programmet web API: er för klientprogram måste du konfigurera åtkomstscope och roller för API: et. Om du vill att ett klientprogram att komma åt ett webb-API som du behöver konfigurera behörigheter för att få åtkomst till API i appregistreringen.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Konfigurera resursprogram att exponera webb API: er

När du exponerar ditt webb-API, API: et ska visas i den **Välj en API** listan när du lägger till behörigheter till en appregistrering. Om du vill lägga till scope, följer du stegen som beskrivs i [att lägga till scope till din resursprogrammet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-access-scopes-to-your-resource-application).

## <a name="configuring-a-client-application-to-access-web-apis"></a>Konfigurera ett klientprogram för att få åtkomst till webb-API: er

När du lägger till behörigheter i din appregistrering, kan du **Lägg till API-åtkomst** till exponerade webb-API: er. För att komma åt webb API: er, följer du stegen som beskrivs i [lägga till autentiseringsuppgifter eller behörighet att komma åt webb API: er](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#to-add-credentials-or-permissions-to-access-web-apis).

## <a name="next-steps"></a>Nästa steg

-   [Integrera program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

-   [Förstå programmanifestet för Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)


