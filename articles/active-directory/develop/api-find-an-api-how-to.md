---
title: Hitta ett API för en specialutvecklad app | Azure
description: Konfigurera de behörigheter du behöver för att komma åt ett visst API i ditt anpassade utvecklade Azure AD-program
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.openlocfilehash: bc50ec86866b7fe04c549c7fd463b6de4df3444b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76698398"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Så här hittar du ett specifikt API som behövs för ett specialutvecklat program

Åtkomst till API:er kräver konfiguration av åtkomstomfattningar och roller. Om du vill exponera webb-API:er för resursprogram för klientprogram måste du konfigurera åtkomstomfattningar och roller för API:et. Om du vill att ett klientprogram ska komma åt ett webb-API måste du konfigurera behörigheter för att komma åt API:et i appregistreringen.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Konfigurera resursprogram för att göra webb API:er tillgängliga

När du exponerar webb-API:et visas API:et i listan **Välj ett API** när du lägger till behörigheter i en appregistrering. Om du vill lägga till åtkomstomfattningar följer du stegen i [Konfigurera ett program för att exponera webb-API:er](quickstart-configure-app-expose-web-apis.md).

## <a name="configuring-a-client-application-to-access-web-apis"></a>Konfigurera ett klientprogram för åtkomst till webb-API:er

När du lägger till behörigheter i appregistreringen kan du **lägga till API-åtkomst** till exponerade webb-API:er. Om du vill komma åt webb-API:er följer du stegen i [Konfigurera ett klientprogram för att komma åt webb-API:er](quickstart-configure-app-access-web-apis.md).

## <a name="next-steps"></a>Nästa steg

- [Förstå Azure Active Directory-programmanifestet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)
