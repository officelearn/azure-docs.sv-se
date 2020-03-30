---
title: Distribuera ett hanteringsverktyg för Windows Virtual Desktop med tjänstens huvudnamn - Azure
description: Distribuera hanteringsverktyget för Windows Virtual Desktop med PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0838edb03c4868548f3d09f14d71ec7016e670a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127790"
---
# <a name="deploy-a-management-tool-with-powershell"></a>Distribuera ett hanteringsverktyg med PowerShell

Den här artikeln visar hur du distribuerar hanteringsverktyget med PowerShell.

## <a name="important-considerations"></a>Att tänka på

Varje Azure Active Directory (Azure AD) klientens prenumeration behöver en egen separat distribution av hanteringsverktyget. Det här verktyget stöder inte Azure AD Business-to-Business (B2B) scenarier. 

Det här hanteringsverktyget är ett exempel. Microsoft kommer att tillhandahålla viktiga säkerhets- och kvalitetsuppdateringar. [Källkoden är tillgänglig i GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Oavsett om du är kund eller partner rekommenderar vi att du anpassar verktyget för att tillgodose dina affärsbehov.

Följande webbläsare är kompatibla med hanteringsverktyget:

- Google Chrome 68 eller senare
- Microsoft Edge 40.15063 eller senare
- Mozilla Firefox 52.0 eller senare
- Safari 10 eller senare (endast macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>Vad du behöver för att distribuera hanteringsverktyget

Innan du distribuerar hanteringsverktyget behöver du en Azure Active Directory-användare (Azure AD) för att skapa en appregistrering och distribuera hanteringsgränssnittet. Den här användaren måste:

- Ha behörighet att skapa resurser i din Azure-prenumeration
- Har behörighet att skapa ett Azure AD-program. Följ dessa steg för att kontrollera om användaren har de behörigheter som krävs genom att följa instruktionerna i [Obligatoriska behörigheter](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

För att kunna distribuera och konfigurera hanteringsverktyget måste du först hämta följande PowerShell-skript från [GitHub-repo-repo-mallarna för FJÄRRSKRIVBORDS-mallar](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy/scripts) och spara dem i samma mapp på den lokala datorn.

  - skapaWvdMgmtUxAppRegistration.ps1
  - updateWvdMgmtUxApiUrl.ps1

När du har distribuerat och konfigurerat hanteringsverktyget rekommenderar vi att du ber en användare att starta hanteringsgränssnittet för att se till att allt fungerar. Användaren som startar hanteringsgränssnittet måste ha en rolltilldelning som gör att de kan visa eller redigera Windows Virtual Desktop-klienten.

## <a name="set-up-powershell"></a>Konfigurera PowerShell

Kom igång genom att logga in på både Az- och Azure AD PowerShell-modulerna. Så här loggar du in:

1. Öppna PowerShell som administratör och navigera till katalogen där du sparade PowerShell-skripten.
2. Logga in på Azure med ett konto som har behörigheter för ägare eller deltagare för den Azure-prenumeration som du planerar att använda för att skapa hanteringsverktyget genom att köra följande cmdlet:

    ```powershell
    Login-AzAccount
    ```

3. Kör följande cmdlet för att logga in på Azure AD med samma konto som du använde för Az PowerShell-modulen:

    ```powershell
    Connect-AzureAD
    ```

4. Därefter navigerar du till mappen där du sparade de två PowerShell-skripten från GitHub-repo-repo-repoen för RDS-mallar.

Håll det PowerShell-fönster som du använde för att logga in öppet för att köra ytterligare PowerShell-cmdletar när du är inloggad.

## <a name="create-an-azure-active-directory-app-registration"></a>Skapa en registrering av Active Directory-program i Azure

Kör följande kommandon för att skapa appregistreringen med nödvändiga API-behörigheter:

```powershell
$appName = Read-Host -Prompt "Enter a unique name for the management tool's app registration. The name can't contain spaces or special characters."
$subscriptionId = Read-Host -Prompt "Enter the Azure subscription ID where you will be deploying the management tool."

.\createWvdMgmtUxAppRegistration.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Nu när du har slutfört registreringen av Azure AD-appen kan du distribuera hanteringsverktyget.

## <a name="deploy-the-management-tool"></a>Distribuera hanteringsverktyget

Kör följande PowerShell-kommandon för att distribuera hanteringsverktyget och associera det med det huvudnamn för tjänsten som du just skapade:
     
```powershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateParameters = @{}
$templateParameters.Add('isServicePrincipal', $true)
$templateParameters.Add('azureAdminUserPrincipalNameOrApplicationId', $ServicePrincipalCredentials.UserName)
$templateParameters.Add('azureAdminPassword', $servicePrincipalCredentials.Password)
$templateParameters.Add('applicationName', $appName)

Get-AzSubscription -SubscriptionId $subscriptionId | Select-AzSubscription
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/mainTemplate.json" `
    -TemplateParameterObject $templateParameters `
    -Verbose
```

När du har skapat webbappen måste du lägga till en omdirigerings-URI till Azure AD-programmet för att kunna logga in användare.

## <a name="set-the-redirect-uri"></a>Ange omdirigerings-URI

Kör följande PowerShell-kommandon för att hämta webbapp-URL:en och ange den som autentiseringsomdirigerings-URI (kallas även svars-URL):

```powershell
$webApp = Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $appName
$redirectUri = "https://" + $webApp.DefaultHostName + "/"
Get-AzureADApplication -All $true | where { $_.AppId -match $servicePrincipalCredentials.UserName } | Set-AzureADApplication -ReplyUrls $redirectUri  
```

Nu när du har lagt till en omdirigera URI måste du uppdatera API-URL:en så att hanteringsverktyget kan interagera med API-serverdelstjänsten.

## <a name="update-the-api-url-for-the-web-application"></a>Uppdatera API-URL:en för webbprogrammet

Kör följande skript för att uppdatera API-URL-konfigurationen i webbprogrammets klientdel:

```powershell
.\updateWvdMgmtUxApiUrl.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Nu när du har konfigurerat webbappen för hanteringsverktyg är det dags att verifiera Azure AD-programmet och ge medgivande.

## <a name="verify-the-azure-ad-application-and-provide-consent"></a>Verifiera Azure AD-programmet och ge medgivande

Så här verifierar du konfigurationen av Azure AD-program och ger medgivande:

1. Öppna din webbläsare och logga in på [Azure-portalen](https://portal.azure.com/) med ditt administrativa konto.
2. Sök efter **appregistreringar** i sökfältet högst upp i Azure-portalen och välj objektet under **Tjänster**.
3. Välj **Alla program** och sök efter det unika appnamnet som du angav för PowerShell-skriptet i Skapa en Azure Active [Directory-appregistrering](#create-an-azure-active-directory-app-registration).
4. På panelen till vänster i webbläsaren väljer du **Autentisering** och kontrollerar att omdirigerings-URI:n är samma som webbapp-URL:en för hanteringsverktyget, som visas i följande bild.
   
   [![Autentiseringssidan med den angivna](media/management-ui-redirect-uri-inline.png) omdirigerings-URI:n](media/management-ui-redirect-uri-expanded.png#lightbox)

5. På den vänstra panelen väljer du **API-behörigheter** för att bekräfta att behörigheter har lagts till. Om du är global administratör väljer du **knappen Bevilja `tenantname` administratörsmedgivande för** och följer dialogruppen för att ge administratörsmedgivande för din organisation.
    
    [![Sidan](media/management-ui-permissions-inline.png) API-behörigheter](media/management-ui-permissions-expanded.png#lightbox)

Nu kan du börja använda hanteringsverktyget.

## <a name="use-the-management-tool"></a>Använda hanteringsverktyget

Nu när du har konfigurerat hanteringsverktyget när som helst kan du starta det när som helst, var som helst. Så här startar du verktyget:

1. Öppna webbadressen till webbappen i en webbläsare. Om du inte kommer ihåg webbadressen kan du logga in på Azure, hitta apptjänsten som du har distribuerat för hanteringsverktyget och sedan välja URL:en.
2. Logga in med autentiseringsuppgifterna för Windows Virtual Desktop.
   
   > [!NOTE]
   > Om du inte gav administratörsmedgivande när du konfigurerade hanteringsverktyget måste varje användare som loggar in ge sitt eget användarmedgivande för att kunna använda verktyget.

3. När du uppmanas att välja en klientgrupp väljer du **Standardklientgrupp** i listrutan.
4. När du väljer **Standardklientgrupp**ska en meny visas till vänster i fönstret. På den här menyn hittar du namnet på din klientgrupp och väljer det.
   
   > [!NOTE]
   > Om du har en anpassad klientgrupp anger du namnet manuellt i stället för att välja från listrutan.

## <a name="report-issues"></a>Rapportera problem

Om du stöter på problem med hanteringsverktyget eller andra Windows Virtual Desktop-verktyg följer du anvisningarna i [Azure Resource Manager-mallar för Fjärrskrivbordstjänster för](https://github.com/Azure/RDS-Templates/blob/master/README.md) att rapportera dem på GitHub.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du distribuerar och ansluter till hanteringsverktyget kan du lära dig hur du använder Azure Service Health för att övervaka serviceproblem och hälsorekommendationer. Mer information finns i [vår uppsättning tjänst varningar handledning](./set-up-service-alerts.md).
