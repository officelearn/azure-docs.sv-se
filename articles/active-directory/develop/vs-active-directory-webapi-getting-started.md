---
title: Kom igång med Azure AD i Visual Studio-WebApi-projekt
description: Hur du kommer igång med Azure Active Directory i WebApi-projekt när du ansluter till eller skapa en Azure AD med hjälp av Visual Studio-anslutna tjänster
services: active-directory
author: ghogen
manager: douge
ms.assetid: bf1eb32d-25cd-4abf-8679-2ead299fedaa
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: 662d958fc7ef6fe5c9d0e61a1d8e48983d10196f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56198804"
---
# <a name="get-started-with-azure-active-directory-webapi-projects"></a>Kom igång med Azure Active Directory (WebApi-projekt)

> [!div class="op_single_selector"]
> - [Komma igång](vs-active-directory-webapi-getting-started.md)
> - [Vad hände](vs-active-directory-webapi-what-happened.md)

Den här artikeln innehåller ytterligare vägledning när du har lagt till Active Directory till ett ASP.NET-WebAPI-projekt i den **projekt > Connected Services** kommandot av Visual Studio. Om du inte redan har lagt till tjänsten i projektet kan du göra det när som helst.

Se [vad hände med mitt WebAPI-projekt?](vs-active-directory-webapi-what-happened.md) för ändringar som görs i ditt projekt när du lägger till den anslutna tjänsten.

## <a name="requiring-authentication-to-access-controllers"></a>Som kräver autentisering till åtkomst domänkontrollanter

Alla domänkontrollanter i ditt projekt har adorned med den `[Authorize]` attribut. Det här attributet kräver att användaren autentiseras innan du använder API: er som definieras av dessa domänkontrollanter. Ta bort skrivskyddsattributet från kontrollanten för att tillåta den kontrollenheten för att användas anonymt. Om du vill ange behörigheter på en mer detaljerad nivå gäller attributet för varje metod som kräver auktorisering i stället för den tillämpas på klassen controller.

## <a name="next-steps"></a>Nästa steg

- [Autentiseringsscenarier för Azure Active Directory](authentication-scenarios.md)
- [Lägga till logga in med Microsoft i en ASP.NET-webbapp](quickstart-v1-aspnet-webapp.md)
