---
title: 'Registrera ett webb-API som anropar webb-API: er – Microsoft Identity Platform | Azure'
description: 'Lär dig hur du skapar ett webb-API som anropar underordnade webb-API: er (app Registration).'
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
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701798"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>Ett webb-API som anropar webb-API: app-registrering

Ett webb-API som anropar underordnade webb-API: er har samma registrering som en skyddad webb-API. Du måste därför följa instruktionerna i [Protected Web API: app Registration](scenario-protected-web-api-app-registration.md).

Eftersom webbappen nu anropar webb-API: er blir det ett konfidentiellt klient program. Det är anledningen till att extra registrerings information krävs: appen måste dela hemligheter (klientautentiseringsuppgifter) med Microsoft Identity Platform.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API-behörigheter

Web Apps anropar API: er för användare för vilka Bearer-token togs emot. Webbapparna måste begära delegerade behörigheter. Mer information finns i [lägga till behörigheter för åtkomst till webb-API: er](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ett webb-API som anropar webb-API: er kod konfiguration](scenario-web-api-call-api-app-configuration.md)
