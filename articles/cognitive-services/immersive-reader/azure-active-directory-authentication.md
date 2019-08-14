---
title: Azure Active Directory (Azure AD)-autentisering
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
ms.openlocfilehash: e4b792a04b4926fdb56f37c089e73b90cde905d3
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990150"
---
# <a name="use-azure-active-directory-azure-ad-authentication-with-the-immersive-reader-service"></a>Använd Azure Active Directory (Azure AD)-autentisering med fördjupad läsar tjänsten

I följande avsnitt ska du använda antingen Azure Cloud Shells miljön eller Azure CLI för att skapa en ny fördjupad läsar resurs med en anpassad under domän och sedan konfigurera Azure AD i din Azure-klient. När du har slutfört den inledande konfigurationen kommer du att anropa Azure AD för att få en åtkomsttoken, på samma sätt som när du använder SDK: n för avancerad läsare. Om du har fastnat finns länkar i varje avsnitt med alla tillgängliga alternativ för vart och ett av Azure CLI-kommandona.

## <a name="create-an-immersive-reader-resource-with-a-custom-subdomain"></a>Skapa en fördjupad läsar resurs med en anpassad under domän

1. Börja med att öppna [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). [Välj en prenumeration](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0#description):

   ```azurecli-interactive
   Select-AzSubscription -SubscriptionName <YOUR_SUBSCRIPTION>
   ```

2. Skapa sedan [en fördjupad läsar resurs](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0) med en anpassad under domän.

   >[!NOTE]
   > Under domänens namn används i avancerad läsar-SDK när läsaren startas med funktionen launchAsync.

   -SkuName kan vara F0 (kostnads fri nivå) eller S0 (standard nivån, även kostnads fritt under den offentliga för hands versionen). S0-nivån har en högre anrops frekvens gräns och ingen månatlig kvot för antalet anrop.

   -Location kan vara något av `eastus`följande: `australiaeast`, `japaneast` `westus` `centralindia` ,,,`northeurope`,`westeurope`

   -CustomSubdomainName måste vara globalt unikt och får inte innehålla specialtecken, till exempel: ".", "!", ",".


   ```azurecli-interactive
   $resource = New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <RESOURCE_NAME> -Type ImmersiveReader -SkuName S0 -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>

   // Display the Resource info
   $resource
   ```

   Om det lyckas bör resurs **slut punkten** Visa det under domän namn som är unikt för din resurs.

   Här fångar vi det nyligen skapade resurs objekt till en **$Resource** -variabel, eftersom det kommer att användas senare när du beviljar åtkomst till den här resursen.


   >[!NOTE]
   > Om du skapar en resurs i Azure Portal används resursen "namn" som den anpassade under domänen. Du kan kontrol lera namnet på under domänen i portalen genom att gå till resurs översikts sidan och söka efter under domänen i slut punkten som visas där, till `https://[SUBDOMAIN].cognitiveservices.azure.com/`exempel. Du kan också kontrol lera detta senare när du behöver hämta under domänen för integrering med SDK.

   Om resursen skapades i portalen kan du också [Hämta en befintlig resurs](https://docs.microsoft.com/powershell/module/az.cognitiveservices/get-azcognitiveservicesaccount?view=azps-1.8.0) nu.

   ```azurecli-interactive
   $resource = Get-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <RESOURCE_NAME>

   // Display the Resource info
   $resource
   ```

## <a name="assign-a-role-to-a-service-principal"></a>Tilldela en roll till ett huvud namn för tjänsten

Nu när du har en anpassad under domän som är kopplad till din resurs, måste du tilldela en roll till ett huvud namn för tjänsten.

1. Först ska vi [skapa ett Azure AD-program](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0).

   >[!NOTE]
   > Lösen ordet, även kallat klient hemlighet, kommer att användas när du hämtar autentiseringstoken.

   ```azurecli-interactive
   $password = "<YOUR_PASSWORD>"
   $secureStringPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
   $aadApp = New-AzADApplication -DisplayName ImmersiveReaderAAD -IdentifierUris http://ImmersiveReaderAAD -Password $secureStringPassword

   // Display the Azure AD app info
   $aadApp
   ```

   Här fångar vi det nya Azure AD App-objektet i en **$aadApp** -variabel som du kan använda i nästa steg.

2. Därefter måste du [skapa ett huvud namn för tjänsten](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0) för Azure AD-programmet.

   ```azurecli-interactive
   $principal = New-AzADServicePrincipal -ApplicationId $aadApp.ApplicationId

   // Display the service principal info
   $principal
   ```

   Här fångar vi det nyligen skapade tjänst huvud objekts objekt till en **$Principal** -variabel som ska användas i nästa steg.


3. Det sista steget är att [tilldela rollen "Cognitive Services användare"](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0) till tjänstens huvud namn (omfattas av resursen). Genom att tilldela en roll beviljar du tjänstens huvud namn åtkomst till den här resursen. Du kan ge samma tjänst huvud namn åtkomst till flera resurser i din prenumeration.

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId $principal.Id -Scope $resource.Id -RoleDefinitionName "Cognitive Services User"
   ```

   >[!NOTE]
   > Det här steget måste utföras för varje fördjupad läsar resurs som du skapar. Om du till exempel skapar flera resurser för olika globala regioner måste du utföra det här steget för var och en av dessa regionala resurser så att Azure AD-autentisering fungerar för alla. Eller, om du skapar en ny resurs vid ett senare tillfälle, behöver du bara utföra det här steget för roll tilldelning för den nya resursen.


## <a name="obtain-an-azure-ad-token"></a>Skaffa en Azure AD-token

I det här exemplet används ditt lösen ord för att autentisera tjänstens huvud namn för att hämta en Azure AD-token.

1. Hämta ditt **TenantId**:
   ```azurecli-interactive
   $context = Get-AzContext
   $context.Tenant.Id
   ```

2. Hämta en token:
   ```azurecli-interactive
   $authority = "https://login.windows.net/" + $context.Tenant.Id
   $resource = "https://cognitiveservices.azure.com/"
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $aadApp.ApplicationId, $password
   $token = $authContext.AcquireTokenAsync($resource, $clientCredential).Result
   $token
   ```

   >[!NOTE]
   > SDK: n för avancerad läsare använder AccessToken-egenskapen för token, t. ex. $token. AccessToken. Se SDK- [referens](reference.md) och kod [exempel](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples) för mer information.

Alternativt kan tjänstens huvud namn autentiseras med ett certifikat. Förutom ett huvud namn för tjänsten stöds även användar huvud konton genom att ha behörighet som delegerats via ett annat Azure AD-program. I det här fallet, i stället för lösen ord eller certifikat, uppmanas användarna att ange tvåfaktorautentisering när de hämtar tokens.

## <a name="next-steps"></a>Nästa steg

* I självstudien om [Node. js](./tutorial-nodejs.md) kan du se vad mer du kan göra med avancerad läsar-SDK med hjälp av Node. js
* Visa [python](./tutorial-python.md) -självstudien för att se vad mer du kan göra med avancerad läsar-SDK med hjälp av python
* Se snabb [](./tutorial-ios-picture-immersive-reader.md) självstudien för att se vad mer du kan göra med avancerad läsar-SDK med Swift
* Utforska SDK: [n för avancerad läsare](https://github.com/microsoft/immersive-reader-sdk) och [Avancerad läsare SDK-referens](./reference.md)