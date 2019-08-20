---
title: Flytta Azure-resurser till en ny prenumeration eller resurs grupp | Microsoft Docs
description: Använd Azure Resource Manager för att flytta resurser till en ny resursgrupp eller prenumeration.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: 114e0d8e935aa8e6ac3f70a34a8050b19758fb42
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624562"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>Flytta resurser till en ny resurs grupp eller prenumeration

Den här artikeln visar hur du flyttar Azure-resurser till antingen en annan Azure-prenumeration eller en annan resurs grupp under samma prenumeration. Du kan använda Azure Portal, Azure PowerShell, Azure CLI eller REST API för att flytta resurser.

Både käll gruppen och mål gruppen är låsta under flytt åtgärden. Skriva och ta bort blockeras på resursgrupper tills flyttningen är klar. Låset innebär att du kan inte lägga till, uppdatera eller ta bort resurser i resursgrupper, men det innebär inte att resurserna som är låsta. Om du flyttar en SQL Server och dess databas till en ny resursgrupp, inträffar ett program som använder databasen utan avbrott. Det kan fortfarande läsa och skriva till databasen.

Om du flyttar en resurs flyttas den bara till en ny resurs grupp eller prenumeration. Platsen för resursen ändras inte.

## <a name="checklist-before-moving-resources"></a>Checklistan innan du flyttar resurser

Några viktiga steg måste utföras innan en resurs flyttas. Du kan undvika fel genom att verifiera dessa villkor.

1. De resurser som du vill flytta måste stödja flytt åtgärden. En lista över vilka resurser som stöder flytta finns i [Flytta åtgärds stöd för resurser](move-support-resources.md).

1. Vissa tjänster har särskilda begränsningar eller krav när du flyttar resurser. Om du har flyttat någon av följande tjänster, kontrol lera den här vägledningen innan du flyttar.

   * [Vägledning för App Services flytt](./move-limitations/app-service-move-limitations.md)
   * [Vägledning för flytt av Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [Klassisk distributions modell flytta vägledning](./move-limitations/classic-model-move-limitations.md) – klassisk beräkning, klassisk lagring, klassiska virtuella nätverk och Cloud Services
   * [Vägledning om nätverks flytt](./move-limitations/networking-move-limitations.md)
   * [Vägledning för Recovery Services flytt](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [Vägledning för Virtual Machines flytt](./move-limitations/virtual-machines-move-limitations.md)

   Om mål resurs gruppen innehåller ett virtuellt nätverk kan statusen för dess beroende resurser blockera flyttningen, även om resurserna inte är involverade i flytten. Mer information finns i avsnittet om att [Flytta rikt linjer för nätverk](./move-limitations/virtual-network-move-limitations.md).

1. Käll-och mål prenumerationerna måste vara aktiva. Om du har problem med att aktivera ett konto som har inaktiverats [skapar du en support förfrågan för Azure](../azure-supportability/how-to-create-azure-support-request.md). Välj **prenumerationshantering** för typ av ärende.

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

1. Målprenumerationen måste vara registrerad för resursprovidern för den resurs som flyttas. Om inte, du får ett felmeddelande om att den **prenumerationen har inte registrerats för en resurstyp**. Du kanske ser det här felet när du flyttar en resurs till en ny prenumeration, men prenumerationen har aldrig använts med den resurs typen.

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

1. **För att flytta över prenumerationer måste resursen och dess beroende resurser finnas i samma resurs grupp och de måste flyttas tillsammans.** En virtuell dator med Managed disks skulle till exempel kräva att den virtuella datorn och de hanterade diskarna flyttas tillsammans, tillsammans med andra beroende resurser.

   Om du flyttar en resurs till en ny prenumeration kan du kontrol lera om resursen har resurser som är beroende av varandra och om de finns i samma resurs grupp. Om resurserna inte finns i samma resurs grupp kontrollerar du om resurserna kan konsol IDE ras i samma resurs grupp. I så fall kan du ta med alla dessa resurser i samma resurs grupp genom att använda en flyttnings åtgärd över resurs grupper.

   Mer information finns i [scenario för att flytta mellan prenumerationer](#scenario-for-move-across-subscriptions).

## <a name="scenario-for-move-across-subscriptions"></a>Scenario för att flytta mellan prenumerationer

Att flytta resurser från en prenumeration till en annan är en process i tre steg:

![flytt scenario mellan prenumerationer](./media/resource-group-move-resources/cross-subscription-move-scenario.png)

I illustrations syfte har vi bara en beroende resurs.

* Steg 1: Om beroende resurser fördelas över olika resurs grupper måste du först flytta dem till en resurs grupp.
* Steg 2: Flytta resurs-och beroende resurser tillsammans från käll prenumerationen till mål prenumerationen.
* Steg 3: Alternativt kan du distribuera om de beroende resurserna till olika resurs grupper inom mål prenumerationen. 

## <a name="validate-move"></a>Verifiera flytt

Den [verifiera flyttåtgärden](/rest/api/resources/resources/validatemoveresources) kan du testa ditt move-scenario utan att faktiskt flytta resurserna. Använd den här åtgärden för att kontrol lera om flytten kommer att lyckas. Verifiering anropas automatiskt när du skickar en flyttnings förfrågan. Använd endast den här åtgärden när du måste för att fastställa resultaten. Om du vill köra den här åtgärden, måste den:

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

Om du vill flytta resurser väljer du resurs gruppen med dessa resurser och väljer sedan knappen **Flytta** .

![Flytta resurser](./media/resource-group-move-resources/select-move.png)

Välj om du flyttar resurser till en ny resursgrupp eller en ny prenumeration.

Välj resurser att flytta och målresursgruppen. Bekräfta att du behöver uppdatera skript för dessa resurser och välj **OK**. Om du har valt prenumerationen redigeringsikonen i föregående steg, måste du också välja målprenumerationen.

![Välj mål](./media/resource-group-move-resources/select-destination.png)

I **meddelanden**, du ser att flyttåtgärden körs.

![Visa flytta status](./media/resource-group-move-resources/show-status.png)

När den har slutförts meddelas du om resultatet.

![Visa flytta resultat](./media/resource-group-move-resources/show-result.png)

Om du får ett fel meddelande kan du läsa mer i [Felsöka flytt av Azure-resurser till en ny resurs grupp eller prenumeration](troubleshoot-move.md).

## <a name="use-azure-powershell"></a>Använda Azure PowerShell

Om du vill flytta befintliga resurser till en annan resurs grupp eller prenumeration använder du kommandot [Move-AzResource](/powershell/module/az.resources/move-azresource) . I följande exempel visas hur du flyttar flera resurser till en ny resurs grupp.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Om du vill flytta till en ny prenumeration kan innehålla ett värde för den `DestinationSubscriptionId` parametern.

Om du får ett fel meddelande kan du läsa mer i [Felsöka flytt av Azure-resurser till en ny resurs grupp eller prenumeration](troubleshoot-move.md).

## <a name="use-azure-cli"></a>Använda Azure CLI

Flytta befintliga resurser till en annan resursgrupp eller prenumeration genom att använda den [az resursflytt](/cli/azure/resource?view=azure-cli-latest#az-resource-move) kommando. Ange resurs-ID resurser att flytta. I följande exempel visas hur du flyttar flera resurser till en ny resurs grupp. I den `--ids` parameter, ange en blankstegsavgränsad lista med resurs-ID för att flytta.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Om du vill flytta till en ny prenumeration, ange den `--destination-subscription-id` parametern.

Om du får ett fel meddelande kan du läsa mer i [Felsöka flytt av Azure-resurser till en ny resurs grupp eller prenumeration](troubleshoot-move.md).

## <a name="use-rest-api"></a>Använda REST-API

Om du vill flytta befintliga resurser till en annan resurs grupp eller prenumeration använder du åtgärden [Flytta resurser](/rest/api/resources/Resources/MoveResources) .

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

Om du får ett fel meddelande kan du läsa mer i [Felsöka flytt av Azure-resurser till en ny resurs grupp eller prenumeration](troubleshoot-move.md).

## <a name="next-steps"></a>Nästa steg

En lista över vilka resurser som stöder flytta finns i [Flytta åtgärds stöd för resurser](move-support-resources.md).
