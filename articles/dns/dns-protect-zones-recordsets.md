---
title: Skydda Azure DNS-zoner och poster
description: Så här skyddar du DNS-zoner och postuppsättningar i Microsoft Azure DNS.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 12/4/2018
ms.author: victorh
ms.openlocfilehash: 137d8e1c1477d5b9c88cecc39316d62a79a4cab8
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52873936"
---
# <a name="how-to-protect-dns-zones-and-records"></a>Så här skyddar du DNS-zoner och poster

DNS-zoner och poster är viktiga resurser. Tar bort en DNS-zon eller bara en enda DNS-post kan resultera i ett totalt tjänstavbrott.  Det är därför viktigt att kritiska DNS-zoner och poster skyddas mot obehöriga eller oavsiktliga ändringar.

Den här artikeln förklarar hur Azure DNS kan du skydda dina DNS-zoner och poster mot sådana ändringar.  Vi använder två kraftfulla säkerhetsfunktioner som tillhandahålls av Azure Resource Manager: [rollbaserad åtkomstkontroll](../role-based-access-control/overview.md) och [resurslås](../azure-resource-manager/resource-group-lock-resources.md).

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

Azure rollbaserad åtkomstkontroll (RBAC) möjliggör detaljerad åtkomsthantering för Azure-användare, grupper och resurser. Med RBAC kan bevilja du exakt åtkomstnivå som användare behöver att utföra sitt arbete. Mer information om hur RBAC hjälper dig att hantera åtkomst finns i [vad är rollbaserad åtkomstkontroll](../role-based-access-control/overview.md).

### <a name="the-dns-zone-contributor-role"></a>DNS-zon deltagarrollen

Rollen deltagare för DNS-zon är en inbyggd roll som tillhandahålls av Azure för att hantera DNS-resurser.  Tilldela DNS-Zondeltagare behörigheter till en användare eller grupp kan den gruppen för att hantera DNS-resurser, men inte resurser av någon annan typ.

Anta exempelvis att resursgruppen *myzones* innehåller fem zoner för Contoso Corporation. Tillståndsbeviljande DNS-administratören DNS-Zondeltagare till resursgruppen, möjliggör fullständig kontroll över dessa DNS-zoner. Det gör också att bevilja onödiga behörigheter, till exempel DNS-administratören inte kan skapa eller stoppa virtuella datorer.

Det enklaste sättet att tilldela RBAC-behörigheter är [via Azure portal](../role-based-access-control/role-assignments-portal.md).  Öppna **åtkomstkontroll (IAM)** för resursgruppen och sedan väljer **Lägg till**och välj sedan den **DNS-Zondeltagare** rollen och välj önskade användare eller grupper för att bevilja behörigheter.

![Resursgruppsnivå RBAC via Azure portal](./media/dns-protect-zones-recordsets/rbac1.png)

Behörigheter kan också vara [beviljas med Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>"
```

Motsvarande kommando är också [tillgängliga via Azure CLI](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --resourceGroup "<resource group name>"
```

### <a name="zone-level-rbac"></a>Zon nivå RBAC

Azure RBAC-regler kan användas till en prenumeration, en resursgrupp eller till en enskild resurs. När det gäller Azure DNS kan kan den här resursen vara en enskild DNS-zon eller till och med en enskild uppsättning av poster.

Anta exempelvis att resursgruppen *myzones* innehåller zonen *contoso.com* och en underdomänen *customers.contoso.com* i vilka CNAME poster skapas för varje Kund-ID.  Det konto som används för att hantera dessa CNAME-poster ska tilldelas behörighet att skapa poster i den *customers.contoso.com* zon endast, det bör inte ha åtkomst till de andra zonerna.

Zonen RBAC-behörighet kan beviljas via Azure portal.  Öppna **åtkomstkontroll (IAM)** för zonen, Välj **Lägg till**och välj sedan den **DNS-Zondeltagare** rollen och välj önskade användare eller grupper för att bevilja behörigheter.

![DNS-zon på RBAC via Azure portal](./media/dns-protect-zones-recordsets/rbac2.png)

Behörigheter kan också vara [beviljas med Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant 'DNS Zone Contributor' permissions to a specific zone
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>" -ResourceName "<zone name>" -ResourceType Microsoft.Network/DNSZones
```

Motsvarande kommando är också [tillgängliga via Azure CLI](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone
azure role assignment create --signInName <user email address> --roleName "DNS Zone Contributor" --resource-name <zone name> --resource-type Microsoft.Network/DNSZones --resource-group <resource group name>
```

### <a name="record-set-level-rbac"></a>Postuppsättningens nivå RBAC

Vi kan gå ett steg längre. Överväg att e-administratören för Contoso Corporation, som behöver åtkomst till MX och TXT-poster överst i zonen contoso.com.  Hon behöver inte åtkomst till alla andra MX och TXT-poster, eller till poster för alla andra typer.  Azure DNS kan du tilldela behörigheter på nivån uppsättningen av poster till exakt de poster som e-administratören behöver åtkomst till.  E-administratör beviljas exakt kontrollera nödvändiga, och kan inte göra några andra ändringar.

-Postuppsättning på RBAC-behörigheter kan konfigureras via Azure-portalen med hjälp av den **användare** knappen på sidan uppsättning av poster:

![Postuppsättningens nivå RBAC via Azure portal](./media/dns-protect-zones-recordsets/rbac3.png)

-Postuppsättning på RBAC-behörigheter kan också vara [beviljas med Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell
# Grant permissions to a specific record set
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -Scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

Motsvarande kommando är också [tillgängliga via Azure CLI](../role-based-access-control/role-assignments-cli.md):

```azurecli
# Grant permissions to a specific record set
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Anpassade roller

Den inbyggda rollen som deltagare för DNS-zon kan fullständig kontroll över en DNS-resurs. Du kan också skapa egna kundens Azure-roller, för att ge ännu mer detaljerad kontroll.

Studera igen exemplet där en CNAME-post registrerar i zonen *customers.contoso.com* skapas för varje kund-ID för Contoso Corporation.  Det konto som används för att hantera dessa CNAME-poster ska beviljas behörighet att hantera CNAME-poster.  Det är sedan det går inte att ändra poster av andra typer (till exempel ändra MX-poster) eller utföra åtgärder för zonen på servernivå, till exempel ta bort zonen.

I följande exempel visas en anpassad rolldefinition för att hantera CNAME-poster:

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

Egenskapen åtgärder definierar följande DNS-specifika behörigheter:

* `Microsoft.Network/dnsZones/CNAME/*` ger fullständig kontroll över CNAME-poster
* `Microsoft.Network/dnsZones/read` ger behörighet att läsa DNS-zoner, men inte ändra dem, så att du ser zonen där CNAME håller på att skapas.

De återstående åtgärderna kopieras från den [DNS-Zondeltagare inbyggd roll](../role-based-access-control/built-in-roles.md#dns-zone-contributor).

> [!NOTE]
> Med en anpassad RBAC-roll för att förhindra att ta bort uppsättningar av poster medan fortfarande att kunna uppdateras inte är en effektiv kontroll. Det förhindrar postuppsättningar från att tas bort, men den förhindrar inte att de kan ändras.  Tillåtna ändringar är att lägga till och ta bort poster från postuppsättningen, inklusive ta bort alla poster om du vill lämna en tom postuppsättning. Detta har samma effekt som tar bort posten från en DNS-matchning synvinkel.

Anpassade Rolldefinitioner kan för närvarande inte definieras via Azure portal. En anpassad roll baserat på den här rolldefinition kan skapas med hjälp av Azure PowerShell:

```azurepowershell
# Create new role definition based on input file
New-AzureRmRoleDefinition -InputFile <file path>
```

Det kan även skapas via Azure CLI:

```azurecli
# Create new role definition based on input file
azure role create -inputfile <file path>
```

Rollen kan sedan tilldelas på samma sätt som inbyggda roller som beskrivits tidigare i den här artikeln.

Mer information om hur du skapa, hantera och tilldela anpassade roller finns i [anpassade roller i Azure RBAC](../role-based-access-control/custom-roles.md).

## <a name="resource-locks"></a>Resurslås

Förutom RBAC stöder Azure Resource Manager en annan typ av säkerhetskontroll, nämligen möjligheten att låsa resurser. Där RBAC regler gör att du kan styra åtgärderna för specifika användare och grupper, resurslås tillämpas på resursen och börjar gälla för alla användare och roller. Mer information finns i [Låsa resurser med Azure Resource Manager](../azure-resource-manager/resource-group-lock-resources.md).

Det finns två typer av resurslås: **CanNotDelete** och **ReadOnly**. Dessa kan användas till en DNS-zon eller till en enskild uppsättning av poster.  I följande avsnitt beskrivs flera vanliga scenarier och ge stöd för dem med hjälp av resurslås.

### <a name="protecting-against-all-changes"></a>Skydda mot alla ändringar

Tillämpa ett ReadOnly-Lås för att förhindra att alla ändringar som görs i zonen.  Detta förhindrar att nya uppsättningar av poster som skapats och befintliga postuppsättningar från att ändras eller tas bort.

Zon på resurslås kan skapas via Azure portal.  Välj sidan DNS-zon **låser**och välj sedan **+ Lägg till**:

![Zon på resurslås via Azure portal](./media/dns-protect-zones-recordsets/locks1.png)

Zon på servernivå resource Lås kan även skapas via Azure PowerShell:

```azurepowershell
# Lock a DNS zone
New-AzureRmResourceLock -LockLevel <lock level> -LockName <lock name> -ResourceName <zone name> -ResourceType Microsoft.Network/DNSZones -ResourceGroupName <resource group name>
```

Konfigurera Azure resurslås stöds inte för närvarande via Azure CLI.

### <a name="protecting-individual-records"></a>Skydda enskilda poster

För att förhindra att en befintlig DNS-postuppsättning mot ändring gäller ett ReadOnly-Lås för postuppsättningen.

> [!NOTE]
> Tillämpa ett CanNotDelete-lås på en postuppsättning är inte en effektiv kontroll. Den förhindrar att posten från att tas bort, men den förhindrar inte att den kan ändras.  Tillåtna ändringar är att lägga till och ta bort poster från postuppsättningen, inklusive ta bort alla poster om du vill lämna en tom postuppsättning. Detta har samma effekt som tar bort posten från en DNS-matchning synvinkel.

Uppsättning av poster på resurslås för närvarande kan bara konfigureras med Azure PowerShell.  De stöds inte i Azure portal eller Azure CLI.

```azurepowershell
# Lock a DNS record set
New-AzureRmResourceLock -LockLevel <lock level> -LockName "<lock name>" -ResourceName "<zone name>/<record set name>" -ResourceType "Microsoft.Network/DNSZones/<record type>" -ResourceGroupName "<resource group name>"
```

### <a name="protecting-against-zone-deletion"></a>Skydd mot borttagning av zon

När en zon tas bort i Azure DNS, raderas också alla postuppsättningar i zonen.  Du kan inte ångra den här åtgärden.  Av misstag tar bort en zon för kritiska finns risken att ha en betydande inverkan.  Det är därför viktigt att skydda mot oavsiktlig zonen tas bort.

Tillämpa ett CanNotDelete-lås på en zon förhindrar att zonen tas bort.  Men eftersom Lås ärvs av underordnade resurser, den förhindrar också att alla postuppsättningar i zonen tas bort, vilket kan vara oönskade.  Dessutom enligt beskrivningen i noteringen ovan, är det också ineffektiv eftersom poster fortfarande kan tas bort från de befintliga uppsättningarna av poster.

Alternativt kan du överväga att tillämpa ett CanNotDelete-lås på en post i zonen, till exempel SOA-postuppsättning.  Eftersom zonen inte kan tas bort utan att även ta bort postuppsättningar, skyddar detta mot zon borttagning, men ändå låta postuppsättningar i zonen som ska ändras fritt. Om det görs ett försök att ta bort zonen, identifierar Azure Resource Manager Detta tar även bort SOA-postuppsättning och blockerar anropet eftersom SOA är låst.  Inga uppsättningar av poster tas bort.

Följande PowerShell-kommando skapar ett CanNotDelete-Lås mot SOA-posten för den angivna zonen:

```azurepowershell
# Protect against zone delete with CanNotDelete lock on the record set
New-AzureRmResourceLock -LockLevel CanNotDelete -LockName "<lock name>" -ResourceName "<zone name>/@" -ResourceType" Microsoft.Network/DNSZones/SOA" -ResourceGroupName "<resource group name>"
```

Ett annat sätt att förhindra oavsiktlig zon borttagning är genom att använda en anpassad roll så operatorn och tjänstkonton som används för att hantera zonerna har inte ta bort behörigheter för zonen. Du kan framtvinga en tvåstegsverifiering borttagning, första beviljad zon ta bort behörigheter (vid zon omfattning att förhindra att ta bort zonen fel) och andra för att ta bort zonen när du behöver ta bort en zon.

Den här andra tillvägagångssättet har fördelen att den fungerar för alla zoner som används av dessa konton utan att behöva komma ihåg att skapa någon Lås. Den har nackdelen att alla konton med borttagningsbehörighet för zonen, till exempel prenumerationsägare, fortfarande av misstag kan ta bort en zon för kritiska.

Det är möjligt att använda båda metoderna - resurslås och anpassade roller - samtidigt som en metod för skydd på djupet att DNS-zon.

## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du arbetar med RBAC [Kom igång med åtkomsthantering i Azure portal](../role-based-access-control/overview.md).
* Läs mer om hur du arbetar med resurslås [låsa resurser med Azure Resource Manager](../azure-resource-manager/resource-group-lock-resources.md).