---
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 07/23/2019
ms.openlocfilehash: 8754504655cdd08c9bf9f89311cb6c5d1057f0e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78262491"
---
## <a name="authenticate-with-azure-active-directory"></a>Autentisera med hjälp av Azure Active Directory

> [!IMPORTANT]
> 1. För närvarande **är det bara** API:et för datorseende, ansikts-API, textanalys-API, uppslukande läsare, formulärre recognizer, avvikelsedetektor och alla Bing-tjänster utom Bing Custom Search stöder autentisering med Azure Active Directory (AAD).
> 2. AAD-autentisering måste alltid användas tillsammans med anpassade underdomännamn för din Azure-resurs. [Regionala slutpunkter](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#is-there-a-list-of-regional-endpoints) stöder inte AAD-autentisering.

I föregående avsnitt visade vi hur du autentiserar mot Azure Cognitive Services med antingen en prenumerationsnyckel för en tjänst eller flera tjänster. Även om dessa nycklar ger en snabb och enkel väg att starta utvecklingen, misslyckas de i mer komplexa scenarier som kräver rollbaserade åtkomstkontroller. Låt oss ta en titt på vad som krävs för att autentisera med Azure Active Directory (AAD).

I följande avsnitt använder du antingen Azure Cloud Shell-miljön eller Azure CLI för att skapa en underdomän, tilldela roller och skaffa en innehavartoken för att anropa Azure Cognitive Services. Om du fastnar finns länkar i varje avsnitt med alla tillgängliga alternativ för varje kommando i Azure Cloud Shell/Azure CLI.

### <a name="create-a-resource-with-a-custom-subdomain"></a>Skapa en resurs med en anpassad underdomän

Det första steget är att skapa en anpassad underdomän. Om du vill använda en befintlig Cognitive Services-resurs som inte har anpassat underdomännamn följer du instruktionerna i [anpassade underdomäner](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#how-does-this-impact-existing-resources) för kognitiva tjänster för att aktivera anpassad underdomän för din resurs.

1. Börja med att öppna Azure Cloud Shell. Välj sedan [en prenumeration:](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext?view=azps-3.3.0)

   ```powershell-interactive
   Set-AzContext -SubscriptionName <SubscriptionName>
   ```

2. Skapa sedan [en Cognitive Services-resurs](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0) med en anpassad underdomän. Underdomännamnet måste vara globalt unikt och får inte innehålla specialtecken, till exempel:".", "!", "".

   ```powershell-interactive
   New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <ACCOUNT_NAME> -Type <ACCOUNT_TYPE> -SkuName <SUBSCRIPTION_TYPE> -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>
   ```

3. Om det lyckas bör **slutpunkten** visa det underdomännamn som är unikt för din resurs.


### <a name="assign-a-role-to-a-service-principal"></a>Tilldela en roll till ett tjänsthuvudnamn

Nu när du har en anpassad underdomän som är associerad med din resurs måste du tilldela en roll till ett huvudnamn för tjänsten.

> [!NOTE]
> Tänk på att AAD-rolltilldelningar kan ta upp till fem minuter att sprida.

1. Låt oss först registrera en [AAD-ansökan](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0).

   ```powershell-interactive
   $SecureStringPassword = ConvertTo-SecureString -String <YOUR_PASSWORD> -AsPlainText -Force

   New-AzADApplication -DisplayName <APP_DISPLAY_NAME> -IdentifierUris <APP_URIS> -Password $SecureStringPassword
   ```

   Du kommer att behöva **ApplicationId** i nästa steg.

2. Därefter måste du [skapa ett tjänsthuvudnamn](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0) för AAD-programmet.

   ```powershell-interactive
   New-AzADServicePrincipal -ApplicationId <APPLICATION_ID>
   ```

   >[!NOTE]
   > Om du registrerar ett program i Azure-portalen slutförs det här steget åt dig.

3. Det sista steget är att [tilldela rollen "Cognitive Services User"](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0) till tjänstens huvudnamn (begränsad till resursen). Genom att tilldela en roll ger du tjänstens huvudåtkomst till den här resursen. Du kan bevilja samma tjänsthuvudnamnsåtkomst till flera resurser i prenumerationen.
   >[!NOTE]
   > ObjectId för tjänstens huvudnamn används, inte ObjectId för programmet.
   > Den ACCOUNT_ID kommer att vara Azure-resurs-ID för Cognitive Services-kontot som du skapade. Du kan hitta Azure-resurs-ID från "egenskaper" för resursen i Azure-portalen.

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId <SERVICE_PRINCIPAL_OBJECTID> -Scope <ACCOUNT_ID> -RoleDefinitionName "Cognitive Services User"
   ```

### <a name="sample-request"></a>Exempel på begäran

I det här exemplet används ett lösenord för att autentisera tjänstens huvudnamn. Token som tillhandahålls används sedan för att anropa API:et för visuellt innehåll.

1. Hämta din **TenantId:**
   ```powershell-interactive
   $context=Get-AzContext
   $context.Tenant.Id
   ```

2. Hämta en token:
   ```powershell-interactive
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $secureSecretObject = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.SecureClientSecret" -ArgumentList $SecureStringPassword   
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, $secureSecretObject
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ```
3. Anropa API:et för visuellt innehåll:
   ```powershell-interactive
   $url = $account.Endpoint+"vision/v1.0/models"
   $result = Invoke-RestMethod -Uri $url  -Method Get -Headers @{"Authorization"=$token.CreateAuthorizationHeader()} -Verbose
   $result | ConvertTo-Json
   ```

Alternativt kan tjänstens huvudnamn autentiseras med ett certifikat. Förutom tjänstens huvudnamn stöds användarens huvudnamn också genom att ha behörigheter delegerade via ett annat AAD-program. I det här fallet, i stället för lösenord eller certifikat, användare skulle uppmanas för tvåfaktorsautentisering när du hämtar token.
