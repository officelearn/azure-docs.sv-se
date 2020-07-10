---
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 05/11/2020
ms.openlocfilehash: 6bbdd3eb62229c3f8f180d2618dd25062ff0c1e9
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86062686"
---
## <a name="authenticate-with-azure-active-directory"></a>Autentisering via Azure Active Directory

> [!IMPORTANT]
> 1. För närvarande är det **bara** API för visuellt innehåll, Ansikts-API, API för textanalys, fördjupad läsare, formulär igenkänning, avvikelse detektor och alla Bing-tjänster utom anpassad sökning i Bing stöd för autentisering med Azure Active Directory (AAD).
> 2. AAD-autentisering måste alltid användas tillsammans med det anpassade under domän namnet för din Azure-resurs. [Regionala slut punkter](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#is-there-a-list-of-regional-endpoints) stöder inte AAD-autentisering.

I föregående avsnitt visade vi dig hur du autentiserar mot Azure Cognitive Services med en prenumerations nyckel för en enda tjänst eller flera tjänster. Även om dessa nycklar ger en snabb och enkel väg för att börja utveckla, är de korta i mer komplexa scenarier som kräver rollbaserade åtkomst kontroller. Låt oss ta en titt på vad som krävs för att autentisera med hjälp av Azure Active Directory (AAD).

I följande avsnitt använder du antingen Azure Cloud Shells miljön eller Azure CLI för att skapa en under domän, tilldela roller och hämta en Bearer-token för att anropa Azure-Cognitive Services. Om du får fastnat finns länkar i varje avsnitt med alla tillgängliga alternativ för varje kommando i Azure Cloud Shell/Azure CLI.

### <a name="create-a-resource-with-a-custom-subdomain"></a>Skapa en resurs med en anpassad under domän

Det första steget är att skapa en anpassad under domän. Om du vill använda en befintlig Cognitive Services-resurs som inte har något anpassat under domän namn följer du anvisningarna i [Cognitive Services anpassade under domäner](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#how-does-this-impact-existing-resources) för att aktivera anpassad under domän för resursen.

1. Börja med att öppna Azure Cloud Shell. [Välj en prenumeration](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext?view=azps-3.3.0):

   ```powershell-interactive
   Set-AzContext -SubscriptionName <SubscriptionName>
   ```

2. Skapa sedan [en Cognitive Services-resurs](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0) med en anpassad under domän. Under domän namnet måste vara globalt unikt och får inte innehålla specialtecken, till exempel: ".", "!", ",".

   ```powershell-interactive
   $account = New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <ACCOUNT_NAME> -Type <ACCOUNT_TYPE> -SkuName <SUBSCRIPTION_TYPE> -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>
   ```

3. Om det lyckas bör **slut punkten** Visa det under domän namn som är unikt för din resurs.


### <a name="assign-a-role-to-a-service-principal"></a>Tilldela en roll till ett huvud namn för tjänsten

Nu när du har en anpassad under domän som är kopplad till din resurs, kommer du att behöva tilldela en roll till ett huvud namn för tjänsten.

> [!NOTE]
> Tänk på att AAD-roll tilldelningar kan ta upp till fem minuter innan de sprids.

1. Först ska vi registrera ett [AAD-program](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0).

   ```powershell-interactive
   $SecureStringPassword = ConvertTo-SecureString -String <YOUR_PASSWORD> -AsPlainText -Force

   $app = New-AzADApplication -DisplayName <APP_DISPLAY_NAME> -IdentifierUris <APP_URIS> -Password $SecureStringPassword
   ```

   Du kommer att behöva **ApplicationId** i nästa steg.

2. Därefter måste du [skapa ett huvud namn för tjänsten](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0) för AAD-programmet.

   ```powershell-interactive
   New-AzADServicePrincipal -ApplicationId <APPLICATION_ID>
   ```

   >[!NOTE]
   > Om du registrerar ett program i Azure Portal har det här steget slutförts.

3. Det sista steget är att [tilldela rollen "Cognitive Services användare"](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0) till tjänstens huvud namn (omfattas av resursen). Genom att tilldela en roll beviljar du tjänstens huvud namns åtkomst till den här resursen. Du kan ge samma tjänst huvud namn åtkomst till flera resurser i din prenumeration.
   >[!NOTE]
   > Objekt-ID: t för tjänstens huvud namn används, inte ObjectId för programmet.
   > ACCOUNT_ID är Azure-resurs-ID: t för det Cognitive Services konto som du har skapat. Du hittar Azure Resource ID från "egenskaper" för resursen i Azure Portal.

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId <SERVICE_PRINCIPAL_OBJECTID> -Scope <ACCOUNT_ID> -RoleDefinitionName "Cognitive Services User"
   ```

### <a name="sample-request"></a>Exempel förfrågan

I det här exemplet används ett lösen ord för att autentisera tjänstens huvud namn. Den angivna token används sedan för att anropa API för visuellt innehåll.

1. Hämta ditt **TenantId**:
   ```powershell-interactive
   $context=Get-AzContext
   $context.Tenant.Id
   ```

2. Hämta en token:
   > [!NOTE]
   > Om du använder Azure Cloud Shell är `SecureClientSecret` klassen inte tillgänglig. 

   #### <a name="powershell"></a>[PowerShell](#tab/powershell)
   ```powershell-interactive
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $secureSecretObject = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.SecureClientSecret" -ArgumentList $SecureStringPassword   
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, $secureSecretObject
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ```
   
   #### <a name="azure-cloud-shell"></a>[Azure Cloud Shell](#tab/azure-cloud-shell)
   ```Azure Cloud Shell
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, <YOUR_PASSWORD>
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ``` 

   ---

3. Anropa API för visuellt innehåll:
   ```powershell-interactive
   $url = $account.Endpoint+"vision/v1.0/models"
   $result = Invoke-RestMethod -Uri $url  -Method Get -Headers @{"Authorization"=$token.CreateAuthorizationHeader()} -Verbose
   $result | ConvertTo-Json
   ```

Alternativt kan tjänstens huvud namn autentiseras med ett certifikat. Förutom tjänstens huvud namn stöds även användarens huvud konto genom att ha behörighet som delegerats via ett annat AAD-program. I det här fallet, i stället för lösen ord eller certifikat, uppmanas användarna att ange tvåfaktorautentisering när de hämtar token.

## <a name="authorize-access-to-managed-identities"></a>Ge åtkomst till hanterade identiteter
 
Cognitive Services stöd Azure Active Directory (Azure AD)-autentisering med [hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Hanterade identiteter för Azure-resurser kan ge åtkomst till Cognitive Services-resurser med hjälp av Azure AD-autentiseringsuppgifter från program som körs i Azure Virtual Machines (VM), Function-appar, skalnings uppsättningar för virtuella datorer och andra tjänster. Genom att använda hanterade identiteter för Azure-resurser tillsammans med Azure AD-autentisering kan du undvika att lagra autentiseringsuppgifter med dina program som körs i molnet.  

### <a name="enable-managed-identities-on-a-vm"></a>Aktivera hanterade identiteter på en virtuell dator

Innan du kan använda hanterade identiteter för Azure-resurser för att ge åtkomst till Cognitive Services resurser från din virtuella dator måste du aktivera hanterade identiteter för Azure-resurser på den virtuella datorn. Information om hur du aktiverar hanterade identiteter för Azure-resurser finns i:

- [Azure-portalen](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm)
- [Azure CLI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm)
- [Azure Resource Manager-mall](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm)
- [Azure Resource Manager klient bibliotek](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm)

Mer information om hanterade identiteter finns i [hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
