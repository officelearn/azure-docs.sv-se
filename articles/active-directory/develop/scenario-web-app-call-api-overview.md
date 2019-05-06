---
title: 'Webbapp som anropar webb-API: er (översikt) - Microsoft identity-plattformen'
description: 'Lär dig att skapa en webbapp som anropar webb-API: er (översikt)'
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
ms.openlocfilehash: ce45f11a697b72ebdd0fe01166a70e7b47aa8e9f
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076307"
---
# <a name="scenario-web-app-that-calls-web-apis"></a>Scenario: Webbapp som anropar webb-API: er

Lär dig hur du skapar en webbapp användare logga in på Microsoft identity-plattformen och som anropar webb-API: er åt den inloggade användaren.

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

Det här scenariot supposes att du har gått igenom följande scenario:

> [!div class="nextstepaction"]
> [Webbapp som loggar in användare](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Översikt

Du lägger till autentisering till din Webbapp som därför kan logga in användare och anropar ett webb-API för den inloggade användaren räkning.

![Webbapp som anropar webb-API: er](./media/scenario-webapp/web-app.svg)

Web Apps som anropar webb-API: er:

- är konfidentiell klientprogram.
- Det är därför de har registrerat en hemlighet (programlösenord eller certifikat) med Azure AD. Den här hemligheten är skickades i under anropet till Azure AD för att hämta en token

## <a name="specifics"></a>Närmare information

> [!NOTE]
> Att lägga till inloggning till en Webbapp använder inte MSAL bibliotek eftersom det här är om hur du skyddar Webbappen. Skydda bibliotek uppnås genom bibliotek med namnet mellanprogram. Det var objektet av scenariot ovan [användare logga in till en Webbapp](scenario-web-app-sign-user-overview.md)
>
> När anropa webb-API: er från en Webbapp, kommer du behöva hämta åtkomsttoken för dessa webb-API: er. Du kan använda MSAL bibliotek för att hämta dessa token.

Slutpunkt till slutpunkt-upplevelse för utvecklare för det här scenariot har därför specifika aspekter som:

- Under den [programregistrering](scenario-web-app-call-api-app-registration.md), måste du har angett en eller flera (om du distribuerar din app till flera platser) svars-URI: er, hemligheter eller certifikat behöver delas med Azure AD.
- Den [programkonfiguration](scenario-web-app-call-api-app-configuration.md) måste ange klientautentiseringsuppgifter som delad med Azure AD under registreringen program

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Appregistrering](scenario-web-app-call-api-app-registration.md)
