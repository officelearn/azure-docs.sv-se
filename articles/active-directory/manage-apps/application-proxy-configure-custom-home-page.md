---
title: Ange en anpassad startsida för publicerade appar med hjälp av Azure AD Application Proxy | Microsoft Docs
description: Täcker grunderna om Azure AD Application Proxy-anslutningsappar
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3143dfafdd402bd9eeb2f201f4e728d84c4b9f09
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64691168"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Ange en anpassad startsida för publicerade appar med hjälp av Azure AD Application Proxy

Den här artikeln beskrivs hur du konfigurerar en app för att be en användare till en anpassad startsida, som kan skilja sig beroende på om de är interna eller externa. När du publicerar en app med Application Proxy kan du ställa in en intern URL, men ibland som inte är den sida som en användare ska se först. Ange en anpassad startsida så att en användare får rätt sida när de har åtkomst till appen. En användare kan se anpassad startsida att du ställer in, oavsett om de använder appen från åtkomstpanelen Azure Active Directory eller Office 365-appstartaren.

När en användare startar appen, är de riktade som standard till rot-URL för domänen för den publicerade appen. Landningssida anges vanligtvis som URL för startsidan. Använda Azure AD PowerShell-modulen för att definiera en anpassad URL-Adressen när du vill att appanvändare hamnar på en viss sida i appen.

Här är ett scenario som förklarar varför ditt företag skulle ange en anpassad startsida och varför det skulle vara olika beroende på typ av användare:

- Eftersom du har andra resurser (till exempel bilder) som Application Proxy behöver åtkomst till på den översta nivån av mappstrukturen kan du publicera appen med `https://ExpenseApp` som den interna URL: en.
- Men i företagsnätverket, en användare går till `https://ExpenseApp/login/login.aspx` att logga in och komma åt din app.
- Den externa URL som standard är `https://ExpenseApp-contoso.msappproxy.net`, som inte tar emot en extern användare komma på inloggningssidan.
- Vill du ange `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx` som den externa URL-Adressen i stället så en extern användare ser på inloggningssidan först.

>[!NOTE]
>När du ger användare åtkomst till publicerade appar appar visas i den [Azure AD-åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md) och [Office 365-appstartaren](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/).

## <a name="before-you-start"></a>Innan du börjar

Innan du ställer in URL: en för startsidan, Tänk på följande krav:

- Den sökväg som du anger måste vara en underdomän sökväg av domän rot-URL.

  Exempel: om rotdomänen URL: en är `https://apps.contoso.com/app1/`, hemsidans URL som du konfigurerar måste börja med `https://apps.contoso.com/app1/`.

- Om du gör en ändring i den publicerade appen kan ändringen återställa värdet för den URL-Adressen. När du uppdaterar appen vilket i framtiden kan du kontrollera och, om det behövs, uppdatera hemside-URL.

Du kan ändra startsidan externt eller internt via Azure portal eller med hjälp av PowerShell.

## <a name="change-the-home-page-in-the-azure-portal"></a>Ändra startsidan i Azure portal

Följ dessa steg om du vill ändra startsidor externa och interna för din app via Azure AD-portalen:

1. Logga in på den [Azure Active Directory-portalen](https://aad.portal.azure.com/). Instrumentpanelen för Azure Active Directory Administrationscenter visas.
2. Välj i sidopanelen, **Azure Active Directory**. Översikt över Azure AD-sidan visas.
3. I Översikt-sidopanelen väljer **appregistreringar**. Listan över registrerade appar visas.
4. Välj din app i listan. En sida som visar information om registrerade appen visas.
5. Klicka på länken under **omdirigerings-URI: er**, som visar antalet omdirigerings-URI: er för webb- och offentliga klienttyper. Autentiseringssidan för registrerade appen visas.
6. I den sista raden i det **omdirigerings-URI: er** tabellen genom att ange den **typ** kolumnen till **offentlig klient (mobilappar och skrivbordsappar)**, och i den **OMDIRIGERINGS-URI**kolumnen, skriver du den interna URL: en som du vill använda. En ny tom rad visas under den rad som du just har ändrat.
7. I den nya raden anger den **typ** kolumnen till **Web**, och i den **OMDIRIGERINGS-URI** kolumnen, skriver du den externa URL: en som du vill använda.
8. Om du vill ta bort alla befintliga omdirigerings-URI: N rader, väljer du den **ta bort** (en Papperskorgen) intill varje oönskad rad.
9. Välj **Spara**.

## <a name="change-the-home-page-with-powershell"></a>Ändra på hemsidan med PowerShell

Om du vill konfigurera en app med hjälp av PowerShell startsida, måste du:

1. Installera Azure AD PowerShell-modulen.
2. Hitta objekt-ID-värdet för appen.
3. Uppdatera appens URL-Adressen med hjälp av PowerShell-kommandon.

### <a name="install-the-azure-ad-powershell-module"></a>Installera Azure AD PowerShell-modulen

Installera Azure AD PowerShell-modulen innan du definierar en anpassad URL-Adressen med hjälp av PowerShell. Du kan hämta paketet från den [PowerShell-galleriet](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16), som använder Graph API-slutpunkt.

Följ dessa steg för att installera paketet:

1. Öppna ett vanligt PowerShell-fönster och kör sedan följande kommando:

   ```powershell
   Install-Module -Name AzureAD
   ```

    Om du använder kommandot som en icke-administratörer kan använda den `-scope currentuser` alternativet.

2. Under installationen, Välj **Y** installera två paket från Nuget.org. Båda paketen krävs.

### <a name="find-the-objectid-of-the-app"></a>Hitta ObjectId för appen

Du kan hämta ObjectId för appen genom att söka efter appen genom att dess visningsnamn eller startsida.

1. Importera Azure AD-modulen i samma PowerShell-fönstret.

   ```powershell
   Import-Module AzureAD
   ```

2. Logga in på Azure AD-modulen som klientadministratören.

   ```powershell
   Connect-AzureAD
   ```

3. Hitta appen. Det här exemplet används PowerShell för att hitta objekt-ID genom att söka efter appen med ett visningsnamn för `SharePoint`.

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   Du bör få ett resultat som liknar den som visas här. Kopiera ObjectId GUID ska användas i nästa avsnitt.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   Du kan också kan du bara hämta listan över alla appar, söklistan för appen med ett visst namn eller startsidan och kopiera appens ObjectId när appen har hittat.

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>Uppdatera hemside-URL

Skapa hemsidans URL och uppdatera din app med det värdet. Fortsätta att använda samma PowerShell-fönstret eller om du använder ett nytt PowerShell-fönster, logga in på Azure AD-modulen igen med `Connect-AzureAD`. Följ dessa steg:

1. Skapa en variabel som innehåller objekt-ID-värdet som du kopierade i föregående avsnitt. (Ersätt värdet för ObjectId som används för i det här SharePoint-exemplet med appens ObjectId värde.)

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

2. Bekräfta att du har rätt appen genom att köra följande kommando. Utdata ska vara identiska med de utdata som du såg i föregående avsnitt ([hitta ObjectId appens](#find-the-objectid-of-the-app)).

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

3. Skapa en tom programobjektet för att lagra de ändringar som du vill göra.

   ```powershell
   $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
   ```

4. Ange URL till startsidan till det värde som du vill. Värdet måste vara en underdomän sökväg till den publicerade appen. Exempel: Om du ändrar den URL-Adressen från `https://sharepoint-iddemo.msappproxy.net/` till `https://sharepoint-iddemo.msappproxy.net/hybrid/`, app-användare gå direkt till anpassad startsida.

   ```powershell
   $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
   ```

5. Kontrollera uppdateringen av startsidan.

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

6. För att bekräfta att ändringen har lyckats, kör du följande kommando från steg 2 igen.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   I vårt exempel bör utdata nu visas på följande sätt:

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

7. Starta om appen för att bekräfta att startsidan visas som den första skärmen som förväntat.

>[!NOTE]
>Alla ändringar du gör i appen kan återställa den URL-Adressen. Om din URL-Adressen återställer upprepar du stegen i det här avsnittet för att ställa in den igen.

## <a name="next-steps"></a>Nästa steg

- [Aktivera fjärråtkomst till SharePoint med Azure AD Application Proxy](application-proxy-integrate-with-sharepoint-server.md)
- [Självstudie: Lägga till ett lokalt program för fjärråtkomst via programproxy i Azure Active Directory](application-proxy-add-on-premises-application.md)