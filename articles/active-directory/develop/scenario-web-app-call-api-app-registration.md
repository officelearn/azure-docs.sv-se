---
title: 'Registrera en webbapp som anropar webb-API: er – Microsoft Identity Platform | Azure'
description: 'Lär dig hur du registrerar en webbapp som anropar webb-API: er'
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
ms.openlocfilehash: bcb4fa5eb5889698174b65eb576c9caf0c82f857
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701679"
---
# <a name="web-app-that-calls-web-apis---app-registration"></a>Webbapp som anropar webb-API: er – app-registrering

En webbapp som anropar webb-API: er har samma registrering som inloggnings användare för webbappar. Du måste därför följa instruktionerna i [webbappen som loggar in användare – app-registrering](scenario-web-app-sign-user-app-registration.md)

Men eftersom webbappen nu anropar webb-API: er blir det ett konfidentiellt klient program. Det är därför en extra registrering krävs: den måste dela hemligheter (klientautentiseringsuppgifterna) med Microsoft Identity Platform.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API-behörigheter

Webb program anropar API: er åt den inloggade användaren. De måste begära delegerade behörigheter. Mer information finns i [lägga till behörigheter för åtkomst till webb-API: er](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Appens kod konfiguration](scenario-web-app-call-api-app-configuration.md)
