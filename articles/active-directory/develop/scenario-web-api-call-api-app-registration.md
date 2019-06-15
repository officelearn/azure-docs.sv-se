---
title: 'Webb-API som anrop underordnat webb API: er (appregistrering) - Microsoft identity-plattformen'
description: 'Lär dig hur du skapar ett webb-API som anrop underordnat webb API: er (appregistrering)'
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
ms.openlocfilehash: fb03869cdea2150b6e922e2d6d81e577c3be02da
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65075392"
---
# <a name="web-api-that-calls-web-apis---app-registration"></a>Webb-API att anrop webb-API: er – appregistrering

Ett webb-API som anropar underordnade webb-API: er har samma registreringen som ett skyddat webb-API. Därför måste du följa instruktionerna i [skyddade webb-API - appregistrering](scenario-protected-web-api-app-registration.md).

Men eftersom appen blir nu anropar webb-API: er, det ett konfidentiellt klientprogram. Det är därför det är extra registrering information som krävs: appen måste dela hemligheter (klientautentiseringsuppgifter) med Microsoft identity-plattformen.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API-behörigheter

Webbprogram anropa API: er åt den användaren som ägartoken togs emot. De måste begära delegerade behörigheter. Mer information finns i [Lägg till behörigheter för att få åtkomst till webb-API: er](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Appens kod konfiguration](scenario-web-api-call-api-app-configuration.md)
