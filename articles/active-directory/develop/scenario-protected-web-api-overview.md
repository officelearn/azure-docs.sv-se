---
title: Protected Web API – översikt
titleSuffix: Microsoft identity platform
description: Lär dig hur du skapar en skyddad webb-API (översikt).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: cf66757d28a3883664aaacd85baad9cc0dea6956
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "81537210"
---
# <a name="scenario-protected-web-api"></a>Scenario: skyddat webb-API

I det här scenariot får du lära dig hur du exponerar ett webb-API. Du lär dig också hur du skyddar webb-API: et så att endast autentiserade användare kan komma åt det.

Om du vill använda ditt webb-API måste du antingen aktivera autentiserade användare med både arbets-och skol konton eller aktivera Microsoft personliga konton.

## <a name="prerequisites"></a>Krav

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Information

Här är en detaljerad information som du behöver känna till för att skydda webb-API: er:

- Din app-registrering måste exponera minst ett omfång. Den token-version som accepteras av ditt webb-API är beroende av inloggnings mål gruppen.
- Kod konfigurationen för webb-API: n måste validera den token som används när webb-API: t anropas.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Appregistrering](scenario-protected-web-api-app-registration.md)
