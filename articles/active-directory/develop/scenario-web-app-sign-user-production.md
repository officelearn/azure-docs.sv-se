---
title: Webbapp som loggar in användare (flytta till produktion) - Microsoft identity-plattformen
description: Lär dig att skapa en webbapp som loggar in användare (flytta till produktion)
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
ms.openlocfilehash: d41ad2518f885bbaa02dda3b01f0c02e9fc1d217
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074717"
---
# <a name="web-app-that-signs-in-users---move-to-production"></a>Webbapp som loggar in användare – flytta till produktion

Nu när du vet hur du skaffa en token för att anropa webb-API: er lär du dig hur du flyttar det till produktion.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Nästa steg

### <a name="calling-web-apis-scenario"></a>Anropa webb-API: er scenario

När dina web app loggar in användare, kan det anropa webb-API: er åt de inloggade användarna. Anropa webb API: er från webbapp är objekt av följande scenario:

> [!div class="nextstepaction"]
> [Webbapp som anropar webb-API: er](scenario-web-app-call-api-overview.md)

### <a name="deep-dive---web-app-tutorial"></a>Djupdykning - självstudier om webbappen

Lär dig mer om andra sätt att logga in användare med ASP.NET Core-självstudie: [ms-identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial). Det här är en progressiv självstudiekurs i produktionskoden redo för en webbapp, inklusive hur du lägger till inloggningen.

<!--- Removed the diagram as it's already shown in the above link to GitHub

![Tutorial overview](media/scenarios/aspnetcore-webapp-tutorial.svg)

--->
