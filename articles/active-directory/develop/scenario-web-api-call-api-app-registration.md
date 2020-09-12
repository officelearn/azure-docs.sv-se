---
title: 'Registrera ett webb-API som anropar webb-API: er – Microsoft Identity Platform | Azure'
description: 'Lär dig hur du skapar ett webb-API som anropar underordnade webb-API: er (app Registration).'
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
ms.openlocfilehash: c0b009d6d53a117aa11d0629fb649b2dd55559af
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/03/2020
ms.locfileid: "89438202"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>Ett webb-API som anropar webb-API: app-registrering

Ett webb-API som anropar underordnade webb-API: er har samma registrering som en skyddad webb-API. Du måste därför följa instruktionerna i [Protected Web API: app Registration](scenario-protected-web-api-app-registration.md).

Eftersom webbappen nu anropar webb-API: er blir det ett konfidentiellt klient program. Det är anledningen till att extra registrerings information krävs: appen måste dela hemligheter (klientautentiseringsuppgifter) med Microsoft Identity Platform.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API-behörigheter

Web Apps anropar API: er för användare för vilka Bearer-token togs emot. Webbapparna måste begära delegerade behörigheter. Mer information finns i [lägga till behörigheter för åtkomst till ditt webb-API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ett webb-API som anropar webb-API: er kod konfiguration](scenario-web-api-call-api-app-configuration.md)
