---
title: Felsök vanliga fel i Azure-distribution | Microsoft Docs
description: Beskriver hur du löser vanliga fel när du distribuerar resurser till Azure med Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: distributionsfel, azure-distribution, distribuera till azure
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/16/2018
ms.author: tomfitz
ms.openlocfilehash: 64af946c3f8f58808f1bca06f1ef21d92da8cda8
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55223569"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Felsöka vanliga Azure-distributionsfel med Azure Resource Manager

Den här artikeln beskriver några vanliga Azure-distribution-fel och innehåller information om du vill åtgärda felen. Om du hittar felkoden för din distributionsfel, [hittar felkod](#find-error-code).

## <a name="error-codes"></a>Felkoder

| Felkod | Åtgärd | Mer information |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Följ namngivningsbegränsningar för storage-konton. | [Lösa lagringskontonamn](resource-manager-storage-account-name-errors.md) |
| AccountPropertyCannotBeSet | Kontrollera tillgängligt lagringsutrymme kontoegenskaperna. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | Det kluster eller en region har resurser som är tillgängliga eller har inte stöd för den begärda VM-storleken. Gör om begäran vid ett senare tillfälle eller be en annan VM-storlek. | [Problem med etablering och allokering för Linux](../virtual-machines/linux/troubleshoot-deployment-new-vm.md), [problem med etablering och allokering för Windows](../virtual-machines/windows/troubleshoot-deployment-new-vm.md) och [Felsök Allokeringsfel](../virtual-machines/troubleshooting/allocation-failure.md)|
| AnotherOperationInProgress | Vänta tills samtidiga åtgärden har slutförts. | |
| AuthorizationFailed | Ditt konto eller tjänstens huvudnamn har inte behörighet att slutföra distributionen. Kontrollera ditt konto tillhör rollen och dess åtkomst för distributionsomfattningen. | [Rollbaserad åtkomstkontroll i Azure](../role-based-access-control/role-assignments-portal.md) |
| BadRequest | Du har skickat distribution värden som inte matchar vad som förväntas av Resource Manager. Kontrollera det inre statusmeddelanden för att få hjälp med felsökning. | [Mallreferensen](/azure/templates/) och [platser som stöds](resource-manager-templates-resources.md#location) |
| Konflikt | Du ska få en åtgärd som inte är tillåtet i resursens aktuella tillstånd. Till exempel tillåts ändra storlek på disken endast när du skapar en virtuell dator eller när Virtuellt datorn frigörs. | |
| DeploymentActive | Vänta tills samtidig distribuering till den här resursgruppen ska slutföras. | |
| DeploymentFailed | DeploymentFailed-felet är ett allmänt fel som inte ger de information du behöver för att lösa felet. Titta i felinformationen för en felkod som innehåller mer information. | [Hitta felkoden](#find-error-code) |
| DeploymentQuotaExceeded | Om du når gränsen på 800 distributioner per resursgrupp kan du ta bort distributioner från historiken som inte längre behövs. Du kan ta bort poster från historiken över med [az group deployment ta bort](/cli/azure/group/deployment#az-group-deployment-delete) för Azure CLI eller [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) i PowerShell. Ta bort en post från distributionshistoriken påverkar inte distribuera resurser. | |
| DnsRecordInUse | DNS-postnamnet måste vara unikt. Ange ett annat namn eller ändra den befintliga posten. | |
| ImageNotFound | Kontrollera inställningarna för VM-avbildning. |  |
| InUseSubnetCannotBeDeleted | Du kan få det här felet när du försöker uppdatera en resurs, men begäran har bearbetats genom att ta bort och skapa resursen. Se till att ange värden för alla oförändrade. | [Uppdatera resurs](/azure/architecture/building-blocks/extending-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Hämta åtkomsttoken för lämpliga klienten. Du kan bara hämta token från den klient som ditt konto tillhör. | |
| InvalidContentLink | Du har troligen försökt att länka till en kapslad mall som inte är tillgänglig. Kontrollera URI som du angav för den kapslade mallen. Om mallen finns i ett storage-konto, kontrollera att URI: N är tillgänglig. Du kan behöva skicka en SAS-token. | [Länkade mallar](resource-group-linked-templates.md) |
| InvalidParameter | En av de värden som du angav för en resurs matchar inte det förväntade värdet. Det här felet kan bero på många olika villkor. Till exempel ett lösenord kan vara otillräckligt eller en blobnamnet kan vara felaktigt. Kontrollera felmeddelandet för att avgöra vilket värde måste åtgärdas. | |
| InvalidRequestContent | Dina värden för distribution som innehåller värden som inte är förväntade eller saknas nödvändiga värden. Kontrollera värdena för din resurstyp av. | [Mallreferens](/azure/templates/) |
| InvalidRequestFormat | Aktivera felsökningsloggning när du genomför distributionen och kontrollera innehållet i begäran. | [Felsökningsloggning](#enable-debug-logging) |
| InvalidResourceNamespace | Kontrollera resursnamnområdet som du angav i den **typ** egenskapen. | [Mallreferens](/azure/templates/) |
| InvalidResourceReference | Resursen finns inte ännu eller felaktigt refererar till. Kontrollera om du behöver lägga till ett beroende. Kontrollera att din användning av den **referens** funktion innehåller de obligatoriska parametrarna för ditt scenario. | [Lös beroenden](resource-manager-not-found-errors.md) |
| InvalidResourceType | Kontrollera resursen skriver du angav i den **typ** egenskapen. | [Mallreferens](/azure/templates/) |
| InvalidSubscriptionRegistrationState | Registrera din prenumeration med resursprovidern. | [Lösa registrering](resource-manager-register-provider-errors.md) |
| InvalidTemplate | Kontrollera din mallens syntax för fel. | [Lösa ogiltig mall](resource-manager-invalid-template-errors.md) |
| InvalidTemplateCircularDependency | Ta bort onödiga beroenden. | [Lösa cirkulärt tjänstberoende](resource-manager-invalid-template-errors.md#circular-dependency) |
| LinkedAuthorizationFailed | Kontrollera om ditt konto tillhör samma klientorganisation som du distribuerar till resursgruppen. | |
| LinkedInvalidPropertyId | Resurs-ID för en resurs är inte korrekt lösning. Kontrollera att du ger alla nödvändiga värden för resurs-ID, inklusive prenumerations-ID, resursgruppens namn, resurstyp, överordnade resursnamnet (vid behov) och resursnamn. | |
| LocationRequired | Ange en plats för din resurs. | [Ange en plats](resource-manager-templates-resources.md#location) |
| MismatchingResourceSegments | Se till att kapslad resurs har rätt antal segment i namn och typ. | [Lösa resource segment](resource-manager-invalid-template-errors.md#incorrect-segment-lengths)
| MissingRegistrationForLocation | Kontrollera status för resursleverantör registrering och platser som stöds. | [Lösa registrering](resource-manager-register-provider-errors.md) |
| MissingSubscriptionRegistration | Registrera din prenumeration med resursprovidern. | [Lösa registrering](resource-manager-register-provider-errors.md) |
| NoRegisteredProviderFound | Kontrollera status för resursleverantör registrering. | [Lösa registrering](resource-manager-register-provider-errors.md) |
| NotFound | Du kan försöka att distribuera en beroende resurs parallellt med en överordnad resurs. Kontrollera om du behöver lägga till ett beroende. | [Lös beroenden](resource-manager-not-found-errors.md) |
| OperationNotAllowed | Distributionen försöker en åtgärd som överskrider kvoten för prenumerationen, resursgruppen eller region. Om möjligt ändra distributionen för att hålla sig inom kvoter. I annat fall du begär en ändring av dina kvoter. | [Lösa kvoter](resource-manager-quota-errors.md) |
| ParentResourceNotFound | Kontrollera att det finns en överordnad resurs innan du skapar underordnat resurser. | [Lösa överordnade resurs](resource-manager-parent-resource-errors.md) |
| PasswordTooLong | Du kanske har markerat ett lösenord som innehåller för många tecken eller kanske har konverterat lösenordsvärdet till en säker sträng innan det skickas som en parameter. Om mallen innehåller en **säker sträng** parameter, du behöver inte att konvertera värdet till en säker sträng. Ange lösenordsvärdet som text. |  |
| PrivateIPAddressInReservedRange | Den angivna IP-adressen innehåller ett adressintervall som krävs av Azure. Ändra IP-adress för att undvika reserverade intervallet. | [IP-adresser](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PrivateIPAddressNotInSubnet | Den angivna IP-adressen ligger utanför undernätsintervallet. Ändra IP-adress som ligger inom intervall för undernät. | [IP-adresser](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PropertyChangeNotAllowed | Vissa egenskaper kan inte ändras på en distribuerad resurs. När du uppdaterar en resurs, begränsa dina ändringar till tillåtna egenskaper. | [Uppdatera resurs](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | Prenumerationen innehåller en resursprincip som förhindrar att en åtgärd som du försöker utföra under distributionen. Hitta den princip som blockerar åtgärden. Om möjligt ändra distributionen för att uppfylla begränsningarna från principen. | [Lösa principer](resource-manager-policy-requestdisallowedbypolicy-error.md) |
| ReservedResourceName | Ange ett resursnamn som inte innehåller ett reserverat namn. | [Reserverade resursnamn](resource-manager-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | Vänta tills borttagningen att slutföra. | |
| ResourceGroupNotFound | Kontrollera namnet på målresursgruppen för distributionen. Det måste redan finnas i din prenumeration. Kontrollera din prenumerationskontexten. | [Azure CLI](/cli/azure/account?#az-account-set) [PowerShell](/powershell/module/Az.Accounts/Set-AzContext) |
| ResourceNotFound | Distributionen av refererar till en resurs som inte kan matchas. Kontrollera att din användning av den **referens** funktion innehåller de parametrar som krävs för ditt scenario. | [Åtgärda referenser](resource-manager-not-found-errors.md) |
| ResourceQuotaExceeded | Distributionen försöker skapa resurser som överstiger kvoten för prenumerationen, resursgruppen eller region. Om möjligt ändra din infrastruktur för hålla dig inom kvoter. I annat fall du begär en ändring av dina kvoter. | [Lösa kvoter](resource-manager-quota-errors.md) |
| SkuNotAvailable | Välj SKU (till exempel VM-storlek) som är tillgänglig för den plats som du har valt. | [Lösa SKU](resource-manager-sku-not-available-errors.md) |
| StorageAccountAlreadyExists | Ange ett unikt namn för lagringskontot. | [Lösa lagringskontonamn](resource-manager-storage-account-name-errors.md)  |
| StorageAccountAlreadyTaken | Ange ett unikt namn för lagringskontot. | [Lösa lagringskontonamn](resource-manager-storage-account-name-errors.md) |
| StorageAccountNotFound | Kontrollera prenumerationen, resursgruppen och namnet på det lagringskonto som du försöker använda. | |
| SubnetsNotInSameVnet | En virtuell dator kan bara ha ett virtuellt nätverk. När du distribuerar flera nätverkskort kan du kontrollera att de tillhör samma virtuella nätverk. | [Flera nätverkskort](../virtual-machines/windows/multiple-nics.md) |
| TemplateResourceCircularDependency | Ta bort onödiga beroenden. | [Lösa cirkulärt tjänstberoende](resource-manager-invalid-template-errors.md#circular-dependency) |
| TooManyTargetResourceGroups | Minska antalet resursgrupper för en enkel distribution. | [Distribution mellan resursgrupper](resource-manager-cross-resource-group-deployment.md) |

## <a name="find-error-code"></a>Hitta felkoden

Det finns två typer av fel som du kan ta emot:

* verifieringsfel
* distributionsfel

Verifieringsfel uppstår från scenarier som kan fastställas före distributionen. De innehåller syntaxfel i mallen eller försöker distribuera resurser som skulle överskrida prenumerationskvoterna. Distributionsfel uppkommer villkor som uppstår under distributionsprocessen. De omfattar försök att få åtkomst till en resurs som distribueras parallellt.

Båda typerna av fel returnerar en felkod som du använder för att felsöka distributionen. Båda typerna av fel visas i den [aktivitetsloggen](resource-group-audit.md). Dock visas valideringsfel inte i distributionshistoriken eftersom distributionen aldrig startades.

### <a name="validation-errors"></a>Verifieringsfel

När du distribuerar via portalen kan se du ett valideringsfel efter att ha skickat dina värden.

![Visa portal verifieringsfel](./media/resource-manager-common-deployment-errors/validation-error.png)

Välj meddelandet för mer information. I följande bild, ser du en **InvalidTemplateDeployment** fel och ett meddelande som anger en princip blockeras distributionen.

![Visa verifieringsinformation](./media/resource-manager-common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>distributionsfel

När åtgärden godkänns vid, men inte fungerar under distributionen, kan du få ett distributionsfel.

Om du vill se distribution felkoder och meddelanden med PowerShell, använder du:

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName exampledeployment -ResourceGroupName examplegroup).Properties.statusMessage
```

Om du vill se distribution felkoder och meddelanden med Azure CLI, använder du:

```azurecli-interactive
az group deployment operation list --name exampledeployment -g examplegroup --query "[*].properties.statusMessage"
```

Markera meddelandet i portalen.

![Aviseringsfel](./media/resource-manager-common-deployment-errors/notification.png)

Visas mer information om hur du distribuerar. Välj alternativet för att hitta mer information om felet.

![distributionen misslyckades](./media/resource-manager-common-deployment-errors/deployment-failed.png)

Du ser felmeddelandet och felkoder. Observera att det finns två felkoder. Första felkoden (**DeploymentFailed**) är ett allmänt fel som inte ger de information du behöver för att lösa felet. Andra felkoden (**StorageAccountNotFound**) tillhandahåller de information du behöver. 

![felinformation](./media/resource-manager-common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>Aktivera felsökningsloggning

Ibland behöver du mer information om begäranden och svar för att lära dig vad som gick fel. Under distributionen kan du begära att ytterligare information är inloggad under en distribution. 

### <a name="powershell"></a>PowerShell

I PowerShell, ange den **DeploymentDebugLogLevel** parametern till alla, obsah ResponseContent eller RequestContent.

```powershell
New-AzResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile c:\Azure\Templates\storage.json `
  -DeploymentDebugLogLevel All
```

Granska begäran innehåll med följande cmdlet:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.request `
| ConvertTo-Json
```

Eller innehåll med svaret:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.response `
| ConvertTo-Json
```

Den här informationen kan hjälpa dig att avgöra om ett värde i mallen som angivits felaktigt.

### <a name="azure-cli"></a>Azure CLI

För närvarande, Azure CLI har inte stöd för att aktivera felsökningsloggning, men du kan hämta felsökningsloggning.

Undersök distributionsåtgärder med följande kommando:

```azurecli
az group deployment operation list \
  --resource-group examplegroup \
  --name exampledeployment
```

Granska begäran innehåll med följande kommando:

```azurecli
az group deployment operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.request
```

Kontrollera svaret innehåll med följande kommando:

```azurecli
az group deployment operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.response
```

### <a name="nested-template"></a>Kapslad mall

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

I vissa fall är det enklaste sättet att felsöka mallen för att testa delar av den. Du kan skapa en förenklad mall som gör att du kan fokusera på den del som du tror orsakas felet. Anta exempelvis att du får ett fel när du refererar till en resurs. Skapa en mall som returnerar den del som orsakar problemet i stället för att hantera med en mall för hela. Det kan hjälpa dig avgöra om du låter i rätt parametrar med Mallfunktioner ska, och hämtar resursen du förväntar dig.

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

Eller så kan du anta att det uppstår distributionsfel som du tycker är relaterade till att ange ett felaktigt sätt beroenden. Testa din mall genom att bryta ned den i förenklad mallar. Skapa först en mall som distribuerar en enskild resurs (till exempel en SQL Server). Lägg till en resurs som är beroende av den (till exempel en SQL-databas) när du är säker på att du har den här resursen korrekt definierad. Lägg till andra beroende resurser (till exempel granskningsprinciper) när du har dessa två resurser korrekt definierad. Ta bort resursgruppen för att se till att du testar rätt beroenden mellan varje test-distribution.


## <a name="next-steps"></a>Nästa steg

* Om du vill gå igenom en självstudiekurs om felsökning finns i [självstudien: Felsöka malldistributioner för Resource Manager](./resource-manager-tutorial-troubleshoot.md)
* Läs om granskning åtgärder i [granskningsåtgärder med Resource Manager](resource-group-audit.md).
* Mer information om åtgärder för att avgöra felen under distributionen, se [visa distributionsåtgärder](resource-manager-deployment-operations.md).
