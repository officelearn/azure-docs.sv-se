---
title: Protected Web API – översikt | Azure
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02bd4b84cc7542714f6db45c12c4b5b13a7fb449
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852583"
---
# <a name="scenario-protected-web-api"></a>Scenario: Skyddat webb-API

I det här scenariot visar vi hur du kan exponera ett webb-API och hur du kan skydda det så att endast autentiserade användare får åtkomst till API: et. Du vill aktivera autentiserade användare med både arbets-och skol konton eller personliga personliga Microsoft-konton för att använda ditt webb-API.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Information

Här är några information som du behöver känna till för att skydda webb-API: er:

- Din app-registrering måste exponera minst ett omfång. Den token-version som accepteras av ditt webb-API är beroende av inloggnings mål gruppen.
- Konfigurationen av koden för webb-API: n måste validera den token som används vid anrop till webb-API: et.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Appregistrering](scenario-protected-web-api-app-registration.md)
