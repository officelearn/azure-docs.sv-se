---
title: Ange en anpassad startsida för publicerade appar med hjälp av Azure AD Application Proxy | Microsoft Docs
description: Täcker grunderna om Azure AD Application Proxy-anslutningsappar
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/08/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 9ee7f0b975dd2990281833726b4013c9726a2935
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53134326"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Ange en anpassad startsida för publicerade appar med hjälp av Azure AD Application Proxy

Den här artikeln beskrivs hur du konfigurerar appar för att dirigera användare till en anpassad startsida. När du publicerar ett program med Application Proxy kan ange du en intern Webbadress men ibland som inte är den sida som användarna ska se först. Ange en anpassad startsida så att dina användare gå till rätt sida när de har åtkomst till apparna. Användarna ser anpassade startsidan som du anger om de har åtkomst till appen från åtkomstpanelen Azure Active Directory eller Office 365-appstartaren.

När användarna startar appen, är de skickas som standard till rot-URL för domänen för den publicerade appen. Landningssida anges vanligtvis som URL för startsidan. Använda Azure AD PowerShell-modulen för att definiera anpassade startsida URL: er när du vill att app-användare alltid landar på en viss sida i appen. 

Här är ett exempel på varför ett företag skulle ange en anpassad startsida:
- I företagsnätverket, användarna gå till *https://ExpenseApp/login/login.aspx* att logga in och komma åt din app.
- Eftersom du har andra tillgångar som bilder som Application Proxy behöver åtkomst till på den översta nivån av mappstrukturen kan du publicera appen med *https://ExpenseApp* som den interna URL: en.
- Den externa URL som standard är *https://ExpenseApp-contoso.msappproxy.net*, vilket inte ta dina användare på inloggningssidan.  
- Ange *https://ExpenseApp-contoso.msappproxy.net/login/login.aspx* som URL för startsidan. 

>[!NOTE]
>När du ger användare åtkomst till publicerade appar appar visas i den [Azure AD-åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md) och [Office 365-appstartaren](https://blogs.office.com/2016/09/27/introducing-the-new-office-365-app-launcher).

## <a name="before-you-start"></a>Innan du börjar

Innan du ställer in URL: en för startsidan, Tänk på följande krav:

* Kontrollera att den angivna sökvägen är en underdomän sökväg till rot domän-URL.

  Om rotdomänen URL: en är till exempel https://apps.contoso.com/app1/, hemsidans URL som du konfigurerar måste börja med https://apps.contoso.com/app1/.

* Om du gör en ändring i den publicerade appen kan ändringen återställa värdet för den URL-Adressen. När du uppdaterar appen vilket i framtiden kan du kontrollera och, om det behövs, uppdatera hemside-URL.

## <a name="change-the-home-page-in-the-azure-portal"></a>Ändra startsidan i Azure portal

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Gå till **Azure Active Directory** > **appregistreringar** och välj ditt program i listan. 
3. Välj **egenskaper** från inställningarna.
4. Uppdatera den **URL-Adressen** fältet med den nya sökvägen. 

   ![Ange ny URL-Adressen](./media/application-proxy-configure-custom-home-page/homepage.png)

5. Välj **spara**

## <a name="change-the-home-page-with-powershell"></a>Ändra på hemsidan med PowerShell

### <a name="install-the-azure-ad-powershell-module"></a>Installera Azure AD PowerShell-modulen

Installera Azure AD PowerShell-modulen innan du definierar en anpassad URL-Adressen med hjälp av PowerShell. Du kan hämta paketet från den [PowerShell-galleriet](https://www.powershellgallery.com/packages/AzureAD/2.0.0.131), som använder Graph API-slutpunkt. 

Följ dessa steg för att installera paketet:

1. Öppna ett vanligt PowerShell-fönster och kör sedan följande kommando:

    ```
     Install-Module -Name AzureAD
    ```
    Om du använder kommandot som en icke-administratörer kan använda den `-scope currentuser` alternativet.
2. Under installationen, Välj **Y** installera två paket från Nuget.org. Båda paketen krävs. 

### <a name="find-the-objectid-of-the-app"></a>Hitta ObjectID för appen

Hämta ObjectID för appen och sedan söka efter appen genom dess startsida.

1. Importera Azure AD-modulen i samma PowerShell-fönstret.

    ```
    Import-Module AzureAD
    ```

2. Logga in på Azure AD-modulen som klientadministratören.

    ```
    Connect-AzureAD
    ```
3. Hitta appen baserat på dess URL-Adressen. Du hittar URL: en i portalen genom att gå till **Azure Active Directory** > **företagsprogram** > **alla program**. Det här exemplet används *sharepoint-iddemo*.

    ```
    Get-AzureADApplication | where { $_.Homepage -like "sharepoint-iddemo" } | fl DisplayName, Homepage, ObjectID
    ```
4. Du bör få ett resultat som liknar den som visas här. Kopiera ObjectID GUID ska användas i nästa avsnitt.

    ```
    DisplayName : SharePoint
    Homepage    : https://sharepoint-iddemo.msappproxy.net/
    ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

### <a name="update-the-home-page-url"></a>Uppdatera hemside-URL

Skapa hemsidans URL och uppdatera ditt program med detta värde. Fortsätta att använda samma PowerShell-fönstret för att köra dessa kommandon. Eller, om du använder ett nytt PowerShell-fönster, logga in på Azure AD-modulen igen med `Connect-AzureAD`. 

1. Bekräfta att du har rätt app och Ersätt *8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4* med objekt-ID som du kopierade i föregående avsnitt.

    ```
    Get-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4.
    ```

 Nu när du har bekräftat appen, är du redo att uppdatera startsidan på följande sätt.

2. Skapa en tom programobjektet för att lagra de ändringar som du vill göra. Den här variabeln innehåller de värden som du vill uppdatera. Ingenting har skapats i det här steget.

    ```
    $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
    ```

3. Ange URL till startsidan till det värde som du vill. Värdet måste vara en underdomän sökväg till den publicerade appen. Exempel: Om du ändrar den URL-Adressen från `https://sharepoint-iddemo.msappproxy.net/` till `https://sharepoint-iddemo.msappproxy.net/hybrid/`, app-användare gå direkt till anpassad startsida.

    ```
    $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
    ```
4. Uppdateringen blir med hjälp av GUID (objekt-ID) som du kopierade i ”steg 1: hitta ObjectID för appen”.

    ```
    Set-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4 -Homepage $homepage
    ```
5. Starta om appen för att bekräfta att ändringen har lyckats.

    ```
    Get-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

>[!NOTE]
>Alla ändringar du gör i appen kan återställa den URL-Adressen. Om din URL-Adressen återställer upprepar du stegen i det här avsnittet för att ställa in den igen.

## <a name="next-steps"></a>Nästa steg

- [Aktivera fjärråtkomst till SharePoint med Azure AD Application Proxy](application-proxy-integrate-with-sharepoint-server.md)
- [Aktivera Application Proxy i Azure portal](application-proxy-add-on-premises-application.md)
