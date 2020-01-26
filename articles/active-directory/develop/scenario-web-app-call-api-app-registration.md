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
ms.openlocfilehash: 5a57fcef3569734964bf6e8a41faa49800798f9b
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759065"
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
