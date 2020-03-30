---
title: Felsök vanliga distributionsfel
description: Beskriver hur du löser vanliga fel när du distribuerar resurser till Azure med Azure Resource Manager.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 10/04/2019
ms.openlocfilehash: bc1568c53cdb5518f694d77a2f28f3cf77296ee2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460389"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Felsöka vanliga fel i Azure-distributioner med Azure Resource Manager

I den här artikeln beskrivs några vanliga Azure-distributionsfel och innehåller information för att lösa felen. Om du inte hittar felkoden för distributionsfel läser du [Hitta felkod](#find-error-code).

Kontakta oss om du letar efter information om en felkod och den informationen inte finns i den här artikeln. Längst ner på den här sidan kan du lämna feedback. Feedbacken spåras med GitHub-problem.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="error-codes"></a>Felkoder

| Felkod | Åtgärd | Mer information |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Följ namngivningsbegränsningar för lagringskonton. | [Lös namn på lagringskonto](error-storage-account-name.md) |
| KontoPropertyCannotBeSet | Kontrollera tillgängliga lagringskontoegenskaper. | [lagringKonton](/azure/templates/microsoft.storage/storageaccounts) |
| AllokeringMissen | Klustret eller regionen har inte tillgängliga resurser eller kan inte stödja den begärda vm-storleken. Försök igen begäran vid ett senare tillfälle eller begär en annan vm-storlek. | [Etablerings- och allokeringsproblem för Linux-](../../virtual-machines/linux/troubleshoot-deployment-new-vm.md) [och allokerings- och allokeringsproblem för Windows](../../virtual-machines/windows/troubleshoot-deployment-new-vm.md) och [felsöka allokeringsfel](../../virtual-machines/troubleshooting/allocation-failure.md)|
| AnotherOperationInProgress | Vänta tills samtidiga åtgärder har slutförts. | |
| TillståndMissen | Ditt konto eller tjänsthuvudnamn har inte tillräcklig åtkomst för att slutföra distributionen. Kontrollera vilken roll ditt konto tillhör och dess åtkomst för distributionsomfånget.<br><br>Det här felet kan visas när en nödvändig resursleverantör inte är registrerad. | [Rollbaserad åtkomstkontroll i Azure](../../role-based-access-control/role-assignments-portal.md)<br><br>[Lös registrering](error-register-resource-provider.md) |
| BadRequest | Du har skickat distributionsvärden som inte matchar vad som förväntas av Resource Manager. Kontrollera det inre statusmeddelandet om du vill ha hjälp med felsökning. | [Mallreferens](/azure/templates/) och [platser som stöds](resource-location.md) |
| Konflikt | Du begär en åtgärd som inte är tillåten i resursens aktuella tillstånd. Till exempel tillåts diskstorlek endast när du skapar en virtuell dator eller när den virtuella datorn är frigörd. | |
| DeploymentActiveAndUneditable | Vänta tills samtidig distribution till den här resursgruppen har slutförts. | |
| DistributionFailedCleanUp | När du distribuerar i fullständigt läge tas alla resurser som inte finns i mallen bort. Du får det här felet när du inte har tillräcklig behörighet för att ta bort alla resurser som inte finns i mallen. Undvik felet genom att ändra distributionsläget till inkrementellt. | [Distributionslägen för Azure Resource Manager](deployment-modes.md) |
| DeploymentNameInvalidCharacters | Distributionsnamnet får bara innehålla bokstavs-, siffra,'-', '.' eller '_'. | |
| DeploymentNameLengthLimitExceed | Distributionsnamnen är begränsade till 64 tecken.  | |
| DistributionEn är inte | Felet DeploymentFailed är ett allmänt fel som inte innehåller den information du behöver för att lösa felet. Leta i felinformationen efter en felkod som innehåller mer information. | [Hitta felkod](#find-error-code) |
| DeploymentQuotaExceeded | Om du når gränsen på 800 distributioner per resursgrupp tar du bort distributioner från historiken som inte längre behövs. | [Lös fel när antalet distributioner överstiger 800](deployment-quota-exceeded.md) |
| DnsRecordInUse | DNS-postnamnet måste vara unikt. Ange ett annat namn. | |
| ImageNotFound | Kontrollera vm-bildinställningar. |  |
| InUseSubnetCannotBeDeleted | Du kan få det här felet när du försöker uppdatera en resurs och begäran bearbetas genom att ta bort och skapa resursen. Se till att ange alla oförändrade värden. | [Uppdatera resurs](/azure/architecture/building-blocks/extending-templates/update-resource) |
| OgiltigautentiseringTokenTenant | Hämta åtkomsttoken för lämplig klient. Du kan bara hämta token från klienten som ditt konto tillhör. | |
| Ogiltiginnehållslänk | Du har troligen försökt länka till en kapslad mall som inte är tillgänglig. Dubbelkolla uri:n som du angav för den kapslade mallen. Om mallen finns i ett lagringskonto kontrollerar du att URI:n är tillgänglig. Du kan behöva skicka en SAS-token. För närvarande kan du inte länka till en mall som finns i ett lagringskonto bakom en [Azure Storage-brandvägg](../../storage/common/storage-network-security.md). Överväg att flytta mallen till en annan databas, till exempel GitHub. | [Länkade mallar](linked-templates.md) |
| OgiltigdeplogenteringPlats | När du distribuerar på prenumerationsnivå har du angett en annan plats för ett tidigare använt distributionsnamn. | [Distributioner på prenumerationsnivå](deploy-to-subscription.md) |
| Ogiltigparameter | Ett av de värden som du angav för en resurs matchar inte det förväntade värdet. Det här felet kan bero på många olika villkor. Ett lösenord kan till exempel vara otillräckligt eller så kan ett blob-namn vara felaktigt. Felmeddelandet bör ange vilket värde som måste korrigeras. | |
| OgiltigtRequestContent | Distributionsvärdena innehåller antingen värden som inte känns igen eller så saknas obligatoriska värden. Bekräfta värdena för resurstypen. | [Mallreferens](/azure/templates/) |
| OgiltigtQuestFormat | Aktivera felsökningsloggning när du kör distributionen och verifiera innehållet i begäran. | [Felsökningsloggning](#enable-debug-logging) |
| OgiltigtResursnamnområde | Kontrollera det resursnamnområde som du angav i **egenskapen type.** | [Mallreferens](/azure/templates/) |
| OgiltigresourceReference | Resursen finns inte ännu eller refereras felaktigt. Kontrollera om du behöver lägga till ett samband. Kontrollera att din användning av **referensfunktionen** innehåller de parametrar som krävs för ditt scenario. | [Lösa beroenden](error-not-found.md) |
| InvalidResourceType | Kontrollera den resurstyp som du har angett i **egenskapen type.** | [Mallreferens](/azure/templates/) |
| OgiltigbeteckningRegistrationState | Registrera din prenumeration hos resursleverantören. | [Lös registrering](error-register-resource-provider.md) |
| InvalidTemplate | Kontrollera om det finns fel i mallsyntaxen. | [Lösa ogiltig mall](error-invalid-template.md) |
| OgiltigTemplateCircularBeroendeberoende | Ta bort onödiga beroenden. | [Lösa cirkulära beroenden](error-invalid-template.md#circular-dependency) |
| LinkedAuthorizationFailed | Kontrollera om ditt konto tillhör samma klient som resursgruppen som du distribuerar till. | |
| LinkedInvalidPropertyId | Resurs-ID:t för en resurs lösers inte korrekt. Kontrollera att du anger alla obligatoriska värden för resurs-ID, inklusive prenumerations-ID, resursgruppnamn, resurstyp, överordnat resursnamn (om det behövs) och resursnamn. | |
| PlatsKäckt | Ange en plats för resursen. | [Ange en plats](resource-location.md) |
| FelmatchningResourceSegments | Kontrollera att den kapslade resursen har rätt antal segment i namn och typ. | [Lösa resurssegment](error-invalid-template.md#incorrect-segment-lengths)
| SaknasRegistrationForLocation | Kontrollera resursproviderns registreringsstatus och platser som stöds. | [Lös registrering](error-register-resource-provider.md) |
| MissingSubscriptionRegistration | Registrera din prenumeration hos resursleverantören. | [Lös registrering](error-register-resource-provider.md) |
| NoRegisteredProviderFound | Kontrollera registreringsstatus för resursprovidern. | [Lös registrering](error-register-resource-provider.md) |
| NotFound | Du kanske försöker distribuera en beroende resurs parallellt med en överordnad resurs. Kontrollera om du behöver lägga till ett samband. | [Lösa beroenden](error-not-found.md) |
| Åtgärden InteTilllämd | Distributionen försöker en åtgärd som överskrider kvoten för prenumerationen, resursgruppen eller regionen. Om möjligt bör du ändra distributionen så att den håller sig inom kvoterna. Annars kan du överväga att begära en ändring av dina kvoter. | [Lös kvoter](error-resource-quota.md) |
| ParentResourceNotFound | Kontrollera att det finns en överordnad resurs innan du skapar de underordnade resurserna. | [Lös överordnad resurs](error-parent-resource.md) |
| PasswordTooLong (LösenordTooLong) | Du kan ha valt ett lösenord med för många tecken eller konverterat lösenordsvärdet till en säker sträng innan du skickar det som en parameter. Om mallen innehåller en **säker strängparameter** behöver du inte konvertera värdet till en säker sträng. Ange lösenordsvärdet som text. |  |
| PrivatIPAddressInReservedRange | Den angivna IP-adressen innehåller ett adressintervall som krävs av Azure. Ändra IP-adress för att undvika reserverat intervall. | [IP-adresser](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PrivatIPAddressIntinSubnet | Den angivna IP-adressen ligger utanför undernätsområdet. Ändra IP-adressen så att den faller inom undernätsområdet. | [IP-adresser](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| EgenskapChangeNotAllowed | Vissa egenskaper kan inte ändras på en distribuerad resurs. När du uppdaterar en resurs begränsar du ändringarna till tillåtna egenskaper. | [Uppdatera resurs](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | Din prenumeration innehåller en resursprincip som förhindrar en åtgärd som du försöker utföra under distributionen. Hitta den princip som blockerar åtgärden. Om möjligt ändrar du distributionen så att den uppfyller begränsningarna från principen. | [Lös principer](error-policy-requestdisallowedbypolicy.md) |
| ReservedResourceName | Ange ett resursnamn som inte innehåller ett reserverat namn. | [Namn på reserverade resurser](error-reserved-resource-name.md) |
| ResursgruppBeingDela | Vänta tills borttagningen är klar. | |
| ResursgruppInteFound | Kontrollera namnet på målresursgruppen för distributionen. Målgruppen måste redan finnas i prenumerationen. Kontrollera din prenumerationskontext. | [Azure CLI](/cli/azure/account?#az-account-set) [PowerShell](/powershell/module/Az.Accounts/Set-AzContext) |
| ResursIntenserGrunda | Distributionen refererar till en resurs som inte kan matchas. Kontrollera att din användning av **referensfunktionen** innehåller de parametrar som krävs för ditt scenario. | [Lös referenser](error-not-found.md) |
| ResourceQuotaExceeded | Distributionen försöker skapa resurser som överskrider kvoten för prenumerationen, resursgruppen eller regionen. Om möjligt bör du ändra infrastrukturen så att den håller sig inom kvoterna. Annars kan du överväga att begära en ändring av dina kvoter. | [Lös kvoter](error-resource-quota.md) |
| SkuNotAvailable | Välj SKU (till exempel VM-storlek) som är tillgänglig för den plats du har valt. | [Lös SKU](error-sku-not-available.md) |
| LagringFörstömnadExisterarExister | Ange ett unikt namn för lagringskontot. | [Lös namn på lagringskonto](error-storage-account-name.md)  |
| LagringKontoRereadyTaken | Ange ett unikt namn för lagringskontot. | [Lös namn på lagringskonto](error-storage-account-name.md) |
| LagringSkontoT Är integrundat | Kontrollera prenumerationen, resursgruppen och namnet på det lagringskonto som du försöker använda. | |
| UndernätEnIntinSameVnet | En virtuell dator kan bara ha ett virtuellt nätverk. När du distribuerar flera nätverkskort kontrollerar du att de tillhör samma virtuella nätverk. | [Flera nätverkskort](../../virtual-machines/windows/multiple-nics.md) |
| PrenumerationRegistrerad | När du distribuerar nätverksresurser registreras Microsoft.Network-resursprovidern automatiskt i prenumerationen. Ibland slutförs inte den automatiska registreringen i tid. Undvik det här intermittenta felet genom att registrera Microsoft.Network-resursprovidern före distributionen. | [Lös registrering](error-register-resource-provider.md) |
| TemplateResourceCircularBeroendeberoende | Ta bort onödiga beroenden. | [Lösa cirkulära beroenden](error-invalid-template.md#circular-dependency) |
| TooManyTargetResourceGroups | Minska antalet resursgrupper för en enskild distribution. | [Distribution mellan resursgrupper](cross-resource-group-deployment.md) |

## <a name="find-error-code"></a>Hitta felkod

Det finns två typer av fel som du kan få:

* valideringsfel
* distributionsfel

Valideringsfel uppstår från scenarier som kan fastställas före distributionen. De innehåller syntaxfel i mallen eller försöker distribuera resurser som skulle överskrida prenumerationskvoterna. Distributionsfel uppstår från förhållanden som inträffar under distributionsprocessen. De omfattar försök att få åtkomst till en resurs som distribueras parallellt.

Båda typerna av fel returnerar en felkod som du använder för att felsöka distributionen. Båda typerna av fel visas i [aktivitetsloggen](../management/view-activity-logs.md). Dock visas valideringsfel inte i distributionshistoriken eftersom distributionen aldrig startades.

### <a name="validation-errors"></a>Valideringsfel

När du distribuerar via portalen visas ett valideringsfel när du har skickat in dina värden.

![visa valideringsfel för portaler](./media/common-deployment-errors/validation-error.png)

Välj meddelandet för mer information. I följande bild visas ett **ogiltigtutbetalningsfel** och ett meddelande som anger en principblockeringsdistribution.

![visa valideringsinformation](./media/common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>Distributionsfel

När åtgärden godkänns men inte fungerar under distributionen visas ett distributionsfel.

Om du vill visa felkoder och meddelanden för distribution med PowerShell kan du använda:

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName exampledeployment -ResourceGroupName examplegroup).Properties.statusMessage
```

Om du vill visa felkoder och meddelanden för distribution med Azure CLI kan du använda:

```azurecli-interactive
az deployment group operation list --name exampledeployment -g examplegroup --query "[*].properties.statusMessage"
```

Markera meddelandet på portalen.

![meddelandefel](./media/common-deployment-errors/notification.png)

Du ser mer information om distributionen. Välj alternativet för att hitta mer information om felet.

![distributionen misslyckades](./media/common-deployment-errors/deployment-failed.png)

Du ser felmeddelandet och felkoderna. Observera att det finns två felkoder. Den första felkoden (**DeploymentFailed**) är ett allmänt fel som inte ger den information du behöver för att lösa felet. Den andra felkoden (**StorageAccountNotFound**) ger dig den information du behöver.

![felinformation](./media/common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>Aktivera felsökningsloggning

Ibland behöver du mer information om begäran och svar för att lära dig vad som gick fel. Under distributionen kan du begära att ytterligare information loggas under en distribution.

### <a name="powershell"></a>PowerShell

I PowerShell anger du parametern **DeploymentDebugLogLevel** till Alla, ResponseContent eller RequestContent.

```powershell
New-AzResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile c:\Azure\Templates\storage.json `
  -DeploymentDebugLogLevel All
```

Undersök innehållet i begäran med följande cmdlet:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.request `
| ConvertTo-Json
```

Eller svarsinnehållet med:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.response `
| ConvertTo-Json
```

Den här informationen kan hjälpa dig att avgöra om ett värde i mallen är felaktigt inställt.

### <a name="azure-cli"></a>Azure CLI

Azure CLI stöder för närvarande inte att aktivera felsökningsloggning, men du kan hämta felsökningsloggning.

Undersök distributionsåtgärderna med följande kommando:

```azurecli
az deployment group operation list \
  --resource-group examplegroup \
  --name exampledeployment
```

Undersök förfråmningsinnehållet med följande kommando:

```azurecli
az deployment group operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.request
```

Undersök svarsinnehållet med följande kommando:

```azurecli
az deployment group operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.response
```

### <a name="nested-template"></a>Kapslad mall

Om du vill logga felsökningsinformation för en kapslad mall använder du elementet **debugSetting.**

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2016-09-01",
  "name": "nestedTemplate",
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

## <a name="create-a-troubleshooting-template"></a>Skapa en felsökningsmall

I vissa fall är det enklaste sättet att felsöka mallen att testa delar av den. Du kan skapa en förenklad mall som gör att du kan fokusera på den del som du tror orsakar felet. Anta till exempel att du får ett fel när du refererar till en resurs. I stället för att hantera en hel mall skapar du en mall som returnerar den del som kan orsaka problemet. Det kan hjälpa dig att avgöra om du skickar in rätt parametrar, använder mallfunktioner korrekt och får den resurs du förväntar dig.

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

Anta också att du får distributionsfel som du tror är relaterade till felaktigt inställda beroenden. Testa mallen genom att dela upp den i förenklade mallar. Skapa först en mall som bara distribuerar en enda resurs (till exempel en SQL Server). När du är säker på att du har definierat resursen korrekt lägger du till en resurs som är beroende av den (till exempel en SQL-databas). När du har definierat dessa två resurser lägger du till andra beroende resurser (till exempel granskningsprinciper). Mellan varje testdistribution tar du bort resursgruppen för att se till att du testar beroendena på ett adekvat sätt.

## <a name="next-steps"></a>Nästa steg

* Information om hur du går igenom en felsökningsstudiekurs finns i [Självstudiekurs: Felsöka Resource Manager-malldistributioner](template-tutorial-troubleshoot.md)
* Mer information om granskningsåtgärder finns i [Granskningsåtgärder med Resurshanteraren](../management/view-activity-logs.md).
* Mer information om åtgärder för att fastställa fel under distributionen finns i [Visa distributionsåtgärder](deployment-history.md).
