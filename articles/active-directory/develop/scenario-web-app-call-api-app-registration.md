---
title: 'Registrera en webbapp som anropar webb-API: er – Microsoft Identity Platform | Azure'
description: 'Lär dig hur du registrerar en webbapp som anropar webb-API: er'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 8cb7d86bd419563363779c499962c81f0c59e3b6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80881884"
---
# <a name="a-web-app-that-calls-web-apis-app-registration"></a>En webbapp som anropar webb-API: app-registrering

En webbapp som anropar webb-API: er har samma registrering som en webbapp som loggar användare i. Följ sedan anvisningarna i [en webbapp som loggar in användare: app-registrering](scenario-web-app-sign-user-app-registration.md).

Men även om webbappen nu anropar webb-API: er blir det ett konfidentiellt klient program. Därför krävs en extra registrering. Appen måste dela klientautentiseringsuppgifterna, eller *hemligheter*, med Microsoft Identity Platform.

[!INCLUDE [Registration of client secrets](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API-behörigheter

Web Apps anropar API: er för den inloggade användaren. För att göra det måste de begära *delegerade behörigheter*. Mer information finns i [lägga till behörigheter för åtkomst till webb-API: er](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [En webbapp som anropar webb-API: er kod konfiguration](scenario-web-app-call-api-app-configuration.md)
