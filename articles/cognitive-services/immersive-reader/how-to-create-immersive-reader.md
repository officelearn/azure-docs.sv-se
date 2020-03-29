---
title: Skapa en resurs för Avancerad läsare
titleSuffix: Azure Cognitive Services
description: Den här artikeln visar hur du skapar en ny Immersive Reader-resurs med en anpassad underdomän och sedan konfigurerar Azure AD i din Azure-klient.
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: 41efe4592c65ae3cdd85ce1b212554e50691905a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330727"
---
# <a name="create-an-immersive-reader-resource-and-configure-azure-active-directory-authentication"></a>Skapa en Immersive Reader-resurs och konfigurera Azure Active Directory-autentisering

I den här artikeln tillhandahåller vi ett skript som skapar en Immersive Reader-resurs och konfigurerar Azure Active Directory (Azure AD) autentisering. Varje gång en Immersive Reader-resurs skapas, oavsett om det är med det här skriptet eller i portalen, måste den också konfigureras med Azure AD-behörigheter. Detta skript kommer att hjälpa dig med det.

Skriptet är utformat för att skapa och konfigurera alla nödvändiga Immersive Reader- och Azure AD-resurser för er alla i ett steg. Men du kan också bara konfigurera Azure AD-autentisering för en befintlig Immersive Reader-resurs, om du till exempel råkar ha redan skapat en i Azure-portalen.

För vissa kunder kan det vara nödvändigt att skapa flera Immersive Reader-resurser, för utveckling kontra produktion eller kanske för flera olika regioner som tjänsten distribueras i. I dessa fall kan du komma tillbaka och använda skriptet flera gånger för att skapa olika Immersive Reader-resurser och få dem konfigurerade med Azure AD-behörigheterna.

Skriptet är utformat för att vara flexibelt. Den söker först efter befintliga Immersive Reader- och Azure AD-resurser i din prenumeration och skapar dem bara efter behov om de inte redan finns. Om det är första gången du skapar en Immersive Reader-resurs kommer skriptet att göra allt du behöver. Om du vill använda den bara för att konfigurera Azure AD för en befintlig Immersive Reader-resurs som har skapats i portalen, kommer det att göra det också. Det kan också användas för att skapa och konfigurera flera Immersive Reader-resurser.

## <a name="set-up-powershell-environment"></a>Konfigurera PowerShell-miljö

1. Börja med att öppna [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Kontrollera att molnskalet är inställt på PowerShell i den övre `pwsh`vänstra hand-rullgardinsmenyn eller genom att skriva .

1. Kopiera och klistra in följande kodavsnitt i skalet.

    ```azurepowershell-interactive
    function Create-ImmersiveReaderResource(
        [Parameter(Mandatory=$true, Position=0)] [String] $SubscriptionName,
        [Parameter(Mandatory=$true)] [String] $ResourceName,
        [Parameter(Mandatory=$true)] [String] $ResourceSubdomain,
        [Parameter(Mandatory=$true)] [String] $ResourceSKU,
        [Parameter(Mandatory=$true)] [String] $ResourceLocation,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupName,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupLocation,
        [Parameter(Mandatory=$true)] [String] $AADAppDisplayName="ImmersiveReaderAAD",
        [Parameter(Mandatory=$true)] [String] $AADAppIdentifierUri,
        [Parameter(Mandatory=$true)] [String] $AADAppClientSecret
    )
    {
        $unused = ''
        if (-not [System.Uri]::TryCreate($AADAppIdentifierUri, [System.UriKind]::Absolute, [ref] $unused)) {
            throw "Error: AADAppIdentifierUri must be a valid URI"
        }

        Write-Host "Setting the active subscription to '$SubscriptionName'"
        $subscriptionExists = Get-AzSubscription -SubscriptionName $SubscriptionName
        if (-not $subscriptionExists) {
            throw "Error: Subscription does not exist"
        }
        az account set --subscription $SubscriptionName

        $resourceGroupExists = az group exists --name $ResourceGroupName
        if ($resourceGroupExists -eq "false") {
            Write-Host "Resource group does not exist. Creating resource group"
            $groupResult = az group create --name $ResourceGroupName --location $ResourceGroupLocation
            if (-not $groupResult) {
                throw "Error: Failed to create resource group"
            }
            Write-Host "Resource group created successfully"
        }

        # Create an Immersive Reader resource if it doesn't already exist
        $resourceId = az cognitiveservices account show --resource-group $ResourceGroupName --name $ResourceName --query "id" -o tsv
        if (-not $resourceId) {
            Write-Host "Creating the new Immersive Reader resource '$ResourceName' (SKU '$ResourceSKU') in '$ResourceLocation' with subdomain '$ResourceSubdomain'"
            $resourceId = az cognitiveservices account create `
                            --name $ResourceName `
                            --resource-group $ResourceGroupName `
                            --kind ImmersiveReader `
                            --sku $ResourceSKU `
                            --location $ResourceLocation `
                            --custom-domain $ResourceSubdomain `
                            --query "id" `
                            -o tsv

            if (-not $resourceId) {
                throw "Error: Failed to create Immersive Reader resource"
            }
            Write-Host "Immersive Reader resource created successfully"
        }

        # Create an Azure Active Directory app if it doesn't already exist
        $clientId = az ad app show --id $AADAppIdentifierUri --query "appId" -o tsv
        if (-not $clientId) {
            Write-Host "Creating new Azure Active Directory app"
            $clientId = az ad app create --password $AADAppClientSecret --display-name $AADAppDisplayName --identifier-uris $AADAppIdentifierUri --query "appId" -o tsv

            if (-not $clientId) {
                throw "Error: Failed to create Azure Active Directory app"
            }
            Write-Host "Azure Active Directory app created successfully"
        }

        # Create a service principal if it doesn't already exist
        $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv
        if (-not $principalId) {
            Write-Host "Creating new service principal"
            az ad sp create --id $clientId | Out-Null
            $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv

            if (-not $principalId) {
                throw "Error: Failed to create new service principal"
            }
            Write-Host "New service principal created successfully"
        }

        # Sleep for 5 seconds to allow the new service principal to propagate
        Write-Host "Sleeping for 5 seconds"
        Start-Sleep -Seconds 5

        Write-Host "Granting service principal access to the newly created Immersive Reader resource"
        $accessResult = az role assignment create --assignee $principalId --scope $resourceId --role "Cognitive Services User"
        if (-not $accessResult) {
            throw "Error: Failed to grant service principal access"
        }
        Write-Host "Service principal access granted successfully"

        # Grab the tenant ID, which is needed when obtaining an Azure AD token
        $tenantId = az account show --query "tenantId" -o tsv

        # Collect the information needed to obtain an Azure AD token into one object
        $result = @{}
        $result.TenantId = $tenantId
        $result.ClientId = $clientId
        $result.ClientSecret = $AADAppClientSecret
        $result.Subdomain = $ResourceSubdomain

        Write-Host "Success! " -ForegroundColor Green -NoNewline
        Write-Host "Save the following JSON object to a text file for future reference:"
        Write-Output (ConvertTo-Json $result)
    }
    ```

1. Kör funktionen `Create-ImmersiveReaderResource`och ange parametrarna efter behov.

    ```azurepowershell-interactive
    Create-ImmersiveReaderResource
      -SubscriptionName '<SUBSCRIPTION_NAME>' `
      -ResourceName '<RESOURCE_NAME>' `
      -ResourceSubdomain '<RESOURCE_SUBDOMAIN>' `
      -ResourceSKU '<RESOURCE_SKU>' `
      -ResourceLocation '<RESOURCE_LOCATION>' `
      -ResourceGroupName '<RESOURCE_GROUP_NAME>' `
      -ResourceGroupLocation '<RESOURCE_GROUP_LOCATION>' `
      -AADAppDisplayName '<AAD_APP_DISPLAY_NAME>' `
      -AADAppIdentifierUri '<AAD_APP_IDENTIFIER_URI>' `
      -AADAppClientSecret '<AAD_APP_CLIENT_SECRET>'
    ```

    | Parameter | Kommentarer |
    | --- | --- |
    | SubscriptionName |Namn på den Azure-prenumeration som ska användas för din Immersive Reader-resurs. Du måste ha en prenumeration för att kunna skapa en resurs. |
    | ResourceName |  Måste vara alfanumeriskt och kan innehålla '-', så länge '-' inte är det första eller sista tecknet. Längden får inte överstiga 63 tecken.|
    | ResourceSubdomain |En anpassad underdomän behövs för din Immersive Reader-resurs. Underdomänen används av SDK när du anropar tjänsten Immersive Reader för att starta läsaren. Underdomänen måste vara globalt unik. Underdomänen måste vara alfanumerisk och innehålla '-', så länge '-' inte är det första eller sista tecknet. Längden får inte överstiga 63 tecken. Den här parametern är valfri om resursen redan finns. |
    | ResourceSKU |Alternativ: `S0`. Besök vår [prissida för Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/immersive-reader/) om du vill veta mer om varje tillgänglig SKU. Den här parametern är valfri om resursen redan finns. |
    | ResourceLocation |Alternativ: `eastus` `eastus2`, `southcentralus` `westus`, `westus2` `australiaeast`, `southeastasia` `centralindia`, `japaneast` `northeurope`, `uksouth` `westeurope`, , , , , , . Den här parametern är valfri om resursen redan finns. |
    | ResourceGroupName |Resurser skapas i resursgrupper inom prenumerationer. Ange namnet på en befintlig resursgrupp. Om resursgruppen inte redan finns skapas en ny med det här namnet. |
    | ResursgruppLokalisering |Om resursgruppen inte finns måste du ange en plats där du kan skapa gruppen. Om du vill hitta `az account list-locations`en lista över platser kör du . Använd *name* namnegenskapen (utan blanksteg) för det returnerade resultatet. Den här parametern är valfri om resursgruppen redan finns. |
    | AADAppDisplayName |Visningsnamnet för Azure Active Directory-programmet. Om ett befintligt Azure AD-program inte hittas skapas ett nytt med det här namnet. Den här parametern är valfri om Azure AD-programmet redan finns. |
    | AADAppIdentifierUri |Uri för Azure AD-appen. Om en befintlig Azure AD-app inte hittas skapas en ny med den här URI:n. Till exempel `https://immersivereaderaad-mycompany`. |
    | AADAppClientSecret |Ett lösenord som du skapar som används senare för att autentisera när du skaffar en token för att starta Immersive Reader. Lösenordet måste vara minst 16 tecken långt, innehålla minst ett specialtecken och innehålla minst ett numeriskt tecken. |

1. Kopiera JSON-utdata till en textfil för senare användning. Resultatet bör likna följande.

    ```json
    {
      "TenantId": "...",
      "ClientId": "...",
      "ClientSecret": "...",
      "Subdomain": "..."
    }
    ```

## <a name="next-steps"></a>Nästa steg

* Visa [snabbstarten Node.js](./quickstart-nodejs.md) för att se vad mer du kan göra med Immersive Reader SDK med Node.js
* Visa [Python-självstudien](./tutorial-python.md) för att se vad mer du kan göra med Immersive Reader SDK med Python
* Visa [Swift-självstudien](./tutorial-ios-picture-immersive-reader.md) för att se vad mer du kan göra med Immersive Reader SDK med Swift
* Utforska [den uppslukande läsaren SDK](https://github.com/microsoft/immersive-reader-sdk) och [Immersive Reader SDK-referensen](./reference.md)




