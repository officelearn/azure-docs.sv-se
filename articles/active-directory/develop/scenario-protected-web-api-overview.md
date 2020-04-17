---
title: Skyddat webb-API - översikt
titleSuffix: Microsoft identity platform
description: Lär dig hur du skapar ett skyddat webb-API (översikt).
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
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537210"
---
# <a name="scenario-protected-web-api"></a>Scenario: Skyddat webb-API

I det här fallet får du lära dig hur du exponerar ett webb-API. Du lär dig också hur du skyddar webb-API:et så att endast autentiserade användare kan komma åt det.

Om du vill använda webb-API:et måste du antingen aktivera autentiserade användare med både arbets- och skolkonton eller aktivera Microsofts personliga konton.

## <a name="prerequisites"></a>Krav

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Detaljerna

Här är specifik information du behöver veta för att skydda webb-API:er:

- Din appregistrering måste visa minst ett scope. Vilken tokenversion som accepteras av webb-API:et beror på inloggningspubliken.
- Kodkonfigurationen för webb-API:et måste validera den token som används när webb-API:et anropas.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Appregistrering](scenario-protected-web-api-app-registration.md)
