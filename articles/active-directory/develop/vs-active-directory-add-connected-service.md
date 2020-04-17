---
title: Använda Active Directory-ansluten tjänst (Visual Studio)
description: Lägga till en Azure Active Directory med hjälp av dialogrutan Lägg till anslutna tjänster i Visual Studio
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.custom: aaddev, vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.openlocfilehash: d5b6452684757aead684356fd9bb032b90c58c4a
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535833"
---
# <a name="add-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Lägga till en Azure Active Directory med hjälp av Anslutna tjänster i Visual Studio

Genom att använda Azure Active Directory (Azure AD) kan du stödja SSO (Single Sign-On) för ASP.NET MVC-webbprogram eller Active Directory-autentisering i webb-API-tjänster. Med Azure AD-autentisering kan användarna använda sina konton från Azure Active Directory för att ansluta till dina webbprogram. Fördelarna med Azure AD-autentisering med webb-API inkluderar förbättrad datasäkerhet när du exponerar ett API från ett webbprogram. Med Azure AD behöver du inte hantera ett separat autentiseringssystem med eget konto och användarhantering.

Den här artikeln och dess tillhörande artiklar innehåller information om hur du använder funktionen Visual Studio Connected Service för Active Directory. Funktionen är tillgänglig i Visual Studio 2015 och senare.

För närvarande stöder den Active Directory-anslutna tjänsten inte ASP.NET Core-program.

## <a name="prerequisites"></a>Krav

- Azure-konto: Om du inte har ett Azure-konto kan du [registrera dig för en kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) eller aktivera dina fördelar för Visual [Studio-prenumeranten](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
- **Visual Studio 2015** eller senare. [Ladda ner Visual Studio nu](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>Ansluta till Azure Active Directory med dialogrutan Anslutna tjänster

1. Skapa eller öppna ett ASP.NET MVC-projekt i Visual Studio eller ett ASP.NET webb-API-projekt. Du kan använda MVC-, webb-API:et, ensidiga program,Azure API App, Azure Mobile App och Azure Mobile Service.You can use the MVC, Web API, Single-Page Application, Azure API App, Azure Mobile App, and Azure Mobile Service templates.

1. Välj menykommandot **Lägg till ansluten tjänst > i Project** eller dubbelklicka på **noden Anslutna tjänster** som hittades under projektet i Solution Explorer.

1. På sidan **Anslutna tjänster** väljer du **Autentisering med Azure Active Directory**.

    ![Sidan Anslutna tjänster](./media/vs-azure-active-directory/connected-services-add-active-directory.png)

1. På sidan **Introduktion** väljer du **Nästa**. Om du ser fel på den här sidan läser du [Diagnostisera fel med Azure Active Directory Connected Service](vs-active-directory-error.md).

    ![Introduktionssida](./media/vs-azure-active-directory/configure-azure-ad-wizard-1.png)

1. Välj en domän i listrutan **Domän** på sidan **Enkel inloggning.** Listan innehåller alla domäner som är tillgängliga för de konton som visas i dialogrutan Kontoinställningar i Visual Studio (**Fil > Kontoinställningar...**). Som ett alternativ kan du ange ett domännamn om du inte hittar det `mydomain.onmicrosoft.com`du letar efter, till exempel . Du kan välja alternativet för att skapa en Azure Active Directory-app eller använda inställningarna från en befintlig Azure Active Directory-app. Välj **Nästa** när du är klar.

    ![Enkel inloggning på sidan](./media/vs-azure-active-directory/configure-azure-ad-wizard-2.png)

1. Välj alternativet **Läs katalogdata** på sidan **Katalogåtkomst** som du vill. Utvecklare inkluderar vanligtvis det här alternativet.

    ![Åtkomstsida för katalog](./media/vs-azure-active-directory/configure-azure-ad-wizard-3.png)

1. Välj **Slutför** om du vill starta ändringar i projektet för att aktivera Azure AD-autentisering. Visual Studio visar förloppet under den här tiden:

    ![Förloppet för Active Directory-ansluten tjänst](./media/vs-azure-active-directory/active-directory-connected-service-output.png)

1. När processen är klar öppnar Visual Studio webbläsaren för en av följande artiklar, beroende på projekttyp:

    - [Komma igång med .NET MVC-projekt](vs-active-directory-dotnet-getting-started.md)
    - [Komma igång med WebAPI-projekt](vs-active-directory-webapi-getting-started.md)

1. Du kan också se Active Directory-domänen på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).

## <a name="how-your-project-is-modified"></a>Hur ditt projekt ändras

När du lägger till den anslutna tjänsten guiden lägger Visual Studio till Azure Active Directory och tillhörande referenser i projektet. Konfigurationsfiler och kodfiler i projektet ändras också för att lägga till stöd för Azure AD. Vilka ändringar som Visual Studio gör beror på projekttypen. Mer information finns i följande artiklar:

- [Vad hände med mitt .NET MVC-projekt?](vs-active-directory-dotnet-what-happened.md)
- [Vad har hänt med mitt webb-API-projekt?](vs-active-directory-webapi-what-happened.md)

## <a name="next-steps"></a>Nästa steg

- [Autentiseringsscenarier för Azure Active Directory](authentication-scenarios.md)
- [Lägga till inloggning med Microsoft till en ASP.NET-webbapp](quickstart-v2-aspnet-webapp.md)
