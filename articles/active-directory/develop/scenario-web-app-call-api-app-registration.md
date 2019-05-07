---
title: 'Webbapp som anropar webb-API: er (appregistrering) - Microsoft identity-plattformen'
description: 'Lär dig att skapa en webbapp som anropar webb-API: er (appregistrering)'
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
ms.openlocfilehash: 5becdc287f7cad28aa6c7c07ebc107586e9a2b2a
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075197"
---
# <a name="web-app-that-calls-web-apis---app-registration"></a>Webbapp som anropar webb-API: er – appregistrering

En Web app anropande webb API: er har samma registreringen som en Web App logga in användare. Du måste därför följer du anvisningarna i [Webbapp som loggar in användare - appregistrering](scenario-web-app-sign-user-app-registration.md)

Men eftersom appen nu anropar webb-API: er, blir det ett konfidentiellt klientprogram. Det är därför det är lite extra registrering krävs: den behöver dela hemligheter (klientautentiseringsuppgifter) med Microsoft identity-plattformen.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API-behörigheter

Webbprogram anropa API: er åt den inloggade användaren. De måste begära delegerade behörigheter. Mer information finns i [Lägg till behörigheter för att få åtkomst till webb-API: er](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Appens kod konfiguration](scenario-web-app-call-api-app-configuration.md)
