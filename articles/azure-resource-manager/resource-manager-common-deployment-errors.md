---
title: Felsök vanliga fel i Azure-distribution | Microsoft Docs
description: Beskriver hur du löser vanliga fel när du distribuerar resurser till Azure med Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: distributionsfel för azure-distribution distribuera till azure
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/08/2018
ms.author: tomfitz
ms.openlocfilehash: 3ecc1a9557c7854a0771decb3cc7f7597bcd87dd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Felsöka vanliga Azure-distribution med Azure Resource Manager

Den här artikeln beskriver vissa vanliga Azure distributionsfel du kan stöta på och ger information om du vill åtgärda felen. Om du inte hittar felkoden för din distribution felet, se [hittar felkod](#find-error-code).

## <a name="error-codes"></a>Felkoder

| Felkod | Åtgärd | Mer information |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Följ namngivningsbegränsningar för lagringskonton. | [Lös lagringskontonamnet](resource-manager-storage-account-name-errors.md) |
| AccountPropertyCannotBeSet | Kontrollera tillgängligt lagringsutrymme kontoegenskaperna. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | Det kluster eller den region har inte resurser som är tillgänglig eller har inte stöd för den begärda VM-storleken. Försöka senare eller be en annan VM-storlek. | [Problem med etablering och fördelningen för Linux](../virtual-machines/linux/troubleshoot-deployment-new-vm.md), [problem med etablering och fördelningen för Windows](../virtual-machines/windows/troubleshoot-deployment-new-vm.md) och [felsöka Tilldelningsfel](../virtual-machines/windows/allocation-failure.md)|
| AnotherOperationInProgress | Vänta tills den samtidiga åtgärden har slutförts. | |
| AuthorizationFailed | Ditt konto eller tjänstens huvudnamn har inte behörighet att slutföra distributionen. Kontrollera ditt konto hör till rollen och dess behörighet för omfånget för distributionen. | [Rollbaserad åtkomstkontroll i Azure](../role-based-access-control/role-assignments-portal.md) |
| BadRequest | Du har skickat distribution värden som inte matchar vad som förväntas av Resource Manager. Kontrollera det inre statusmeddelanden för hjälp med felsökning. | [Mallreferensen](/azure/templates/) och [platser som stöds](resource-manager-templates-resources.md#location) |
| Konflikt | Du begär en åtgärd som inte tillåts i resursens aktuella tillstånd. Till exempel tillåts ändra storlek på diskar endast när du skapar en virtuell dator eller när den virtuella datorn har frigjorts. | |
| DeploymentActive | Vänta tills samtidiga distributionen till den här resursgruppen ska slutföras. | |
| DeploymentFailed | DeploymentFailed-felet är ett allmänt fel som inte innehåller de information du behöver för att lösa felet. Titta i felinformationen för en felkod som innehåller mer information. | [Hitta felkod](#find-error-code) |
| DeploymentQuotaExceeded | Om du når gränsen på 800 distributioner per resursgrupp kan du ta bort distributioner från tidigare som inte längre behövs. Du kan ta bort poster från historiken med [az distribution bort](/cli/azure/group/deployment#az_group_deployment_delete) för Azure CLI eller [ta bort AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/remove-azurermresourcegroupdeployment) i PowerShell. Ta bort en post från distributionshistoriken påverkar inte distribuera resurser. | |
| DnsRecordInUse | DNS-postnamn måste vara unikt. Ange ett annat namn eller ändra den befintliga posten. | |
| ImageNotFound | Kontrollera inställningarna för VM-avbildning. |  |
| InUseSubnetCannotBeDeleted | Det här felet kan uppstå när du försöker uppdatera en resurs, men begäran har bearbetats genom att ta bort och skapa resursen. Se till att ange alla värden som är oförändrade. | [Uppdatera resurs](/azure/architecture/building-blocks/extending-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Hämta åtkomsttoken för den lämpliga innehavaren. Du kan bara hämta token från den klient som kontot tillhör. | |
| InvalidContentLink | Du har troligen försökt att länka till en kapslad mall som inte är tillgänglig. Kontrollera den URI som du angav för den kapslade mallen. Om mallen finns i ett lagringskonto, kontrollera att URI: N är tillgänglig. Du kan behöva passera en SAS-token. | [länkade mallar](resource-group-linked-templates.md) |
| InvalidParameter | Ett av de värden som du angav för en resurs matchar inte det förväntade värdet. Det här felet kan bero på många olika villkor. Till exempel ett lösenord kan vara otillräcklig eller en blobbnamnet kan vara felaktigt. Läs felmeddelandet att avgöra vilket värde som behöver åtgärdas. | |
| InvalidRequestContent | Värden krävs för din distribution värden som innehåller värden som inte förväntas eller saknas. Kontrollera värdena för din resurstypen. | [Mallreferensen](/azure/templates/) |
| InvalidRequestFormat | Aktivera felsökningsloggning vid körning av distributionen och kontrollera innehållet i begäran. | [Felsökningsloggning](#enable-debug-logging) |
| InvalidResourceNamespace | Kontrollera resursnamnrymden som du angav i den **typen** egenskapen. | [Mallreferensen](/azure/templates/) |
| InvalidResourceReference | Resursen finns ännu inte eller är felaktigt refererar till. Kontrollera om du behöver lägga till ett beroende. Kontrollera att din användning av den **referens** funktion innehåller de obligatoriska parametrarna för ditt scenario. | [Lös beroenden](resource-manager-not-found-errors.md) |
| InvalidResourceType | Kontrollera resursen skriver du angav i den **typen** egenskapen. | [Mallreferensen](/azure/templates/) |
| InvalidSubscriptionRegistrationState | Registrera prenumerationen med resursprovidern. | [Lös registrering](resource-manager-register-provider-errors.md) |
| InvalidTemplate | Kontrollera din mallens syntax för fel. | [Lös ogiltig mall](resource-manager-invalid-template-errors.md) |
| InvalidTemplateCircularDependency | Ta bort onödiga beroenden. | [Lös Cirkelberoenden](resource-manager-invalid-template-errors.md#circular-dependency) |
| LinkedAuthorizationFailed | Kontrollera om ditt konto tillhör samma klient som du distribuerar till resursgruppen. | |
| LinkedInvalidPropertyId | Resurs-ID för en resurs matchar inte korrekt. Kontrollera att du anger alla nödvändiga värden för resurs-ID, inklusive prenumerations-ID, resursgruppens namn, resurstyp, överordnade resurs (om det behövs) och resursnamnet. | |
| LocationRequired | Ange en plats för din resurs. | [Ange en plats](resource-manager-templates-resources.md#location) |
| MismatchingResourceSegments | Se till att kapslade resursen har rätt antal segment i namn och typ. | [Matcha resursen segment](resource-manager-invalid-template-errors.md#incorrect-segment-lengths)
| MissingRegistrationForLocation | Kontrollera status för resource provider för registrering och platser som stöds. | [Lös registrering](resource-manager-register-provider-errors.md) |
| MissingSubscriptionRegistration | Registrera prenumerationen med resursprovidern. | [Lös registrering](resource-manager-register-provider-errors.md) |
| NoRegisteredProviderFound | Kontrollera registreringsstatus i resource provider. | [Lös registrering](resource-manager-register-provider-errors.md) |
| NotFound | Du kanske försöker distribuera en beroende resurs parallellt med en överordnad resurs. Kontrollera om du behöver lägga till ett beroende. | [Lös beroenden](resource-manager-not-found-errors.md) |
| OperationNotAllowed | Distributionen är försöker utföra en åtgärd som överskrider kvoten för prenumerationen, resursgruppen eller region. Ändra distributionen för att hålla sig inom kvoterna om möjligt. I annat fall bör du begära en ändring av din kvoter. | [Lös kvoter](resource-manager-quota-errors.md) |
| ParentResourceNotFound | Kontrollera att det finns en överordnad-resurs innan du skapar underordnat resurser. | [Lösa överordnade resursen](resource-manager-parent-resource-errors.md) |
| PrivateIPAddressInReservedRange | Den angivna IP-adressen innehåller ett adressintervall som krävs av Azure. Ändra IP-adress för att undvika reserverade intervallet. | [IP-adresser](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PrivateIPAddressNotInSubnet | Den angivna IP-adressen ligger utanför intervallet för undernätet. Ändra IP-adressen ligger inom intervallet för undernätet. | [IP-adresser](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PropertyChangeNotAllowed | Vissa egenskaper kan inte ändras för en distribuerad resurs. När du uppdaterar en resurs, begränsa ändringarna till tillåtna egenskaper. | [Uppdatera resurs](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | Prenumerationen innehåller en resursprincip som förhindrar att en åtgärd som du försöker utföra under distributionen. Hitta den princip som blockerar åtgärden. Om möjligt ändra distributionen för att uppfylla begränsningarna från principen. | [Lös principer](resource-manager-policy-requestdisallowedbypolicy-error.md) |
| ReservedResourceName | Ange en resurs som inte innehåller ett reserverat namn. | [Reserverade resursnamn](resource-manager-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | Vänta tills borttagningen ska slutföras. | |
| ResourceGroupNotFound | Kontrollera namnet på målresursgruppen för distributionen. Det måste finnas i din prenumeration. Kontrollera din prenumerationskontext. | [Azure CLI](/cli/azure/account?#az_account_set) [PowerShell](/powershell/module/azurerm.profile/set-azurermcontext) |
| ResourceNotFound | Distributionen refererar till en resurs som inte kan matchas. Kontrollera att din användning av den **referens** funktion innehåller de parametrar som krävs för ditt scenario. | [Lösa referenser](resource-manager-not-found-errors.md) |
| ResourceQuotaExceeded | Distributionen försöker att skapa resurser som överskrider kvoten för prenumerationen, resursgruppen eller region. Om möjligt ändra din infrastruktur för att hålla sig inom kvoter. I annat fall bör du begära en ändring av din kvoter. | [Lös kvoter](resource-manager-quota-errors.md) |
| SkuNotAvailable | Välj SKU (till exempel VM-storlek) som är tillgänglig för den plats som du har valt. | [Lös SKU](resource-manager-sku-not-available-errors.md) |
| StorageAccountAlreadyExists | Ange ett unikt namn för lagringskontot. | [Lös lagringskontonamnet](resource-manager-storage-account-name-errors.md)  |
| StorageAccountAlreadyTaken | Ange ett unikt namn för lagringskontot. | [Lös lagringskontonamnet](resource-manager-storage-account-name-errors.md) |
| StorageAccountNotFound | Kontrollera prenumerationen, resursgruppen och namnet på det lagringskonto som du försöker använda. | |
| SubnetsNotInSameVnet | En virtuell dator kan bara ha ett virtuellt nätverk. När du distribuerar flera nätverkskort kan du se till att de hör till samma virtuella nätverk. | [Flera nätverkskort](../virtual-machines/windows/multiple-nics.md) |
| TemplateResourceCircularDependency | Ta bort onödiga beroenden. | [Lös Cirkelberoenden](resource-manager-invalid-template-errors.md#circular-dependency) |
| TooManyTargetResourceGroups | Minska antalet resursgrupper för en enda distribution. | [Distribution mellan resursgrupper](resource-manager-cross-resource-group-deployment.md) |

## <a name="find-error-code"></a>Hitta felkod

Det finns två typer av fel som du kan ta emot:

* Verifieringsfel
* Distributionsfel

Verifieringsfel uppstår scenarier som kan fastställas innan distribution. De omfattar syntaxfel i mallen, eller försök att distribuera resurser som skulle överskrida kvoter för din prenumeration. Distributionsfel uppkommer villkor som kan inträffa under distributionsprocessen. De omfattar försöker komma åt en resurs som distribueras parallellt.

Båda typerna av fel returnerar en felkod som används för att felsöka distributionen. Båda typerna av fel visas i den [aktivitetsloggen](resource-group-audit.md). Dock visas inte valideringsfel i distributionshistoriken eftersom distributionen har startat.

### <a name="validation-errors"></a>Verifieringsfel

När du distribuerar via portalen finns ett verifieringsfel efter att ha skickat värdena.

![Visa portal verifieringsfel](./media/resource-manager-common-deployment-errors/validation-error.png)

Välj meddelandet för mer information. I följande bild visas en **InvalidTemplateDeployment** fel och ett meddelande som anger en princip blockeras distributionen.

![Visa valideringsinformation](./media/resource-manager-common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>Distributionsfel

När åtgärden har klarat valideringen men misslyckas under distributionen, visas fel i meddelanden. Markera meddelandet.

![meddelandet-fel](./media/resource-manager-common-deployment-errors/notification.png)

Du kan visa mer information om hur du distribuerar. Välj alternativet för att få mer information om felet.

![distributionen misslyckades](./media/resource-manager-common-deployment-errors/deployment-failed.png)

Du ser felmeddelandet och felkoder. Observera att det finns två felkoder. Första felkoden (**DeploymentFailed**) är ett allmänt fel som inte ger de information du behöver för att lösa felet. Andra felkoden (**StorageAccountNotFound**) innehåller de information du behöver. 

![felinformation](./media/resource-manager-common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>Aktivera felsökningsloggning

Ibland behöver mer information om begäran och svar för att lära dig vad som gick fel. Du kan begära att ytterligare information loggas under en distribution med hjälp av PowerShell eller Azure CLI.

- PowerShell

   I PowerShell, ange den **DeploymentDebugLogLevel** parameter till alla, ResponseContent eller RequestContent.

  ```powershell
  New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile c:\Azure\Templates\storage.json -DeploymentDebugLogLevel All
  ```

   Kontrollera begäran innehåll med följande cmdlet:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.request | ConvertTo-Json
  ```

   Eller innehåll med svaret:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.response | ConvertTo-Json
  ```

   Den här informationen kan hjälpa dig att avgöra om ett värde i mallen felaktigt anges.

- Azure CLI

   Undersök distributionsåtgärder med följande kommando:

  ```azurecli
  az group deployment operation list --resource-group ExampleGroup --name vmlinux
  ```

- Kapslad mall

   Logga felsökningsinformation för en kapslad mall med det **debugSetting** element.

  ```json
  {
      "apiVersion": "2016-09-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri": "{template-uri}",
              "contentVersion": "1.0.0.0"
          },
          "debugSetting": {
             "detailLevel": "requestContent, responseContent"
          }
      }
  }
  ```

## <a name="create-a-troubleshooting-template"></a>Skapa en mall för felsökning

I vissa fall är det enklaste sättet att felsöka mallen för att testa delar av den. Du kan skapa en förenklad mall som gör det möjligt att fokusera på den del som du tror orsakar felet. Anta att du får ett fel när du refererar till en resurs. Skapa en mall som returnerar den del som orsakar problemet i stället för hantering av en hel mall. Det kan hjälpa dig att avgöra om du skickar in rätt parametrar med hjälp av Mallfunktioner korrekt, och hämtar resursen du förväntar dig.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
        "type": "string"
    },
    "storageResourceGroup": {
        "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "exampleOutput": {
        "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
        "type" : "object"
    }
  }
}
```

Eller anta att det uppstår distributionsfel som du tror är relaterade till felaktigt angiven beroenden. Testa din mall genom att dela upp den i förenklad mallar. Först skapa en mall som distribuerar en enskild resurs (till exempel en SQL Server). Lägga till en resurs som är beroende av den (till exempel en SQL-databas) när du är säker på att du har den här resursen korrekt definierad. Lägg till andra beroende resurser (till exempel granskningsprinciper) när du har de två resurser korrekt definierad. Ta bort resursgruppen för att se till att du testar rätt beroenden mellan varje test-distribution.


## <a name="next-steps"></a>Nästa steg
* Läs om granskning åtgärder i [granskningsåtgärder med Resource Manager](resource-group-audit.md).
* Mer information om åtgärder för att avgöra felen under distributionen, se [visa distributionsåtgärder](resource-manager-deployment-operations.md).
