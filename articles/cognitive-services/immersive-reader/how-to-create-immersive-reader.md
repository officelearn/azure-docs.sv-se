---
title: Skapa en resurs för Avancerad läsare
titleSuffix: Azure Cognitive Services
description: Den här artikeln visar hur du skapar en ny fördjupad läsar resurs med en anpassad under domän och sedan konfigurerar Azure AD i din Azure-klient.
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: 53de4608616cb8f3b85bb88f1dbc5a4a79f4c02b
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77188850"
---
# <a name="create-an-immersive-reader-resource-and-configure-azure-active-directory-authentication"></a>Skapa en fördjupad läsar resurs och konfigurera Azure Active Directory autentisering

I den här artikeln innehåller vi ett skript som skapar en fördjupad läsar resurs och konfigurerar Azure Active Directory (Azure AD)-autentisering. Varje gången en fördjupad läsar resurs skapas, oavsett om det är det här skriptet eller i portalen, måste det också konfigureras med Azure AD-behörigheter. Det här skriptet hjälper dig med det.

Skriptet är utformat för att skapa och konfigurera alla nödvändiga integrerande läsare och Azure AD-resurser i ett enda steg. Men du kan också konfigurera Azure AD-autentisering för en befintlig fördjupad läsar resurs, om du t. ex. redan har skapat en i Azure Portal.

För vissa kunder kan det vara nödvändigt att skapa flera fördjupade Reader-resurser, för utveckling eller produktion, eller för flera olika regioner som din tjänst distribueras i. I dessa fall kan du komma tillbaka och använda skriptet flera gånger för att skapa olika fördjupade läsar resurser och få dem konfigurerade med Azure AD-behörigheterna.

Skriptet är utformat för att vara flexibelt. Den letar först efter befintliga integrerande läsare och Azure AD-resurser i din prenumeration och skapar dem bara vid behov om de inte redan finns. Om det är första gången du skapar en fördjupad läsare-resurs, kommer skriptet att göra allt du behöver. Om du bara vill använda det för att konfigurera Azure AD för en befintlig fördjupad läsar resurs som skapades i portalen, så gör det också. Det kan också användas för att skapa och konfigurera flera fördjupade läsares resurser.

## <a name="set-up-powershell-environment"></a>Konfigurera PowerShell-miljö

1. Börja med att öppna [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Se till att Cloud Shell är inställt på PowerShell i den övre vänstra List rutan eller genom att skriva `pwsh`.

1. Kopiera och klistra in följande kod avsnitt i gränssnittet.

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

1. Kör funktionen `Create-ImmersiveReaderResource`, och ange parametrarna efter behov.

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
    | SubscriptionName |Namnet på den Azure-prenumeration som ska användas för den fördjupade läsar resursen. Du måste ha en prenumeration för att kunna skapa en resurs. |
    | ResourceName |  Måste vara alfanumeriska och kan innehålla "-", förutsatt att "-" inte är det första eller sista. Längden får inte överskrida 63 tecken.|
    | ResourceSubdomain |En anpassad under domän krävs för den fördjupande läsar resursen. Under domänen används av SDK när tjänsten för avancerad läsare anropas för att starta läsaren. Under domänen måste vara globalt unik. Under domänen måste vara alfanumerisk och kan innehålla "-", förutsatt att "-" inte är det första eller sista. Längden får inte överskrida 63 tecken. Den här parametern är valfri om resursen redan finns. |
    | ResourceSKU |Alternativ: `S0`. Besök vår [Cognitive Services prissättnings sida](https://azure.microsoft.com/pricing/details/cognitive-services/immersive-reader/) om du vill veta mer om de olika tillgängliga SKU: er. Den här parametern är valfri om resursen redan finns. |
    | ResourceLocation |Alternativ: `eastus`, `eastus2`, `southcentralus`, `westus`, `westus2`, `australiaeast`, `southeastasia`, `centralindia`, `japaneast`, `northeurope`, `uksouth`, `westeurope`. Den här parametern är valfri om resursen redan finns. |
    | ResourceGroupName |Resurser skapas i resurs grupper inom prenumerationer. Ange namnet på en befintlig resurs grupp. Om resurs gruppen inte redan finns skapas en ny med det här namnet. |
    | ResourceGroupLocation |Om din resurs grupp inte finns måste du ange en plats där gruppen ska skapas. Du hittar en lista över platser genom att köra `az account list-locations`. Använd egenskapen *namn* (utan blank steg) för det returnerade resultatet. Den här parametern är valfri om din resurs grupp redan finns. |
    | AADAppDisplayName |Visnings namnet för Azure Active Directorys programmet. Om det inte går att hitta något befintligt Azure AD-program skapas en ny med det här namnet. Den här parametern är valfri om Azure AD-programmet redan finns. |
    | AADAppIdentifierUri |URI för Azure AD-appen. Om det inte går att hitta en befintlig Azure AD-App skapas en ny med denna URI. Till exempel `https://immersivereaderaad-mycompany`. |
    | AADAppClientSecret |Ett lösen ord som du skapar kommer att användas senare för att autentisera när du hämtar en token för att starta den fördjupade läsaren. Lösen ordet måste innehålla minst 16 tecken, innehålla minst 1 specialtecken och innehålla minst 1 numeriskt tecken. |

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

* Visa snabb starten för [Node. js](./quickstart-nodejs.md) för att se vad mer du kan göra med avancerad läsar-SDK med hjälp av Node. js
* Visa [python-självstudien](./tutorial-python.md) för att se vad mer du kan göra med avancerad läsar-SDK med hjälp av python
* Se snabb [självstudien](./tutorial-ios-picture-immersive-reader.md) för att se vad mer du kan göra med avancerad läsar-SDK med Swift
* Utforska SDK: [n för avancerad läsare](https://github.com/microsoft/immersive-reader-sdk) och [Avancerad läsare SDK-referens](./reference.md)




