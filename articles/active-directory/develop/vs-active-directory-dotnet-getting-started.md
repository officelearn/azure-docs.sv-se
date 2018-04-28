---
title: Kom igång med Azure AD i Visual Studio .NET MVC-projekt
description: Hur du kommer igång med Azure Active Directory i .NET MVC-projekt när du ansluter till eller skapa en Azure AD med hjälp av Visual Studio anslutna tjänster
services: active-directory
author: ghogen
manager: douge
ms.assetid: 1c8b6a58-5144-4965-a905-625b9ee7b22b
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev
ms.openlocfilehash: d521b8c85fb66b6c50d1b9f07e5f4d653e9e57b9
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="getting-started-with-azure-active-directory-aspnet-mvc-projects"></a>Komma igång med Azure Active Directory (ASP.NET MVC-projekt)

> [!div class="op_single_selector"]
> - [Komma igång](vs-active-directory-dotnet-getting-started.md)
> - [Vad hände](vs-active-directory-dotnet-what-happened.md)

Den här artikeln innehåller ytterligare vägledning när du har lagt till Active Directory till ett ASP.NET MVC-projektet till den **Project > Connected Services** kommandot av Visual Studio. Om du inte redan har lagt till tjänsten till ditt projekt kan du göra det när som helst.

Se [vad hände med min MVC-projektet?](vs-active-directory-dotnet-what-happened.md) för ändringar i ditt projekt när du lägger till tjänsten anslutna.

## <a name="requiring-authentication-to-access-controllers"></a>Kräva autentisering till access-domänkontrollanter

Alla domänkontrollanter i ditt projekt har adorned med den `[Authorize]` attribut. Det här attributet kräver att användaren autentiseras innan du använder dessa domänkontrollanter. Ta bort skrivskyddsattributet från styrenheten för att tillåta att kontrollanten kan användas anonymt. Om du vill ange behörigheter på en mer detaljerad nivå gäller attributet för varje metod som kräver tillstånd i stället för att tillämpas på klassen domänkontrollant.

## <a name="adding-signin--signout-controls"></a>Lägga till inloggning / utloggning styr

Om du vill lägga till inloggning/utloggning kontroller i vyn kan du använda den `_LoginPartial.cshtml` partiella vyn för att lägga till funktionen i en av dina vyer. Här är ett exempel på funktioner som lagts till i vanlig `_Layout.cshtml` vyn. (Observera det sista elementet i div med klassen navigeringsfält för komprimera):

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

- [Autentiseringsscenarier för Azure Active Directory](active-directory-authentication-scenarios.md)
- [Lägga till inloggning med Microsoft till ett ASP.NET-webbprogram](guidedsetups/active-directory-aspnetwebapp-v1.md)
