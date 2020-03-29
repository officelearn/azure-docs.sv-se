---
title: 'Registrera ett webb-API som anropar webb-API: er - Microsoft identity platform | Azure'
description: Lär dig hur du skapar ett webb-API som anropar underordnade webb-API:er (appregistrering).
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
ms.openlocfilehash: bafd71f34602535bb6193a8d8114a1182e4e8f40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701798"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>Ett webb-API som anropar webb-API:er: Appregistrering

Ett webb-API som anropar underordnade webb-API:er har samma registrering som ett skyddat webb-API. Därför måste du följa instruktionerna i [Skyddad webb-API: Appregistrering](scenario-protected-web-api-app-registration.md).

Eftersom webbappen nu anropar webb-API:er blir det ett konfidentiellt klientprogram. Därför krävs extra registreringsinformation: appen måste dela hemligheter (klientuppgifter) med Microsofts identitetsplattform.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API-behörigheter

Webbappar anropar API:er för användare för vilka innehavartoken togs emot. Webbapparna måste begära delegerade behörigheter. Mer information finns i [Lägga till behörigheter för åtkomst till webb-API:er](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ett webb-API som anropar webb-API:er: Kodkonfiguration](scenario-web-api-call-api-app-configuration.md)
