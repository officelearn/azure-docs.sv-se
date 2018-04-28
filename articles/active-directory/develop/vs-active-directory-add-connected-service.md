---
title: Lägga till ett Azure Active Directory med anslutna tjänster i Visual Studio
description: Lägg till ett Azure Active Directory med hjälp av dialogrutan Visual Studio Lägg till anslutna tjänster
services: active-directory
author: ghogen
manager: douge
ms.assetid: f599de6b-e369-436f-9cdc-48a0165684cb
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.openlocfilehash: a87ed8630f86cf004a05cdb2ae8b34c479cdaf32
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="adding-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Lägga till ett Azure Active Directory med anslutna tjänster i Visual Studio

Med hjälp av Azure Active Directory (Azure AD) kan stöder du enkel inloggning (SSO) för ASP.NET MVC-webbprogram och Active Directory-autentisering i Web API-tjänster. Användarna kan använda sina konton från Azure Active Directory för att ansluta till ditt webbprogram med Azure AD-autentisering. Fördelarna med Azure AD-autentisering med Web API är förbättrad datasäkerhet när exponera en API från ett webbprogram. Du behöver inte hantera en separat autentiseringssystemet med ett eget konto och användaren management med Azure AD.

Den här artikeln och dess tillhörande artiklar innehåller information för att använda funktionen Visual Studio ansluten Service för Active Directory. Funktionen finns i både 2017 för Visual Studio och Visual Studio 2015.

För närvarande stöder inte tjänsten Active Directory anslutna ASP.NET Core program.

## <a name="prerequisites"></a>Förutsättningar

- Azure-konto: Om du inte har ett Azure-konto kan du [registrera dig för en kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) eller [aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>Ansluta till Azure Active Directory genom att använda dialogrutan anslutna tjänster

1. Skapa eller öppna ett ASP.NET MVC-projekt eller ett ASP.NET Web API-projekt i Visual Studio. Du kan använda MVC, webb-API, sidan program, Azure API Apps, Azure Mobile Apps och mallar för Azure-mobiltjänst.

1. Välj den **Project > Lägg till anslutna tjänst...**  menyalternativ eller dubbelklicka på den **anslutna tjänster** nod hittades under projektet i Solution Explorer.

1. På den **anslutna tjänster** väljer **autentisering med Azure Active Directory**.

    ![Anslutna Tjänstesida](./media/vs-azure-active-directory/connected-services-add-active-directory.png)

1. På den **introduktion** väljer **nästa**. Om du får felmeddelanden på den här sidan finns [diagnostisera fel med Azure Active Directory anslutna Service](vs-active-directory-error.md).

    ![Introduktionssida](./media/vs-azure-active-directory/configure-azure-ad-wizard-1.png)

1. På den **enkel inloggning på** väljer du en domän från den **domän** listrutan. Listan innehåller alla domäner som är tillgängliga med de konton som visas i dialogrutan Inställningar av Visual Studio (**fil > kontoinställningar...** ). Alternativt kan du ange ett domännamn om du inte hittar det du söker efter, t.ex `mydomain.onmicrosoft.com`. Du kan välja alternativet för att skapa en app i Azure Active Directory eller använda inställningar från en befintlig app i Azure Active Directory. Välj **nästa** när du är klar.

    ![Enkel inloggning på sidan](./media/vs-azure-active-directory/configure-azure-ad-wizard-2.png)

1. På den **katalogåtkomst** väljer den **läsa katalogdata** alternativ efter behov. Utvecklare har vanligtvis det här alternativet.

    ![Åtkomst katalogsidan](./media/vs-azure-active-directory/configure-azure-ad-wizard-3.png)

1. Välj **Slutför** att starta ändringar till projektet att aktivera Azure AD-autentisering. Visual Studio visar förloppet under denna tid:

    ![Active Directory-anslutna service pågår](./media/vs-azure-active-directory/active-directory-connected-service-output.png)

1. När processen är klar, öppnar webbläsaren till en av följande artiklar som är lämpliga för ditt projekt i Visual Studio:

    - [Komma igång med .NET MVC-projekt](vs-active-directory-dotnet-getting-started.md)
    - [Komma igång med WebAPI-projekt](vs-active-directory-webapi-getting-started.md)

1. Du kan också se Active Directory-domänen på den [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040).

## <a name="how-your-project-is-modified"></a>Hur projektet har ändrats

När du lägger till tjänsten anslutna guiden lägger Visual Studio till Azure Active Directory och associerade referenser till projektet. Konfigurationsfiler och kodfiler i projektet ändras även om du vill lägga till stöd för Azure AD. Vissa ändringar som Visual Studio gör beror på projekttypen. Se följande artiklar för ytterligare information:

- [Vad hände med mitt .NET MVC-projekt?](vs-active-directory-dotnet-what-happened.md)
- [Vad hände med Web API-projekt?](vs-active-directory-webapi-what-happened.md)

## <a name="next-steps"></a>Nästa steg

- [Autentiseringsscenarier för Azure Active Directory](active-directory-authentication-scenarios.md)
- [Lägga till inloggning med Microsoft till ett ASP.NET-webbprogram](guidedsetups/active-directory-aspnetwebapp-v1.md)
