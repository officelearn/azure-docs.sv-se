---
title: Kom igång med Azure AD med Visual Studio .NET MVC-projekt
description: Hur du kommer igång med Azure Active Directory i .NET MVC-projekt när du ansluter till eller skapa en Azure AD med hjälp av Visual Studio-anslutna tjänster
services: active-directory
author: ghogen
manager: douge
ms.assetid: 1c8b6a58-5144-4965-a905-625b9ee7b22b
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 437cb8b26cb33dfbb9c259e2842a59a765b78903
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2018
ms.locfileid: "42056775"
---
# <a name="getting-started-with-azure-active-directory-aspnet-mvc-projects"></a>Komma igång med Azure Active Directory (ASP.NET MVC-projekt)

> [!div class="op_single_selector"]
> - [Komma igång](vs-active-directory-dotnet-getting-started.md)
> - [Vad hände](vs-active-directory-dotnet-what-happened.md)

Den här artikeln innehåller ytterligare vägledning när du har lagt till Active Directory till ett ASP.NET MVC-projekt i den **projekt > Connected Services** kommandot av Visual Studio. Om du inte redan har lagt till tjänsten i projektet kan du göra det när som helst.

Se [vad hände med mitt MVC-projekt?](vs-active-directory-dotnet-what-happened.md) för ändringar som görs i ditt projekt när du lägger till den anslutna tjänsten.

## <a name="requiring-authentication-to-access-controllers"></a>Som kräver autentisering till åtkomst domänkontrollanter

Alla domänkontrollanter i ditt projekt har adorned med den `[Authorize]` attribut. Det här attributet kräver att användaren autentiseras innan du använder dessa domänkontrollanter. Ta bort skrivskyddsattributet från kontrollanten för att tillåta den kontrollenheten för att användas anonymt. Om du vill ange behörigheter på en mer detaljerad nivå gäller attributet för varje metod som kräver auktorisering i stället för den tillämpas på klassen controller.

## <a name="adding-signin--signout-controls"></a>Att lägga till inloggning / utloggning styr

Du kan använda för att lägga till inloggning/utloggning kontroller i vyn i `_LoginPartial.cshtml` partiella vy för att lägga till funktioner till en av dina vyer. Här är ett exempel på funktioner som lagts till i standard `_Layout.cshtml` vy. (Observera att det sista elementet i div med klassen navigeringsfältet komprimera):

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
- [Lägga till logga in med Microsoft i en ASP.NET-webbapp](quickstart-v1-aspnet-webapp.md)
