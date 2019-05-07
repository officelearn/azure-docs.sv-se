---
title: Skyddade webb-API - översikt | Azure
description: Lär dig hur du skapar ett skyddat webb-API (översikt).
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5bc0075e6604bd1f94531040f2e4a0628e70667
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074897"
---
# <a name="scenario-protected-web-api"></a>Scenario: Skyddade webb-API

I det här scenariot får du lära dig hur du kan visa ett webb-API och hur du kan skydda den så att endast behöriga användare har åtkomst till API: et. Du vill aktivera autentiserade användare med både arbete och skolkonton eller personliga personliga Microsoft-konton att använda ditt webb-API.

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Närmare information

Här följer några ge specifik information som du behöver veta för att skydda webb-API: er:

- Din appregistrering måste exponera minst ett omfång. Den token versionen som accepteras av ditt webb-API är beroende av inloggningen målgrupp.
- Konfigurationen av koden för webb-API måste validera token som används när du anropar webb-API.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Appregistrering](scenario-protected-web-api-app-registration.md)
