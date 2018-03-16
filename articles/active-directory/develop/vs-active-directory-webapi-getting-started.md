---
title: "Kom igång med Azure AD i Visual Studio-WebApi projekt | Microsoft Docs"
description: "Hur du kommer igång med Azure Active Directory i WebApi projekt när du ansluter till eller skapa en Azure AD med hjälp av Visual Studio anslutna tjänster"
services: active-directory
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: bf1eb32d-25cd-4abf-8679-2ead299fedaa
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: a5953a8ae4ef3fc221c984cc7520422251ef2c82
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-azure-active-directory-webapi-projects"></a>Kom igång med Azure Active Directory (WebApi-projekt)

> [!div class="op_single_selector"]
> - [Komma igång](vs-active-directory-webapi-getting-started.md)
> - [Vad hände](vs-active-directory-webapi-what-happened.md)

Den här artikeln innehåller ytterligare vägledning när du har lagt till Active Directory till ett ASP.NET-WebAPI-projekt i den **Project > Connected Services** kommandot av Visual Studio. Om du inte redan har lagt till tjänsten till ditt projekt kan du göra det när som helst.

Se [vad hände med WebAPI projektet?](vs-active-directory-webapi-what-happened.md) för ändringar i ditt projekt när du lägger till tjänsten anslutna.

## <a name="requiring-authentication-to-access-controllers"></a>Kräva autentisering till access-domänkontrollanter

Alla domänkontrollanter i ditt projekt har adorned med den `[Authorize]` attribut. Det här attributet kräver att användaren autentiseras innan du använder API: er som definieras av dessa domänkontrollanter. Ta bort skrivskyddsattributet från styrenheten för att tillåta att kontrollanten kan användas anonymt. Om du vill ange behörigheter på en mer detaljerad nivå gäller attributet för varje metod som kräver tillstånd i stället för att tillämpas på klassen domänkontrollant.

## <a name="next-steps"></a>Nästa steg

- [Autentiseringsscenarier för Azure Active Directory](active-directory-authentication-scenarios.md)
- [Lägga till inloggning med Microsoft till ett ASP.NET-webbprogram](guidedsetups/active-directory-aspnetwebapp-v1.md)
