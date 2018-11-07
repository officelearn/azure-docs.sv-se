---
title: Att lägga till en Azure Active Directory med hjälp av Connected Services i Visual Studio
description: Lägg till en Azure Active Directory med hjälp av dialogrutan Visual Studio lägga till Connected Services
services: active-directory
author: ghogen
manager: douge
ms.assetid: f599de6b-e369-436f-9cdc-48a0165684cb
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.openlocfilehash: 565a484d2eae0879b625e79ef7ffbdd8eb5e4455
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51251044"
---
# <a name="adding-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Att lägga till en Azure Active Directory med hjälp av Connected Services i Visual Studio

Med hjälp av Azure Active Directory (AD Azure), kan du använda enkel inloggning (SSO) för ASP.NET MVC-webbprogram och Active Directory-autentisering i webb-API-tjänster. Med Azure AD-autentisering kan användarna använda sina konton från Azure Active Directory för att ansluta till dina webbprogram. Fördelarna med Azure AD-autentisering med webb-API är förbättrad datasäkerhet när exponerar ett API från ett webbprogram. Du behöver inte hantera en separat autentiseringssystem med sin egen konto och användare med Azure AD.

Den här artikeln och dess tillhörande artiklar innehåller information för att använda Visual Studio Connected Service-funktionen för Active Directory. Funktionen är tillgänglig i både Visual Studio 2017 och Visual Studio 2015.

För närvarande stöder inte tjänsten Active Directory-anslutna ASP.NET Core-program.

## <a name="prerequisites"></a>Förutsättningar

- Azure-konto: Om du inte har ett Azure-konto, kan du [registrera dig för en kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) eller [aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
- **Visual Studio 2015** eller senare. [Hämta Visual Studio 2017 nu](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>Ansluta till Azure Active Directory genom att använda dialogen Connected Services

1. Skapa eller öppna ett ASP.NET MVC-projekt eller ett ASP.NET Web API-projekt i Visual Studio. Du kan använda MVC, webb-API, Single-Page Application, Azure API Apps, Azure Mobile-appen och mallar för Azure mobila tjänster.

1. Välj den **projekt > Lägg till Connected Service...**  menyalternativ eller dubbelklicka på den **Connected Services** nod finns under projektet i Solution Explorer.

1. På den **Connected Services** väljer **autentisering med Azure Active Directory**.

    ![Sidan för anslutna tjänster](./media/vs-azure-active-directory/connected-services-add-active-directory.png)

1. På den **introduktion** väljer **nästa**. Om du får felmeddelanden på den här sidan finns [diagnostisera fel med den Azure Active Directory Connected Service](vs-active-directory-error.md).

    ![Introduktionssida](./media/vs-azure-active-directory/configure-azure-ad-wizard-1.png)

1. På den **enkel inloggning på** väljer du en domän från den **domän** listrutan. Listan innehåller alla domäner som är tillgängliga med de konton som visas i dialogrutan Inställningar av Visual Studio (**fil > kontoinställningar...** ). Alternativt kan du ange ett domännamn om du inte hittar det du letar efter, till exempel `mydomain.onmicrosoft.com`. Du kan välja alternativet för att skapa en app i Azure Active Directory eller använda inställningar från en befintlig app i Azure Active Directory. Välj **nästa** när du är klar.

    ![Enkel inloggning på sidan](./media/vs-azure-active-directory/configure-azure-ad-wizard-2.png)

1. På den **katalogåtkomst** väljer den **läsa katalogdata** alternativet efter behov. Utvecklare omfattar vanligtvis det här alternativet.

    ![Sidan för Directory-åtkomst](./media/vs-azure-active-directory/configure-azure-ad-wizard-3.png)

1. Välj **Slutför** att starta ändringar till projektet för att aktivera Azure AD-autentisering. Visual Studio visar förloppet under denna tid:

    ![Active Directory-anslutna service-förlopp](./media/vs-azure-active-directory/active-directory-connected-service-output.png)

1. När processen är klar, öppnar webbläsaren till någon av följande artiklar efter behov för ditt projekt i Visual Studio:

    - [Komma igång med .NET MVC-projekt](vs-active-directory-dotnet-getting-started.md)
    - [Komma igång med WebAPI-projekt](vs-active-directory-webapi-getting-started.md)

1. Du kan också se Active Directory-domän på den [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).

## <a name="how-your-project-is-modified"></a>Hur ditt projekt ändras

När du lägger till den anslutna tjänsten guiden lägger Azure Active Directory och tillhörande referenser till ditt projekt i Visual Studio. Konfigurationsfiler och kodfiler i projektet ändras också om du vill lägga till stöd för Azure AD. De specifika ändringar som Visual Studio gör beror på vilken projekt. Se följande artiklar om information:

- [Vad hände med mitt .NET MVC-projekt?](vs-active-directory-dotnet-what-happened.md)
- [Vad hände med mitt webb-API-projekt?](vs-active-directory-webapi-what-happened.md)

## <a name="next-steps"></a>Nästa steg

- [Autentiseringsscenarier för Azure Active Directory](authentication-scenarios.md)
- [Lägga till logga in med Microsoft i en ASP.NET-webbapp](quickstart-v1-aspnet-webapp.md)
