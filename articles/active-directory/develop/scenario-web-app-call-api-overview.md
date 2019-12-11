---
title: 'Bygg en webbapp som anropar webb-API: er – Microsoft Identity Platform | Azure'
description: 'Lär dig hur du skapar en webbapp som anropar webb-API: er (översikt)'
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
ms.openlocfilehash: 3888c7f838d6009382f849bc7d3e34c49b3b70a4
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962142"
---
# <a name="scenario-web-app-that-calls-web-apis"></a>Scenario: webb program som anropar webb-API: er

Lär dig hur du skapar en webbapp som loggar in användare på Microsoft Identity Platform och anropar webb-API: er åt den inloggade användaren.

## <a name="prerequisites"></a>Krav

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

Det här scenariot förutsätter att du har gått igenom följande scenario:

> [!div class="nextstepaction"]
> [Webbapp som loggar in användare](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Översikt

Du lägger till autentisering till din webbapp, som därför kan logga in användare och anropa ett webb-API för den inloggade användarens räkning.

![Webbapp som anropar webb-API:er](./media/scenario-webapp/web-app.svg)

Web Apps som anropar webb-API: er:

- är konfidentiella klient program.
- Därför har de registrerat en hemlighet (program lösen ord eller certifikat) med Azure AD. Den här hemligheten har överförts under anropet till Azure AD för att hämta en token

## <a name="specifics"></a>Information

> [!NOTE]
> När du lägger till inloggning i en webbapp används inte MSAL-biblioteken eftersom detta är att skydda webbappen. Att skydda bibliotek uppnås av bibliotek som heter mellanprogram. Detta var objektet i föregående scenario [inloggnings användare till en webbapp](scenario-web-app-sign-user-overview.md)
>
> När du anropar webb-API: er från en webbapp måste du hämta åtkomsttoken för dessa webb-API: er. Du kan använda MSAL-bibliotek för att hämta dessa tokens.

Slutpunkten för utvecklare i det här scenariot har därför särskilda aspekter som:

- Under [program registreringen](scenario-web-app-call-api-app-registration.md)måste du ange en eller flera (om du distribuerar din app till flera platser) svars-URI: er, hemligheter eller certifikat måste delas med Azure AD.
- [Program konfigurationen](scenario-web-app-call-api-app-configuration.md) måste ange klientautentiseringsuppgifter som delad med Azure AD under program registreringen

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Appregistrering](scenario-web-app-call-api-app-registration.md)
