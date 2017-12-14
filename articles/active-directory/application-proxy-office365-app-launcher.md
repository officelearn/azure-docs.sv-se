---
title: "Ange en anpassad hemsida för publicerade appar med hjälp av Azure AD Application Proxy | Microsoft Docs"
description: Beskriver grunderna om Azure AD Application Proxy-kopplingar
services: active-directory
documentationcenter: 
author: kgremban
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: a9bc2f7b90adb3b3bef6dd6790500272939e637d
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Ange en anpassad hemsida för publicerade appar med hjälp av Azure AD Application Proxy

Den här artikeln beskrivs hur du konfigurerar appar för att dirigera användare till en anpassad startsida. När du publicerar ett program med Application Proxy kan ange du en intern URL men ibland som inte är sidan användarna ska se först. Ange en anpassad hemsida så att användarna gå till sidan rätt när de har åtkomst till apparna. Användarna ser anpassade sidan som du ställer in om de har åtkomst till appen från åtkomstpanelen Azure Active Directory eller Office 365 app starta.

När användarna startar appen, är de riktade som standard till rot-URL för den publicerade app för domänen. Landningssida anges vanligtvis som URL för startsidan. Använda Azure AD PowerShell-modulen för att definiera anpassade startsidan URL: er när du vill att användarna ska hamna på en viss sida i appen. 

Här är ett exempel på varför ett företag vill ange en anpassad startsida:
- I företagsnätverket, användare går du till *https://ExpenseApp/login/login.aspx* att logga in och komma åt din app.
- Eftersom du har andra tillgångar som avbildningar som Application Proxy behöver åtkomst till på den översta nivån av mappstrukturen kan du publicera appen med *https://ExpenseApp* som intern URL.
- Standard-externa URL: en är *https://ExpenseApp-contoso.msappproxy.net*, som tar inte användarna att logga in sidan.  
- Ange *https://ExpenseApp-contoso.msappproxy.net/login/login.aspx* som URL för startsidan. 

>[!NOTE]
>När du ger användare åtkomst till publicerade appar appar visas i den [Azure AD-åtkomstpanelen](active-directory-saas-access-panel-introduction.md) och [Office 365 app starta](https://blogs.office.com/2016/09/27/introducing-the-new-office-365-app-launcher).

## <a name="before-you-start"></a>Innan du börjar

Innan du anger URL till startsidan, Tänk på följande krav:

* Kontrollera att den angivna sökvägen är en underdomän sökväg av domän rot-URL.

  Om rotdomänen URL: en är till exempel https://apps.contoso.com/app1/, den URL-Adressen som du konfigurerar måste börja med https://apps.contoso.com/app1/.

* Om du gör en ändring i appen publicerade kan ändringen återställa värdet för URL till startsidan. När du uppdaterar appen i framtiden, bör du kontrollera och, om det behövs, uppdatera URL till startsidan.

## <a name="change-the-home-page-in-the-azure-portal"></a>Ändra startsidan i Azure-portalen

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Gå till **Azure Active Directory** > **App registreringar** och välj ditt program i listan. 
3. Välj **egenskaper** från inställningarna.
4. Uppdatering av **URL-Adressen** med den nya sökvägen. 

   ![Ange ny URL-Adressen](./media/application-proxy-office365-app-launcher/homepage.png)

5. Välj **spara**

## <a name="change-the-home-page-with-powershell"></a>Ändra startsidan med PowerShell

### <a name="install-the-azure-ad-powershell-module"></a>Installera Azure AD PowerShell-modulen

Installera Azure AD PowerShell-modulen innan du definierar en anpassad URL-Adressen med hjälp av PowerShell. Du kan hämta paketet från den [PowerShell-galleriet](https://www.powershellgallery.com/packages/AzureAD/2.0.0.131), som använder Graph API-slutpunkten. 

Följ dessa steg för att installera paketet:

1. Öppna ett PowerShell-fönster som standard och kör sedan följande kommando:

    ```
     Install-Module -Name AzureAD
    ```
    Om du använder kommandot som icke-administratörer kan använda den `-scope currentuser` alternativet.
2. Under installationen, Välj **Y** att installera två paket från Nuget.org. Båda paketen krävs. 

### <a name="find-the-objectid-of-the-app"></a>Sök efter objekt-ID för appen

Hämta ObjectID för appen och sök sedan efter appen genom att dess hemsida.

1. Importera Azure AD-modulen i samma PowerShell-fönstret.

    ```
    Import-Module AzureAD
    ```

2. Logga in på Azure AD-modulen som Innehavaradministratör.

    ```
    Connect-AzureAD
    ```
3. Hitta appen baserat på dess URL-Adressen. Du hittar Webbadressen i portalen genom att gå till **Azure Active Directory** > **företagsprogram** > **alla program**. Det här exemplet används *sharepoint iddemo*.

    ```
    Get-AzureADApplication | where { $_.Homepage -like “sharepoint-iddemo” } | fl DisplayName, Homepage, ObjectID
    ```
4. Du bör få ett resultat som liknar den som visas här. Kopiera ObjectID GUID för användning i nästa avsnitt.

    ```
    DisplayName : SharePoint
    Homepage    : https://sharepoint-iddemo.msappproxy.net/
    ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

### <a name="update-the-home-page-url"></a>Uppdatera URL till startsidan

Skapa URL till startsidan och uppdatera ditt program med det värdet. Fortsätt med samma PowerShell-fönstret för att köra dessa kommandon. Eller, om du använder ett nytt PowerShell-fönster, logga in på Azure AD-modulen igen med `Connect-AzureAD`. 

1. Bekräfta att du har rätt appen och Ersätt *8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4* med objekt-ID som du kopierade i föregående avsnitt.

    ```
    Get-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4.
    ```

 Nu när du har bekräftat appen, är du redo att uppdatera sidan, enligt följande.

2. Skapa en tom programobjektet för att lagra de ändringar som du vill göra. Den här variabeln innehåller de värden som du vill uppdatera. Inget har skapats i det här steget.

    ```
    $appnew = New-Object “Microsoft.Open.AzureAD.Model.Application”
    ```

3. Ange den URL-Adressen till det värde som du vill. Värdet måste vara en underdomän sökvägen till publicerade appen. Till exempel om du ändrar den URL-Adressen från *https://sharepoint-iddemo.msappproxy.net/* till *https://sharepoint-iddemo.msappproxy.net/hybrid/*, appanvändare gå direkt till sidan med anpassade.

    ```
    $homepage = “https://sharepoint-iddemo.msappproxy.net/hybrid/”
    ```
4. Gör uppdateringen med hjälp av GUID (objekt-ID) som du kopierade i ”steg 1: hitta ObjectID för appen”.

    ```
    Set-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4 -Homepage $homepage
    ```
5. Starta om appen för att bekräfta att ändringen har lyckats.

    ```
    Get-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

>[!NOTE]
>Alla ändringar du gör i appen kan återställa URL till startsidan. Om din startsida URL återställer Upprepa stegen i det här avsnittet för att ange den igen.

## <a name="next-steps"></a>Nästa steg

- [Aktivera fjärråtkomst och SharePoint med Azure AD Application Proxy](application-proxy-enable-remote-access-sharepoint.md)
- [Aktivera Application Proxy på Azure-portalen](active-directory-application-proxy-enable.md)
