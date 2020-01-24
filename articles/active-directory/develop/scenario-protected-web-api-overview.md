---
title: Protected Web API – översikt
titleSuffix: Microsoft identity platform
description: Lär dig hur du skapar en skyddad webb-API (översikt).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: f96393adf0eaed8a28bc1a2ec0def6b0386bd7ac
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701985"
---
# <a name="scenario-protected-web-api"></a>Scenario: skyddat webb-API

I det här scenariot visar vi hur du kan exponera ett webb-API och hur du kan skydda det så att endast autentiserade användare får åtkomst till API: et. Du vill aktivera autentiserade användare med både arbets-och skol konton eller personliga personliga Microsoft-konton för att använda ditt webb-API.

## <a name="prerequisites"></a>Krav

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Information

Här är några information som du behöver känna till för att skydda webb-API: er:

- Din app-registrering måste exponera minst ett omfång. Den token-version som accepteras av ditt webb-API är beroende av inloggnings mål gruppen.
- Konfigurationen av koden för webb-API: n måste validera den token som används vid anrop till webb-API: et.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Appregistrering](scenario-protected-web-api-app-registration.md)
