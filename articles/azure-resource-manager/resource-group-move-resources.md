---
title: Flytta Azure-resurser till en ny prenumeration eller resursgrupp grupp | Microsoft Docs
description: Använd Azure Resource Manager för att flytta resurser till en ny resursgrupp eller prenumeration.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 01ec8facf2771de9ec01b9470521340a59ee4d0d
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721379"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>Flytta resurser till en ny resursgrupp eller prenumeration

Den här artikeln visar hur du flyttar Azure-resurser till en annan Azure-prenumeration eller en annan resursgrupp i samma prenumeration. Du kan använda Azure-portalen, Azure PowerShell, Azure CLI eller REST API för att flytta resurser.

Både källgruppen och målgruppen är låsta vid flytt. Skriva och ta bort blockeras på resursgrupper tills flyttningen är klar. Låset innebär att du kan inte lägga till, uppdatera eller ta bort resurser i resursgrupper, men det innebär inte att resurserna som är låsta. Om du flyttar en SQL Server och dess databas till en ny resursgrupp, inträffar ett program som använder databasen utan avbrott. Det kan fortfarande läsa och skriva till databasen.

En resurs flyttas bara flyttar det till en ny resursgrupp eller prenumeration. Den ändrar inte platsen för resursen.

## <a name="checklist-before-moving-resources"></a>Checklistan innan du flyttar resurser

Några viktiga steg måste utföras innan en resurs flyttas. Du kan undvika fel genom att verifiera dessa villkor.

1. Flyttåtgärden måste ha stöd för de resurser som du vill flytta. En lista som resurser stöd för att flytta finns i [flytta åtgärden stöd för resurser](move-support-resources.md).

1. Vissa tjänster har vissa begränsningar eller krav när du flyttar resurser. Om du har flytta någon av följande tjänster, kontrollera som vägledning innan du fortsätter.

   * [Flytta vägledning för App Services](./move-limitations/app-service-move-limitations.md)
   * [Flytta vägledning för Azure DevOps-tjänster](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [Klassisk distribution modellen flytta vägledning](./move-limitations/classic-model-move-limitations.md) -klassiska Compute, klassisk lagring, klassiska virtuella nätverk och molntjänster
   * [Recovery Services flytta vägledning](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [Flytta vägledning för virtuella datorer](./move-limitations/virtual-machines-move-limitations.md)
   * [Flytta vägledning för virtuella nätverk](./move-limitations/virtual-network-move-limitations.md)

1. Käll- och målprenumerationer måste vara aktiv. Om du har problem med att aktivera ett konto som har inaktiverats, [skapa en Azure-supportbegäran](../azure-supportability/how-to-create-azure-support-request.md). Välj **prenumerationshantering** för typ av ärende.

1. Käll- och målprenumerationer måste finnas inom samma [Azure Active Directory-klient](../active-directory/develop/quickstart-create-new-tenant.md). Om du vill kontrollera att båda prenumerationerna har samma klient-ID, använder du Azure PowerShell eller Azure CLI.

   Använd för Azure PowerShell:

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   Om du använder Azure CLI använder du:

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   Om klient-ID: N för käll- och målprenumerationer inte är samma, kan du använda följande metoder för att stämma av klient-ID: N:

   * [Överföra ägarskap för en Azure-prenumeration till ett annat konto](../billing/billing-subscription-transfer.md)
   * [Så här associerar du eller lägger till en prenumeration i din Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. Målprenumerationen måste vara registrerad för resursprovidern för den resurs som flyttas. Om inte, du får ett felmeddelande om att den **prenumerationen har inte registrerats för en resurstyp**. Du kan se det här felet när du flyttar en resurs till en ny prenumeration, men att prenumerationen aldrig har använts med den resurstypen.

   Använd följande kommandon för att hämta registreringsstatus PowerShell:

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   Om du vill registrera en resursleverantör, använder du:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   För Azure CLI, använder du följande kommandon för att hämta registreringsstatus:

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   Om du vill registrera en resursleverantör, använder du:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. Det konto som flyttar resurser måste ha minst följande behörigheter:

   * **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action** på resursgrupp för källa.
   * **Microsoft.Resources/subscriptions/resourceGroups/write** på målresursgruppen.

1. Kontrollera prenumerationskvoter för den prenumeration som du flyttar resurser till innan du flyttar resurser. Om du flytta resurserna innebär prenumerationen kommer att överskrida gränsen, måste du granska om du kan begära en ökning av kvoten. En lista över begränsningar och hur du begär en ökning, se [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](../azure-subscription-service-limits.md).

## <a name="validate-move"></a>Verifiera flytt

Den [verifiera flyttåtgärden](/rest/api/resources/resources/validatemoveresources) kan du testa ditt move-scenario utan att faktiskt flytta resurserna. Du kan använda den här åtgärden för att kontrollera om flytten lyckas. Verifiering kallas automatiskt när du skickar en begäran om att flytta. Använd den här åtgärden endast när du behöver du i förväg bestämmer resultaten. Om du vill köra den här åtgärden, måste den:

* namn på resursgrupp för källa
* resurs-ID för målresursgruppen
* resurs-ID för varje resurs att flytta
* den [åtkomsttoken](/rest/api/azure/#acquire-an-access-token) för ditt konto

Skicka följande begäran:

```HTTP
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2019-05-10
Authorization: Bearer <access-token>
Content-type: application/json
```

Med en brödtext i begäran:

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Om begäran har formaterats korrekt, returnerar åtgärden:

```HTTP
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

202 statuskoden anger verifieringsförfrågan togs emot, men den inte har fastställt om flyttåtgärden lyckas. Den `location` värdet innehåller en URL som används för att kontrollera statusen för långvarig åtgärd.  

Om du vill kontrollera statusen genom att skicka följande begäran:

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

Medan åtgärden körs, fortsätta att ta emot 202 statuskoden. Vänta antalet sekunder som anges i den `retry-after` värdet innan du försöker igen. Om åtgärden för att flytta verifieras felmeddelandet 204 statuskoden. Om flytten valideringen misslyckas visas ett felmeddelande som:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="use-the-portal"></a>Använda portalen

Välj resursgruppen med dessa resurser för att flytta resurser, och välj sedan den **flytta** knappen.

![Flytta resurser](./media/resource-group-move-resources/select-move.png)

Välj om du flyttar resurser till en ny resursgrupp eller en ny prenumeration.

Välj resurser att flytta och målresursgruppen. Bekräfta att du behöver uppdatera skript för dessa resurser och välj **OK**. Om du har valt prenumerationen redigeringsikonen i föregående steg, måste du också välja målprenumerationen.

![Välj mål](./media/resource-group-move-resources/select-destination.png)

I **meddelanden**, du ser att flyttåtgärden körs.

![Visa flytta status](./media/resource-group-move-resources/show-status.png)

När den har slutförts meddelas du om resultatet.

![Visa flytta resultat](./media/resource-group-move-resources/show-result.png)

Om du får ett felmeddelande visas [Felsök flytta Azure-resurser till ny resursgrupp eller prenumeration](troubleshoot-move.md).

## <a name="use-azure-powershell"></a>Använda Azure PowerShell

Flytta befintliga resurser till en annan resursgrupp eller prenumeration genom att använda den [flytta AzResource](/powershell/module/az.resources/move-azresource) kommando. I följande exempel visas hur du flyttar flera resurser till en ny resursgrupp.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Om du vill flytta till en ny prenumeration kan innehålla ett värde för den `DestinationSubscriptionId` parametern.

Om du får ett felmeddelande visas [Felsök flytta Azure-resurser till ny resursgrupp eller prenumeration](troubleshoot-move.md).

## <a name="use-azure-cli"></a>Använda Azure CLI

Flytta befintliga resurser till en annan resursgrupp eller prenumeration genom att använda den [az resursflytt](/cli/azure/resource?view=azure-cli-latest#az-resource-move) kommando. Ange resurs-ID resurser att flytta. I följande exempel visas hur du flyttar flera resurser till en ny resursgrupp. I den `--ids` parameter, ange en blankstegsavgränsad lista med resurs-ID för att flytta.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Om du vill flytta till en ny prenumeration, ange den `--destination-subscription-id` parametern.

Om du får ett felmeddelande visas [Felsök flytta Azure-resurser till ny resursgrupp eller prenumeration](troubleshoot-move.md).

## <a name="use-rest-api"></a>Använda REST-API

Flytta befintliga resurser till en annan resursgrupp eller prenumeration genom att använda den [flytta resurser](/rest/api/resources/Resources/MoveResources) igen.

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

I begärandetexten anger du målresursgruppen och resurser för att flytta.

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Om du får ett felmeddelande visas [Felsök flytta Azure-resurser till ny resursgrupp eller prenumeration](troubleshoot-move.md).

## <a name="next-steps"></a>Nästa steg

En lista som resurser stöd för att flytta finns i [flytta åtgärden stöd för resurser](move-support-resources.md).
