---
title: "Lägga till ett Azure Active Directory med anslutna tjänster i Visual Studio | Microsoft Docs"
description: "Lägg till ett Azure Active Directory med hjälp av dialogrutan Visual Studio Lägg till anslutna tjänster"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: f599de6b-e369-436f-9cdc-48a0165684cb
ms.service: active-directory
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: kraigb
ms.openlocfilehash: a767c93fb271f3aa33d9556c69c511bcac7cb0d5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="adding-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Lägga till ett Azure Active Directory med anslutna tjänster i Visual Studio
Med hjälp av Azure Active Directory (Azure AD) kan stöder du enkel inloggning (SSO) för ASP.NET MVC-webbprogram och Active Directory-autentisering i Web API-tjänster. Användarna kan använda sina konton från Azure Active Directory för att ansluta till ditt webbprogram med Azure Active Directory-autentisering. Fördelarna med Azure Active Directory-autentisering med Web API är förbättrad datasäkerhet när exponera en API från ett webbprogram. Du behöver inte hantera en separat autentiseringssystemet med ett eget konto och användaren management med Azure AD.

## <a name="prerequisites"></a>Krav
- Azure-konto – om du inte har ett Azure-konto kan du [registrera dig för en kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) eller [aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>Ansluta till Azure Active Directory genom att använda dialogrutan anslutna tjänster
1. Skapa eller öppna ett ASP.NET MVC-projekt eller ett ASP.NET Web API-projekt i Visual Studio.

1. Från Solution Explorer högerklickar du på den **anslutna tjänster** nod, och på snabbmenyn Välj **Lägg till anslutna tjänster**.

1. På den **anslutna tjänster** väljer **autentisering med Azure Active Directory**.
   
    ![Anslutna Tjänstesida](./media/vs-azure-tools-connected-services-add-active-directory/connected-services-add-active-directory.png)

1. På den **introduktion** sida av den **konfigurera Azure AD Authentication** väljer **nästa**.
   
    ![Introduktionssida](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-1.png)

1. På den **enkel inloggning på** sida av den **konfigurera Azure AD Authentication** guiden, Välj en domän från den **domän** listrutan. Listan över domäner innehåller alla domäner som är tillgängliga med de konton som visas i dialogrutan Inställningar. Alternativt kan du ange ett domännamn om du inte hittar det du söker efter, t.ex `mydomain.onmicrosoft.com`. Du kan välja alternativet för att skapa en app i Azure Active Directory eller använda inställningar från en befintlig app i Azure Active Directory. Välj **nästa** när du är klar.
   
    ![Enkel inloggning på sidan](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-2.png)

1. På den **katalogåtkomst** sida av den **konfigurera Azure AD Authentication** guiden kontrollerar du att den **läsa katalogdata** alternativet är markerat. 
   
    ![Åtkomst katalogsidan](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-3.png)

1. Välj **Slutför** att lägga till nödvändiga konfigurationskod och referenser till aktivera ditt projekt för Azure AD-autentisering. Du kan se Active Directory-domänen på den [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Visual Studio visas en [vad hände](#how-your-project-is-modified) artikel för att visa hur projektet har ändrats. Om du vill kontrollera att allt fungerar, öppna ett av de ändrade konfigurationsfilerna och kontrollera att finns inställningarna som anges i artikeln. 

## <a name="how-your-project-is-modified"></a>Hur projektet har ändrats
När du kör guiden läggs Azure Active Directory och associerade referenser till ditt projekt i Visual Studio. Konfigurationsfiler och kodfiler i projektet ändras även om du vill lägga till stöd för Azure AD. Vissa ändringar som Visual Studio gör beror på projekttypen. Detaljerad information om hur ASP.NET MVC-projekt har ändrats, se [vilka happened – MVC projekt](http://go.microsoft.com/fwlink/p/?LinkID=513809). Web API-projekt, se [vad hände – Web API-projekt](http://go.microsoft.com/fwlink/p/?LinkId=513810).

## <a name="next-steps"></a>Nästa steg
* [MSDN-Forum för Azure Active Directory](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD)
* [Dokumentationen för Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Blogginlägg: Introduktion till Azure Active Directory](http://blogs.msdn.com/b/brunoterkaly/archive/2014/03/03/introduction-to-windows-azure-active-directory.aspx)

