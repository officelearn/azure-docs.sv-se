---
title: "Kom igång med Azure AD i Visual Studio MVC projekt | Microsoft Docs"
description: "Hur du kommer igång med Azure Active Directory i MVC projekt efter anslutning till eller skapa en Azure AD med hjälp av Visual Studio anslutna tjänster"
services: active-directory
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 1c8b6a58-5144-4965-a905-625b9ee7b22b
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: c4d49cfc9887e422b3eaed2b96348c99eca48881
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-azure-active-directory-and-visual-studio-connected-services-mvc-projects"></a>Komma igång med Azure Active Directory och Visual Studio anslutna tjänster (MVC-projekt)
> [!div class="op_single_selector"]
> * [Komma igång](vs-active-directory-dotnet-getting-started.md)
> * [Vad hände](vs-active-directory-dotnet-what-happened.md)
> 
> 

## <a name="requiring-authentication-to-access-controllers"></a>Kräva autentisering till access-domänkontrollanter
Alla domänkontrollanter i ditt projekt har adorned med den **auktorisera** attribut. Det här attributet kräver att användaren autentiseras innan du använder dessa domänkontrollanter. Ta bort skrivskyddsattributet från styrenheten för att tillåta att kontrollanten kan användas anonymt. Om du vill ange behörigheter på en mer detaljerad nivå gäller attributet för varje metod som kräver tillstånd i stället för att tillämpas på klassen domänkontrollant.

## <a name="adding-signin--signout-controls"></a>Lägga till inloggning / utloggning styr
Om du vill lägga till inloggning/utloggning kontroller i vyn kan du använda den **_LoginPartial.cshtml** partiella vyn för att lägga till funktionen i en av dina vyer. Här är ett exempel på funktioner som lagts till i vanlig **_Layout.cshtml** vyn. (Observera det sista elementet i div med klassen navigeringsfält för komprimera):

<pre>
    &lt;!DOCTYPE html&gt; 
     &lt;html&gt; 
     &lt;head&gt; 
         &lt;meta charset="utf-8" /&gt; 
        &lt;meta name="viewport" content="width=device-width, initial-scale=1.0"&gt; 
        &lt;title&gt;@ViewBag.Title - My ASP.NET Application&lt;/title&gt; 
        @Styles.Render("~/Content/css") 
        @Scripts.Render("~/bundles/modernizr") 
    &lt;/head&gt; 
    &lt;body&gt; 
        &lt;div class="navbar navbar-inverse navbar-fixed-top"&gt; 
            &lt;div class="container"&gt; 
                &lt;div class="navbar-header"&gt; 
                    &lt;button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse"&gt; 
                        &lt;span class="icon-bar"&gt;&lt;/span&gt; 
                        &lt;span class="icon-bar"&gt;&lt;/span&gt; 
                        &lt;span class="icon-bar"&gt;&lt;/span&gt; 
                    &lt;/button&gt; 
                    @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" }) 
                &lt;/div&gt; 
                &lt;div class="navbar-collapse collapse"&gt; 
                    &lt;ul class="nav navbar-nav"&gt; 
                        &lt;li&gt;@Html.ActionLink("Home", "Index", "Home")&lt;/li&gt; 
                        &lt;li&gt;@Html.ActionLink("About", "About", "Home")&lt;/li&gt; 
                        &lt;li&gt;@Html.ActionLink("Contact", "Contact", "Home")&lt;/li&gt; 
                    &lt;/ul&gt; 
                    <span style="background-color:yellow">@Html.Partial("_LoginPartial")</span> 
                &lt;/div&gt; 
            &lt;/div&gt; 
        &lt;/div&gt; 
        &lt;div class="container body-content"&gt; 
            @RenderBody() 
            &lt;hr /&gt; 
            &lt;footer&gt; 
                &lt;p&gt;&amp;copy; @DateTime.Now.Year - My ASP.NET Application&lt;/p&gt; 
            &lt;/footer&gt; 
        &lt;/div&gt; 
        @Scripts.Render("~/bundles/jquery") 
        @Scripts.Render("~/bundles/bootstrap") 
        @RenderSection("scripts", required: false) 
    &lt;/body&gt; 
    &lt;/html&gt;
</pre>

## <a name="next-steps"></a>Nästa steg
- [Lär dig mer om Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 

