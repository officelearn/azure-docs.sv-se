---
title: Flytta resurser till en ny prenumeration eller resursgrupp
description: Använd Azure Resource Manager för att flytta resurser till en ny resursgrupp eller prenumeration.
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: 40432c55a7f7e289d2e5cbc8afe94847074e4ca8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248858"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>Flytta resurser till en ny resursgrupp eller prenumeration

Den här artikeln visar hur du flyttar Azure-resurser till antingen en annan Azure-prenumeration eller en annan resursgrupp under samma prenumeration. Du kan använda Azure-portalen, Azure PowerShell, Azure CLI eller REST-API:et för att flytta resurser.

Både källgruppen och målgruppen är låsta under flytten. Skriv- och borttagningsåtgärder blockeras för resursgrupperna tills flytten är klar. Det här låset innebär att du inte kan lägga till, uppdatera eller ta bort resurser i resursgrupperna. Det betyder inte att resurserna är frusna. Om du till exempel flyttar en SQL Server och dess databas till en ny resursgrupp sker inga avbrott för programmet som använder databasen. Det kan fortfarande läsa och skriva till databasen. Låset kan pågå i högst fyra timmar, men de flesta rör sig komplett i mycket kortare tid.

Om du flyttar en resurs flyttas den bara till en ny resursgrupp eller prenumeration. Resursens plats ändras inte.

## <a name="checklist-before-moving-resources"></a>Checklista för att flytta resurser

Några viktiga steg måste utföras innan en resurs flyttas. Du kan undvika fel genom att verifiera dessa villkor.

1. De resurser som du vill flytta måste stödja flyttåtgärden. En lista över vilka resurser som stöder flytta finns i [Flytta åtgärdsstöd för resurser](move-support-resources.md).

1. Vissa tjänster har specifika begränsningar eller krav när resurser flyttas. Om du flyttar någon av följande tjänster kontrollerar du den vägledningen innan du flyttar.

   * [Flytta vägledning för App Services](./move-limitations/app-service-move-limitations.md)
   * [Flytta vägledning för Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [Vägledning för klassisk distributionsmodellflyttning](./move-limitations/classic-model-move-limitations.md) – klassisk beräkning, klassisk lagring, klassiska virtuella nätverk och molntjänster
   * [Vägledning för nätverksflytt](./move-limitations/networking-move-limitations.md)
   * [Vägledning för Återställningstjänster flyttar](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [Flytta vägledning för virtuella datorer](./move-limitations/virtual-machines-move-limitations.md)

1. Käll- och målprenumerationerna måste vara aktiva. Om du har problem med att aktivera ett konto som har inaktiverats [skapar du en Azure-supportbegäran](../../azure-portal/supportability/how-to-create-azure-support-request.md). Välj **Prenumerationshantering** för ärendetypen.

1. Käll- och målprenumerationerna måste finnas i samma [Azure Active Directory-klientorganisation](../../active-directory/develop/quickstart-create-new-tenant.md). Om du vill kontrollera att båda prenumerationerna har samma klient-ID använder du Azure PowerShell eller Azure CLI.

   För Azure PowerShell använder du:

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   Om du använder Azure CLI använder du:

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   Om klient-ID:t för käll- och målprenumerationerna inte är desamma använder du följande metoder för att stämma av klient-ID:erna:

   * [Överföra ägarskap för en Azure-prenumeration till ett annat konto](../../billing/billing-subscription-transfer.md)
   * [Så här associerar du eller lägger till en prenumeration i din Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. Målprenumerationen måste vara registrerad för resursprovidern för den resurs som flyttas. Om inte visas ett felmeddelande om att **prenumerationen inte är registrerad för en resurstyp**. Det här felet kan visas när du flyttar en resurs till en ny prenumeration, men den prenumerationen har aldrig använts med den resurstypen.

   För PowerShell använder du följande kommandon för att få registreringsstatus:

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   Om du vill registrera en resursleverantör använder du:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   För Azure CLI använder du följande kommandon för att få registreringsstatus:

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   Om du vill registrera en resursleverantör använder du:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. Kontot som flyttar resurserna måste ha minst följande behörigheter:

   * **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action** i källresursgruppen.
   * **Microsoft.Resources/subscriptions/resourceGroups/write** på målresursgruppen.

1. Innan du flyttar resurserna kontrollerar du prenumerationskvoterna för den prenumeration som du flyttar resurserna till. Om flytten av resurserna innebär att prenumerationen överskrider sina gränser måste du granska om du kan begära en ökning av kvoten. En lista över begränsningar och hur du begär en ökning finns i [Azure-prenumerations- och tjänstgränser, kvoter och begränsningar](../../azure-resource-manager/management/azure-subscription-service-limits.md).

1. **För en flytt mellan prenumerationer måste resursen och dess beroende resurser finnas i samma resursgrupp och de måste flyttas tillsammans.** En virtuell dator med hanterade diskar kräver till exempel att den virtuella datorn och de hanterade diskarna flyttas tillsammans, tillsammans med andra beroende resurser.

   Om du flyttar en resurs till en ny prenumeration kontrollerar du om resursen har några beroende resurser och om de finns i samma resursgrupp. Om resurserna inte finns i samma resursgrupp kontrollerar du om resurserna kan konsolideras till samma resursgrupp. Om så är fallet, föra in alla dessa resurser i samma resursgrupp med hjälp av en flyttåtgärd mellan resursgrupper.

   Mer information finns i [Scenario för flyttning mellan prenumerationer](#scenario-for-move-across-subscriptions).

## <a name="scenario-for-move-across-subscriptions"></a>Scenario för att flytta mellan prenumerationer

Att flytta resurser från en prenumeration till en annan är en process i tre steg:

![flytta scenario med flera prenumerationer](./media/move-resource-group-and-subscription/cross-subscription-move-scenario.png)

Som illustration har vi bara en beroende resurs.

* Steg 1: Om beroende resurser fördelas mellan olika resursgrupper flyttar du dem först till en resursgrupp.
* Steg 2: Flytta resursen och de beroende resurserna tillsammans från källprenumerationen till målprenumerationen.
* Steg 3: Du kan också distribuera om de beroende resurserna till olika resursgrupper i målprenumerationen. 

## <a name="validate-move"></a>Validera flytta

Med [åtgärden validera flytta](/rest/api/resources/resources/validatemoveresources) kan du testa ditt flyttscenario utan att flytta resurserna. Använd den här åtgärden för att kontrollera om flytten kommer att lyckas. Validering anropas automatiskt när du skickar en flyttbegäran. Använd endast den här åtgärden när du behöver bestämma resultatet i förväg. För att kunna köra den här åtgärden behöver du:

* namn på källresursgruppen
* resurs-ID för målresursgruppen
* resurs-ID för varje resurs som ska flyttas
* [åtkomsttoken](/rest/api/azure/#acquire-an-access-token) för ditt konto

Skicka följande begäran:

```HTTP
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2019-05-10
Authorization: Bearer <access-token>
Content-type: application/json
```

Med en begäran kropp:

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Om begäran är korrekt formaterad returneras åtgärden:

```HTTP
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

Statuskoden 202 anger att valideringsbegäran accepterades, men den har ännu inte fastställt om flyttåtgärden kommer att lyckas. Värdet `location` innehåller en URL som du använder för att kontrollera status för den långvariga åtgärden.  

Om du vill kontrollera statusen skickar du följande begäran:

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

Medan åtgärden fortfarande körs fortsätter du att få statuskoden 202. Vänta på antalet sekunder som `retry-after` anges i värdet innan du försöker igen. Om flyttåtgärden valideras får du statuskoden 204. Om flyttvalideringen misslyckas visas ett felmeddelande, till exempel:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="use-the-portal"></a>Använda portalen

Om du vill flytta resurser markerar du resursgruppen med dessa resurser och väljer sedan knappen **Flytta.**

![flytta resurser](./media/move-resource-group-and-subscription/select-move.png)

Välj om du flyttar resurserna till en ny resursgrupp eller en ny prenumeration.

Markera de resurser som ska flyttas och målresursgruppen. Bekräfta att du behöver uppdatera skript för dessa resurser och välj **OK**. Om du har valt ikonen för redigeringsprenumerationen i föregående steg måste du också välja målprenumerationen.

![välj destination](./media/move-resource-group-and-subscription/select-destination.png)

I **Meddelanden**ser du att flyttåtgärden körs.

![visa flyttstatus](./media/move-resource-group-and-subscription/show-status.png)

När den är klar får du ett meddelande om resultatet.

![visa flytta resultat](./media/move-resource-group-and-subscription/show-result.png)

Om du får ett felmeddelande läser [du Felsöka att flytta Azure-resurser till ny resursgrupp eller prenumeration](troubleshoot-move.md).

## <a name="use-azure-powershell"></a>Använda Azure PowerShell

Om du vill flytta befintliga resurser till en annan resursgrupp eller prenumeration använder du kommandot [Flytta-AzResource.](/powershell/module/az.resources/move-azresource) I följande exempel visas hur du flyttar flera resurser till en ny resursgrupp.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Om du vill flytta till en `DestinationSubscriptionId` ny prenumeration inkluderar du ett värde för parametern.

Om du får ett felmeddelande läser [du Felsöka att flytta Azure-resurser till ny resursgrupp eller prenumeration](troubleshoot-move.md).

## <a name="use-azure-cli"></a>Använda Azure CLI

Om du vill flytta befintliga resurser till en annan resursgrupp eller prenumeration använder du kommandot [för flytta az-resurs.](/cli/azure/resource?view=azure-cli-latest#az-resource-move) Ange resurs-ID:erna för de resurser som ska flyttas. I följande exempel visas hur du flyttar flera resurser till en ny resursgrupp. I `--ids` parametern anger du en utrymmesavgränsad lista över de resurs-ID:er som ska flyttas.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Om du vill flytta till `--destination-subscription-id` en ny prenumeration anger du parametern.

Om du får ett felmeddelande läser [du Felsöka att flytta Azure-resurser till ny resursgrupp eller prenumeration](troubleshoot-move.md).

## <a name="use-rest-api"></a>Använda REST-API

Om du vill flytta befintliga resurser till en annan resursgrupp eller prenumeration använder du åtgärden [Flytta resurser.](/rest/api/resources/Resources/MoveResources)

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

I begärandetexten anger du målgruppen och de resurser som ska flyttas.

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Om du får ett felmeddelande läser [du Felsöka att flytta Azure-resurser till ny resursgrupp eller prenumeration](troubleshoot-move.md).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Fråga: Min resursflyttning, som vanligtvis tar några minuter, har pågått i nästan en timme. Är det något fel?**

Att flytta en resurs är en komplex åtgärd som har olika faser. Det kan handla om mer än bara resursleverantören för den resurs du försöker flytta. På grund av beroenden mellan resursleverantörer tillåter Azure Resource Manager 4 timmar för åtgärden att slutföras. Den här tidsperioden ger resursleverantörer en chans att återställa från tillfälliga problem. Om din flyttbegäran är inom 4-timmarsperioden fortsätter åtgärden att försöka slutföras och kan fortfarande lyckas. Käll- och målresursgrupperna är låsta under den här tiden för att undvika konsekvensproblem.

**Fråga: Varför är min resursgrupp låst i 4 timmar under resursflytten?**

4-timmarsfönstret är den maximala tillåtna tiden för resursflyttning. För att förhindra ändringar av de resurser som flyttas, är både käll- och målresursgrupperna låsta under resursflyttens varaktighet.

Det finns två faser i en flyttbegäran. I den första fasen flyttas resursen. I den andra fasen skickas meddelanden till andra resursleverantörer som är beroende av att resursen flyttas. En resursgrupp kan låsas för hela 4-timmarsfönstret när en resursprovider misslyckas i båda faserna. Under den tillåtna tiden försöker Resource Manager det misslyckade steget.

Om en resurs inte kan flyttas inom 4-timmarsfönstret låser Resurshanteraren upp båda resursgrupperna. Resurser som har flyttats finns i målresursgruppen. Resurser som inte kunde flyttas lämnas källresursgruppen.

**Fråga: Vilka konsekvenser får käll- och målresursgrupperna att låsas under resursflytten?**

Låset hindrar dig från att ta bort någon av resursgrupperna, skapa en ny resurs i antingen resursgruppen eller ta bort någon av de resurser som ingår i flytten.

Följande bild visar ett felmeddelande från Azure-portalen när en användare försöker ta bort en resursgrupp som är en del av ett pågående drag.

![Flytta felmeddelandet som försöker ta bort](./media/move-resource-group-and-subscription/move-error-delete.png)

**Fråga: Vad betyder felkoden "MissingMoveDependentResources"?**

När du flyttar en resurs måste dess beroende resurser antingen finnas i målresursgruppen eller prenumerationen eller inkluderas i flyttbegäran. Felkoden MissingMoveDependentResources visas när en beroende resurs inte uppfyller det här kravet. Felmeddelandet innehåller information om den beroende resurs som måste inkluderas i flyttbegäran.

Om du till exempel flyttar en virtuell dator kan det krävas att sju resurstyper flyttas med tre olika resursleverantörer. Dessa resursleverantörer och typer är:

* Microsoft.Compute
   * virtuellaMaskiner
   * Diskar
* Microsoft.Network
  * nätverkGränssnitt
  * offentliga IP-adresser
  * nätverkSäkerhetsgrupper
  * virtualNetworks
* Microsoft.Storage
  * lagringKonton

Ett annat vanligt exempel är att flytta ett virtuellt nätverk. Du kan behöva flytta flera andra resurser som är associerade med det virtuella nätverket. Flyttbegäran kan kräva att offentliga IP-adresser, vägtabeller, virtuella nätverksgateways, nätverkssäkerhetsgrupper och andra flyttas.

**Fråga: Varför kan jag inte flytta vissa resurser i Azure?**

För närvarande flyttas inte alla resurser i Azure-supporten. En lista över resurser som stöder flytten finns i [Flytta åtgärdsstöd för resurser](move-support-resources.md).

## <a name="next-steps"></a>Nästa steg

En lista över vilka resurser som stöder flytta finns i [Flytta åtgärdsstöd för resurser](move-support-resources.md).
