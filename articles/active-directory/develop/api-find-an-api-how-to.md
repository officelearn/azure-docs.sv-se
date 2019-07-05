---
title: Hitta ett visst API som behövs för ett egenutvecklat program | Microsoft Docs
description: Så här konfigurerar du de behörigheter som du behöver komma åt ett visst API i dina anpassade utvecklade Azure AD-program
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2492a9346585698132e7fd9cfcde068ffd60ebc5
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476157"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Hitta ett visst API som behövs för ett egenutvecklat program

Åtkomst till API: er kräver konfiguration av åtkomstscope och roller. Om du vill exponera din resurs programmet web API: er för klientprogram måste du konfigurera åtkomstscope och roller för API: et. Om du vill att ett klientprogram att komma åt ett webb-API som du behöver konfigurera behörigheter för att få åtkomst till API i appregistreringen.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Konfigurera resursprogram för att göra webb API:er tillgängliga

När du exponerar ditt webb-API, API: et ska visas i den **Välj en API** listan när du lägger till behörigheter till en appregistrering. Om du vill lägga till scope, följer du stegen som beskrivs i [konfigurera ett program att exponera webb API: er](quickstart-configure-app-expose-web-apis.md).

## <a name="configuring-a-client-application-to-access-web-apis"></a>Konfigurera ett klientprogram för att få åtkomst till webb-API: er

När du lägger till behörigheter i din appregistrering, kan du **Lägg till API-åtkomst** till exponerade webb-API: er. För att komma åt webb API: er, följer du stegen som beskrivs i [konfigurera ett klientprogram för att få åtkomst till webb-API: er](quickstart-configure-app-access-web-apis.md).

## <a name="next-steps"></a>Nästa steg

-   [Förstå programmanifestet för Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)


