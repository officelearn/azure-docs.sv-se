---
title: Komma igång med Azure AD i Visual Studio WebApi-projekt
description: Komma igång med Azure Active Directory i WebApi-projekt efter anslutning till eller skapa en Azure AD med hjälp av Visual Studio-anslutna tjänster
author: ghogen
manager: jillfra
ms.assetid: bf1eb32d-25cd-4abf-8679-2ead299fedaa
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 33894e237144628634c3b63393130eb0af5b9877
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159462"
---
# <a name="get-started-with-azure-active-directory-webapi-projects"></a>Komma igång med Azure Active Directory (WebApi-projekt)

> [!div class="op_single_selector"]
> - [Komma igång](vs-active-directory-webapi-getting-started.md)
> - [Vad hände](vs-active-directory-webapi-what-happened.md)

Den här artikeln innehåller ytterligare vägledning när du har lagt till Active Directory i ett ASP.NET WebAPI-projekt via kommandot **Project > Connected Services** i Visual Studio. Om du inte redan har lagt till tjänsten i projektet kan du göra det när som helst.

Se [Vad hände med mitt WebAPI-projekt?](vs-active-directory-webapi-what-happened.md)

## <a name="requiring-authentication-to-access-controllers"></a>Kräver autentisering för åtkomstkontrollanter

Alla handkontroller i projektet pryddes med `[Authorize]` attributet. Det här attributet kräver att användaren autentiseras innan du öppnar API:erna som definieras av dessa styrenheter. Om du vill att styrenheten ska kunna nås anonymt tar du bort attributet från styrenheten. Om du vill ange behörigheterna på en mer detaljerad nivå använder du attributet på varje metod som kräver auktorisering i stället för att tillämpa det på controller-klassen.

## <a name="next-steps"></a>Nästa steg

- [Autentiseringsscenarier för Azure Active Directory](authentication-scenarios.md)
- [Lägga till inloggning med Microsoft till en ASP.NET-webbapp](quickstart-v2-aspnet-webapp.md)
