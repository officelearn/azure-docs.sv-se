---
title: Anpassad startsida för publicerade appar - Azure AD Application Proxy
description: Täcker grunderna om Azure AD Application Proxy-kopplingar
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1621b273f617955a374ed46d9c215ba99e5b2913
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275604"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Ange en anpassad startsida för publicerade appar med hjälp av Azure AD Application Proxy

I den här artikeln beskrivs hur du konfigurerar en app så att en användare dirigeras till en anpassad startsida. När du publicerar en app med Programproxy anger du en intern URL, men ibland är det inte den sida som en användare bör se först. Ange en anpassad startsida så att en användare får rätt sida när de öppnar appen. En användare ser den anpassade startsidan som du anger, oavsett om de kommer åt appen från Azure Active Directory Access Panel eller Office 365-startprogrammet.

När en användare startar appen dirigeras de som standard till rotdomän-URL:en för den publicerade appen. Målsidan anges vanligtvis som startsides-URL.The mål page is typically set as the home page URL. Använd Azure AD PowerShell-modulen för att definiera en anpassad webbadress när du vill att en appanvändare ska landa på en viss sida i appen.

Här är ett scenario som förklarar varför ditt företag skulle ange en anpassad startsida:

- I företagets nätverk går en `https://ExpenseApp/login/login.aspx` användare till för att logga in och komma åt din app.
- Eftersom du har andra resurser (till exempel bilder) som Programproxy måste komma åt `https://ExpenseApp` på den översta nivån i mappstrukturen publicerar du appen med som intern URL.
- Standardens externa `https://ExpenseApp-contoso.msappproxy.net`URL är , som inte tar en extern användare till inloggningssidan.
- Du vill `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx` ange som webbadress på startsidan i stället, så att en extern användare ser inloggningssidan först.

> [!NOTE]
> När du ger användarna åtkomst till publicerade appar visas apparna i [Azure AD Access Panel](../user-help/my-apps-portal-end-user-access.md) och Office [365-appstartprogrammet](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/).

## <a name="before-you-start"></a>Innan du börjar

Tänk på följande krav innan du ställer in webbadressen:

- Sökvägen som du anger måste vara en underdomänsökväg till rotdomänens URL.

  Om rotdomän-URL:en `https://apps.contoso.com/app1/`till exempel är måste startsides-URL:en som du konfigurerar börja med `https://apps.contoso.com/app1/`.

- Om du gör en ändring i den publicerade appen kan ändringen återställa värdet för startsidans URL. När du uppdaterar appen i framtiden bör du kontrollera och vid behov uppdatera webbadressen till startsidan.

Du kan ange startsides-URL:en antingen via Azure-portalen eller med PowerShell.

## <a name="change-the-home-page-in-the-azure-portal"></a>Ändra startsidan i Azure-portalen

Så här ändrar du startsidans URL för din app via Azure AD-portalen:

1. Logga in på [Azure-portalen](https://portal.azure.com/) som administratör.
1. Välj **Azure Active Directory**och sedan **Appregistreringar**. Listan över registrerade appar visas.
1. Välj din app i listan. En sida som visar information om den registrerade appen visas.
1. Under **Hantera**väljer du **Branding**.
1. Uppdatera **webbadressen till startsidan** med den nya sökvägen.

   ![Varumärkessida för en registrerad app som visar url-fältet startsida](media/application-proxy-configure-custom-home-page/app-proxy-app-branding.png)

1. Välj **Spara**.

## <a name="change-the-home-page-with-powershell"></a>Ändra startsidan med PowerShell

Om du vill konfigurera startsidan för en app med PowerShell måste du:

1. Installera Azure AD PowerShell-modulen.
1. Leta reda på objectid-värdet för appen.
1. Uppdatera appens url till startsidan med PowerShell-kommandon.

### <a name="install-the-azure-ad-powershell-module"></a>Installera Azure AD PowerShell-modulen

Installera Azure AD PowerShell-modulen innan du definierar en anpassad url till startsidan med PowerShell.Du kan hämta paketet från [PowerShell-galleriet](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16), som använder Graph API-slutpunkten.

Så här installerar du paketet:

1. Öppna ett standardfönster för PowerShell och kör sedan följande kommando:

   ```powershell
   Install-Module -Name AzureAD
   ```

    Om du kör kommandot som icke-administratör använder `-scope currentuser` du alternativet.

1. Under installationen väljer du **Y** för att installera två paket från Nuget.org. Båda paketen krävs.

### <a name="find-the-objectid-of-the-app"></a>Hitta objectid för appen

Du får ObjectId för appen genom att söka efter appen med dess visningsnamn eller startsida.

1. Importera Azure AD-modulen i samma PowerShell-fönster.

   ```powershell
   Import-Module AzureAD
   ```

1. Logga in på Azure AD-modulen som klientadministratör.

   ```powershell
   Connect-AzureAD
   ```

1. Hitta appen. I det här exemplet används PowerShell för att hitta ObjectId genom att söka efter appen med visningsnamnet `SharePoint`.

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   Du bör få ett resultat som liknar det som visas här. Kopiera ObjectId GUID som ska användas i nästa avsnitt.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   Alternativt kan du bara hämta listan över alla appar, söka i listan efter appen med ett visst visningsnamn eller startsida och kopiera appens ObjectId när appen har hittats.

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>Uppdatera webbadressen till startsidan

Skapa webbadressen till startsidan och uppdatera appen med det värdet. Fortsätt använda samma PowerShell-fönster, eller om du använder ett nytt PowerShell-fönster `Connect-AzureAD`loggar du in på Azure AD-modulen igen med . Följ sedan de här stegen:

1. Skapa en variabel för att hålla det ObjectId-värde som du kopierade i föregående avsnitt. (Ersätt objectid-värdet som används i det här SharePoint-exemplet med appens ObjectId-värde.)

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

1. Bekräfta att du har rätt app genom att köra följande kommando. Utdata ska vara identiska med utdata som du såg i föregående avsnitt ([Sök in objectid för appen](#find-the-objectid-of-the-app)).

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

1. Skapa ett tomt programobjekt som du vill behålla de ändringar som du vill göra.

   ```powershell
   $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
   ```

1. Ange webbadressen till startsidan till önskat värde. Värdet måste vara en underdomänsökväg till den publicerade appen. Om du till exempel ändrar `https://sharepoint-iddemo.msappproxy.net/` webbadressen till startsidan till `https://sharepoint-iddemo.msappproxy.net/hybrid/`går appanvändare direkt till den anpassade startsidan.

   ```powershell
   $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
   ```

1. Gör uppdateringen av startsidan.

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

1. Om du vill bekräfta att ändringen lyckades kör du följande kommando från steg 2 igen.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   Till vårt exempel bör utdata nu visas på följande sätt:

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

1. Starta om appen för att bekräfta att startsidan visas som den första skärmen, som förväntat.

> [!NOTE]
> Alla ändringar som du gör i appen kan återställa startsidans URL. Om webbadressen till startsidan återställs upprepar du stegen i det här avsnittet för att ställa tillbaka den.

## <a name="next-steps"></a>Nästa steg

- [Aktivera fjärråtkomst till SharePoint med Azure AD-programproxy](application-proxy-integrate-with-sharepoint-server.md)
- [Självstudiekurs: Lägga till ett lokalt program för fjärråtkomst via programproxy i Azure Active Directory](application-proxy-add-on-premises-application.md)