---
title: Komma igång med Azure AD i .NET MVC-projekt | Azure
description: Komma igång med Azure Active Directory i .NET MVC-projekt efter anslutning till eller skapa en Azure AD med hjälp av Visual Studio-anslutna tjänster
author: ghogen
manager: jillfra
ms.assetid: 1c8b6a58-5144-4965-a905-625b9ee7b22b
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: eae649a4de88373ee79e49ecb7d5f14564a3054b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159496"
---
# <a name="getting-started-with-azure-active-directory-aspnet-mvc-projects"></a>Komma igång med Azure Active Directory (ASP.NET MVC-projekt)

> [!div class="op_single_selector"]
> - [Komma igång](vs-active-directory-dotnet-getting-started.md)
> - [Vad hände](vs-active-directory-dotnet-what-happened.md)

Den här artikeln innehåller ytterligare vägledning när du har lagt till Active Directory i ett ASP.NET MVC-projekt via kommandot **Project > Connected Services** i Visual Studio. Om du inte redan har lagt till tjänsten i projektet kan du göra det när som helst.

Se [Vad hände med mitt MVC-projekt?](vs-active-directory-dotnet-what-happened.md)

## <a name="requiring-authentication-to-access-controllers"></a>Kräver autentisering för åtkomstkontrollanter

Alla handkontroller i projektet pryddes med `[Authorize]` attributet. Det här attributet kräver att användaren autentiseras innan du öppnar dessa styrenheter. Om du vill att styrenheten ska kunna nås anonymt tar du bort attributet från styrenheten. Om du vill ange behörigheterna på en mer detaljerad nivå använder du attributet på varje metod som kräver auktorisering i stället för att tillämpa det på controller-klassen.

## <a name="adding-signin--signout-controls"></a>Lägga till signin- och signoutkontroller

Om du vill lägga till signin-/signout-kontrollerna i vyn kan du använda den `_LoginPartial.cshtml` partiella vyn för att lägga till funktionen i en av dina vyer. Här är ett exempel på de `_Layout.cshtml` funktioner som har lagts till i standardvyn. (Notera det sista elementet i div med klass navbar-kollaps):

```html
<!DOCTYPE html>
 <html>
 <head>
     <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>@ViewBag.Title - My ASP.NET Application</title>
    @Styles.Render("~/Content/css")
    @Scripts.Render("~/bundles/modernizr")
</head>
<body>
    <div class="navbar navbar-inverse navbar-fixed-top">
        <div class="container">
            <div class="navbar-header">
                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                </button>
                @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
            </div>
            <div class="navbar-collapse collapse">
                <ul class="nav navbar-nav">
                    <li>@Html.ActionLink("Home", "Index", "Home")</li>
                    <li>@Html.ActionLink("About", "About", "Home")</li>
                    <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
                </ul>
                @Html.Partial("_LoginPartial")
            </div>
        </div>
    </div>
    <div class="container body-content">
        @RenderBody() 
        <hr />
        <footer>
            <p>&copy; @DateTime.Now.Year - My ASP.NET Application</p>
        </footer>
    </div>
    @Scripts.Render("~/bundles/jquery")
    @Scripts.Render("~/bundles/bootstrap")
    @RenderSection("scripts", required: false)
</body>
</html>
```

## <a name="next-steps"></a>Nästa steg

- [Autentiseringsscenarier för Azure Active Directory](authentication-scenarios.md)
- [Lägga till inloggning med Microsoft till en ASP.NET-webbapp](quickstart-v2-aspnet-webapp.md)
