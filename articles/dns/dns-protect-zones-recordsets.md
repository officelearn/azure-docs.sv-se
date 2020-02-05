---
title: Skydda DNS-zoner och poster – Azure DNS
description: I den här utbildnings vägen ska du komma igång med att skydda DNS-zoner och post uppsättningar i Microsoft Azure DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 12/4/2018
ms.author: rohink
ms.openlocfilehash: 549090f04f4969b00dc1c8ee8d5cc70a50523ca8
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76983834"
---
# <a name="how-to-protect-dns-zones-and-records"></a>Skydda DNS-zoner och-poster

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

DNS-zoner och-poster är kritiska resurser. Att ta bort en DNS-zon eller till och med bara en enskild DNS-post kan resultera i ett total avbrott i tjänsten.  Det är därför viktigt att kritiska DNS-zoner och poster skyddas mot obehöriga eller oavsiktliga ändringar.

I den här artikeln förklaras hur Azure DNS hjälper dig att skydda dina DNS-zoner och-poster mot sådana ändringar.  Vi tillämpar två kraftfulla säkerhetsfunktioner som tillhandahålls av Azure Resource Manager: [rollbaserad åtkomst kontroll](../role-based-access-control/overview.md) och [resurs lås](../azure-resource-manager/management/lock-resources.md).

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

Med Azure Role-baserade Access Control (RBAC) kan du få detaljerad åtkomst hantering för Azure-användare, grupper och resurser. Med RBAC kan du ge exakt den mängd åtkomst som användarna behöver för att utföra sina jobb. Mer information om hur RBAC hjälper dig att hantera åtkomst finns i [Vad är rollbaserad Access Control](../role-based-access-control/overview.md).

### <a name="the-dns-zone-contributor-role"></a>Rollen som deltagare i DNS-zonen

Rollen som deltagare i DNS-zonen är en inbyggd roll som tillhandahålls av Azure för hantering av DNS-resurser.  Genom att tilldela behörighet för DNS-zonen till en användare eller grupp kan gruppen hantera DNS-resurser, men inte resurser av någon annan typ.

Anta till exempel att *resurs gruppens zoner innehåller* fem zoner för Contoso Corporation. Att bevilja DNS-administratörens behörighet för DNS-zonen deltagare i den resurs gruppen ger fullständig kontroll över dessa DNS-zoner. Den förhindrar också att du beviljar onödiga behörigheter, till exempel kan DNS-administratören inte skapa eller stoppa Virtual Machines.

Det enklaste sättet att tilldela RBAC-behörigheter är [via Azure Portal](../role-based-access-control/role-assignments-portal.md).  Öppna **åtkomst kontroll (IAM)** för resurs gruppen och välj sedan **Lägg till**och välj sedan rollen **DNS Zone Contributor** och välj de användare eller grupper som krävs för att bevilja behörigheter.

![Resurs grupp nivå RBAC via Azure Portal](./media/dns-protect-zones-recordsets/rbac1.png)

Behörigheter kan också [beviljas med Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
New-AzRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>"
```

Motsvarande kommando är också [tillgängligt via Azure CLI](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --resourceGroup "<resource group name>"
```

### <a name="zone-level-rbac"></a>RBAC för zon nivå

Azure RBAC-regler kan användas för en prenumeration, en resurs grupp eller en enskild resurs. Om det är Azure DNS kan resursen vara en enskild DNS-zon eller till och med en enskild post uppsättning.

Anta till exempel att *resurs gruppen innehåller zonen* *contoso.com* och en under zon *Customers.contoso.com* där CNAME-poster skapas för varje kund konto.  Det konto som används för att hantera dessa CNAME-poster ska tilldelas behörigheter för att skapa poster i *Customers.contoso.com* -zonen, den ska inte ha åtkomst till de andra zonerna.

RBAC-behörigheter på Zone-nivå kan beviljas via Azure Portal.  Öppna **åtkomst kontroll (IAM)** för zonen, välj sedan **Lägg till**och välj sedan rollen för **DNS-zonen deltagare** och välj de användare eller grupper som krävs för att bevilja behörigheter.

![DNS-zons nivå RBAC via Azure Portal](./media/dns-protect-zones-recordsets/rbac2.png)

Behörigheter kan också [beviljas med Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to a specific zone
New-AzRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>" -ResourceName "<zone name>" -ResourceType Microsoft.Network/DNSZones
```

Motsvarande kommando är också [tillgängligt via Azure CLI](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone
azure role assignment create --signInName <user email address> --roleName "DNS Zone Contributor" --resource-name <zone name> --resource-type Microsoft.Network/DNSZones --resource-group <resource group name>
```

### <a name="record-set-level-rbac"></a>Data uppsättnings nivå RBAC

Vi kan gå ett steg närmare. Överväg e-postadministratören för Contoso Corporation, som behöver åtkomst till MX-och TXT-posterna i contoso.com-zonens spets.  Hon behöver inte åtkomst till någon annan MX-eller TXT-post, eller till några poster av någon annan typ.  Med Azure DNS kan du tilldela behörigheter på post uppsättnings nivån, exakt vilka poster som e-administratören behöver åtkomst till.  E-postadministratören beviljas exakt den kontroll hon behöver, och det går inte att göra några andra ändringar.

Du kan konfigurera RBAC-behörigheter för post uppsättnings nivåer via Azure Portal med hjälp av knappen **användare** på sidan post uppsättning:

![Post uppsättnings nivå RBAC via Azure Portal](./media/dns-protect-zones-recordsets/rbac3.png)

Det går även att bevilja RBAC-behörigheter för post uppsättnings nivåer [med hjälp av Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant permissions to a specific record set
New-AzRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -Scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

Motsvarande kommando är också [tillgängligt via Azure CLI](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant permissions to a specific record set
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Anpassade roller

Den inbyggda rollen för DNS-zon Contributor möjliggör fullständig kontroll över en DNS-resurs. Det är också möjligt att skapa egna Azure-roller för kunder för att ge ännu bättre kontroll.

Överväg att göra om exemplet där en CNAME-post i zonen *Customers.contoso.com* skapas för varje Contoso Corporation-kund konto.  Det konto som används för att hantera dessa CNAME ska beviljas behörighet att endast hantera CNAME-poster.  Det går sedan inte att ändra poster av andra typer (t. ex. genom att ändra MX-poster) eller utföra åtgärder på zon nivå, till exempel zon borttagning.

I följande exempel visas en anpassad roll definition för hantering av CNAME-poster:

```json
{
    "Name": "DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/dnsZones/CNAME/*",
        "Microsoft.Network/dnsZones/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
    ],
    "NotActions": [
    ],
    "AssignableScopes": [
        "/subscriptions/ c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

Egenskapen åtgärder definierar följande DNS-/regionsspecifika behörigheter:

* `Microsoft.Network/dnsZones/CNAME/*` ger fullständig kontroll över CNAME-poster
* `Microsoft.Network/dnsZones/read` beviljar behörighet att läsa DNS-zoner, men inte att ändra dem, så att du kan se i vilken zon som CNAME skapas.

Återstående åtgärder kopieras från den [inbyggda rollen deltagare i DNS-zonen](../role-based-access-control/built-in-roles.md#dns-zone-contributor).

> [!NOTE]
> Om du använder en anpassad RBAC-roll för att förhindra borttagning av post uppsättningar samtidigt som du tillåter att den uppdateras är inte en effektiv kontroll. Det förhindrar att post uppsättningar tas bort, men den förhindrar inte att de ändras.  Tillåtna ändringar omfattar att lägga till och ta bort poster från post uppsättningen, inklusive att ta bort alla poster för att lämna en tom post uppsättning. Detta har samma resultat som när du tar bort post uppsättningen från en DNS-matchning.

Anpassade roll definitioner kan för närvarande inte definieras via Azure Portal. En anpassad roll som baseras på den här roll definitionen kan skapas med hjälp av Azure PowerShell:

```azurepowershell
# Create new role definition based on input file
New-AzRoleDefinition -InputFile <file path>
```

Den kan också skapas via Azure CLI:

```azurecli
# Create new role definition based on input file
azure role create -inputfile <file path>
```

Rollen kan sedan tilldelas på samma sätt som inbyggda roller, enligt beskrivningen ovan i den här artikeln.

Mer information om hur du skapar, hanterar och tilldelar anpassade roller finns i [anpassade roller i Azure RBAC](../role-based-access-control/custom-roles.md).

## <a name="resource-locks"></a>Resurs lås

Förutom RBAC har Azure Resource Manager stöd för en annan typ av säkerhets kontroll, nämligen möjligheten att låsa resurser. Om RBAC-regler tillåter att du styr åtgärderna för vissa användare och grupper, tillämpas resurs lås på resursen och gäller för alla användare och roller. Mer information finns i [Låsa resurser med Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).

Det finns två typer av resurs Lås: **CanNotDelete** och **ReadOnly**. Dessa kan användas antingen i en DNS-zon eller i en enskild post uppsättning.  I följande avsnitt beskrivs flera vanliga scenarier och hur du kan hantera dem med hjälp av resurs lås.

### <a name="protecting-against-all-changes"></a>Skydda mot alla ändringar

Använd ett skrivskyddat lås för zonen för att förhindra att ändringar görs.  Detta förhindrar att nya post uppsättningar skapas och att befintliga post uppsättningar ändras eller tas bort.

Resurs lås för zonens nivå kan skapas via Azure Portal.  Välj **Lås**på sidan DNS-zon och välj sedan **+ Lägg till**:

![Resurs lås på zon nivå via Azure Portal](./media/dns-protect-zones-recordsets/locks1.png)

Resurs lås på zon-nivå kan också skapas via Azure PowerShell:

```azurepowershell
# Lock a DNS zone
New-AzResourceLock -LockLevel <lock level> -LockName <lock name> -ResourceName <zone name> -ResourceType Microsoft.Network/DNSZones -ResourceGroupName <resource group name>
```

Det finns för närvarande inte stöd för att konfigurera Azure-resurs lås via Azure CLI.

### <a name="protecting-individual-records"></a>Skydda enskilda poster

Om du vill förhindra att en befintlig DNS-post har ändrats använder du ett skrivskyddat lås till post uppsättningen.

> [!NOTE]
> Att använda ett CanNotDelete-lås till en post uppsättning är inte en effektiv kontroll. Det förhindrar att post uppsättningen tas bort, men den förhindrar inte att den ändras.  Tillåtna ändringar omfattar att lägga till och ta bort poster från post uppsättningen, inklusive att ta bort alla poster för att lämna en tom post uppsättning. Detta har samma resultat som när du tar bort post uppsättningen från en DNS-matchning.

Resurs lås för post uppsättnings nivå kan för närvarande bara konfigureras med hjälp av Azure PowerShell.  De stöds inte i Azure Portal eller Azure CLI.

```azurepowershell
# Lock a DNS record set
New-AzResourceLock -LockLevel <lock level> -LockName "<lock name>" -ResourceName "<zone name>/<record set name>" -ResourceType "Microsoft.Network/DNSZones/<record type>" -ResourceGroupName "<resource group name>"
```

### <a name="protecting-against-zone-deletion"></a>Skydda mot zon borttagning

När en zon tas bort i Azure DNS raderas även alla post uppsättningar i zonen.  Du kan inte ångra den här åtgärden.  Att oavsiktligt ta bort en kritisk zon har möjlighet att få betydande påverkan på verksamheten.  Det är därför mycket viktigt att skydda mot oavsiktlig zon borttagning.

Om du använder ett CanNotDelete-lås till en zon förhindrar du att zonen tas bort.  Eftersom låsen ärvs av underordnade resurser, förhindrar den dock även att alla post uppsättningar i zonen tas bort, vilket kan vara oönskade.  Enligt beskrivningen i anteckningen ovan är den också ineffektiv eftersom poster fortfarande kan tas bort från de befintliga post uppsättningarna.

Alternativt bör du överväga att använda ett CanNotDelete-lås till en post uppsättning i zonen, till exempel SOA-postuppsättningen.  Eftersom zonen inte kan tas bort utan att även ta bort post uppsättningarna, skyddas detta mot zon borttagning, samtidigt som det fortfarande går att ändra post uppsättningar i zonen. Om ett försök görs att ta bort zonen, kan Azure Resource Manager identifiera detta även ta bort SOA-postuppsättningen och blockera anropet eftersom SOA är låst.  Inga post uppsättningar har tagits bort.

Följande PowerShell-kommando skapar ett CanNotDelete-lås mot SOA-posten för den aktuella zonen:

```azurepowershell
# Protect against zone delete with CanNotDelete lock on the record set
New-AzResourceLock -LockLevel CanNotDelete -LockName "<lock name>" -ResourceName "<zone name>/@" -ResourceType "Microsoft.Network/DNSZones/SOA" -ResourceGroupName "<resource group name>"
```

Ett annat sätt att förhindra borttagning av oavsiktliga zoner är genom att använda en anpassad roll för att säkerställa att operatören och tjänst kontona som används för att hantera dina zoner inte har behörighet att ta bort zonen. När du behöver ta bort en zon kan du framtvinga en borttagning av två steg, först bevilja zon borttagnings behörighet (vid zon omfånget för att förhindra borttagning av fel zon) och sekund för att ta bort zonen.

Den här andra metoden har fördelen att den fungerar för alla zoner som används av dessa konton, utan att behöva komma ihåg att skapa några lås. Det har en nackdel att alla konton med behörighet för zon borttagning, till exempel prenumerations ägaren, fortfarande kan ta bort en kritisk zon av misstag.

Det går att använda båda metoderna-resurs lås och anpassade roller – samtidigt, som en skydds djup metod för DNS-zonskydd.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du arbetar med RBAC finns i [Kom igång med åtkomst hantering i Azure Portal](../role-based-access-control/overview.md).
* Mer information om hur du arbetar med resurs lås finns i [låsa resurser med Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).
