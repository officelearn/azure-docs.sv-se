---
title: Hitta ett API för en anpassad-utvecklad app | Azure
description: Så här konfigurerar du de behörigheter du behöver för att komma åt ett visst API i ditt anpassade Azure AD-program
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.openlocfilehash: 8c3d79211ce594ec4c175aa6f6b5f8df3a7c4ba8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88115551"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Så här hittar du ett specifikt API som krävs för ett anpassat, utvecklat program

Åtkomst till API: er kräver konfiguration av åtkomstscope och roller. Om du vill exponera dina resurs program webb-API: er för klient program måste du konfigurera åtkomstscope och roller för API: et. Om du vill att ett klient program ska ha åtkomst till ett webb-API måste du konfigurera behörigheter för åtkomst till API: et i appens registrering.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Konfigurera resursprogram för att göra webb API:er tillgängliga

När du exponerar ditt webb-API visas API: et i listan **Välj en API** när du lägger till behörigheter till en app-registrering. Om du vill lägga till åtkomstscope följer du stegen som beskrivs i [Konfigurera ett program för att exponera webb-API: er](quickstart-configure-app-expose-web-apis.md).

## <a name="configuring-a-client-application-to-access-web-apis"></a>Konfigurera ett klient program för åtkomst till webb-API: er

När du lägger till behörigheter till din app-registrering kan du **lägga till API-åtkomst** till exponerade webb-API: er. För att få åtkomst till webb-API: er följer du stegen som beskrivs i [Konfigurera ett klient program för åtkomst till webb-API: er](quickstart-configure-app-access-web-apis.md).

## <a name="next-steps"></a>Nästa steg

- [Förstå Azure Active Directory applikations manifestet](./reference-app-manifest.md)