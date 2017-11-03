---
title: Skydda DNS-zoner och poster | Microsoft Docs
description: "Hur du skyddar DNS-zoner och postuppsättningar i Microsoft Azure DNS."
services: dns
documentationcenter: na
author: jtuliani
manager: carmonm
ms.assetid: 190e69eb-e820-4fc8-8e9a-baaf0b3fb74a
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/20/2016
ms.author: jonatul
ms.openlocfilehash: 0b7040d6273b3a6b85cd55850d596807226b87fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-protect-dns-zones-and-records"></a>Så här skyddar du DNS-zoner och poster

DNS-zoner och poster är viktiga resurser. Ta bort en DNS-zon eller bara en DNS-post kan resultera i en totala avbrott.  Det är därför viktigt att kritiska DNS-zoner och poster skyddas mot obehörig eller oavsiktliga ändringar.

Den här artikeln förklarar hur Azure DNS kan du skydda DNS-zoner och poster mot sådana ändringar.  Det använda två kraftfulla säkerhetsfunktioner som tillhandahålls av Azure Resource Manager: [rollbaserad åtkomstkontroll](../active-directory/role-based-access-control-what-is.md) och [resurslås](../azure-resource-manager/resource-group-lock-resources.md).

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

Azure rollbaserad åtkomstkontroll (RBAC) Aktivera detaljerad åtkomsthantering för Azure-användare, grupper och resurser. Med RBAC kan bevilja du exakt åtkomstnivå som användare måste utföra sitt arbete. Mer information om hur RBAC kan hjälpa dig att hantera åtkomsten finns [vad är rollbaserad åtkomstkontroll](../active-directory/role-based-access-control-what-is.md).

### <a name="the-dns-zone-contributor-role"></a>Rollen DNS-zonen-deltagare

DNS-zonen-deltagare roll är en inbyggd roll som tillhandahålls av Azure för att hantera DNS-resurser.  Tilldela DNS-zonen deltagarbehörighet till en användare eller grupp kan den gruppen att hantera DNS-resurser, men inte resurser för andra typer.

Anta exempelvis att resurs grupp 'myzones' innehåller fem zoner för Contoso Corporation. Tilldela DNS-administratören DNS-zonen-deltagare behörigheter till resursgruppen kan fullständig kontroll över dessa DNS-zoner. Den förhindrar tillståndsbeviljande onödiga, till exempel DNS-administratören inte kan skapa eller stoppa virtuella datorer.

Det enklaste sättet att tilldela behörigheter för RBAC [via Azure portal](../active-directory/role-based-access-control-configure.md).  Öppna 'Åtkomstkontroll (IAM)'-bladet för resursgruppen på ”Lägg till' och sedan Markera rollen DNS-zonen-deltagare och välj nödvändiga användare eller grupper för att bevilja behörighet.

![Resursgruppsnivå RBAC via Azure portal](./media/dns-protect-zones-recordsets/rbac1.png)

Behörigheter kan också vara [beviljas med hjälp av Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md):

```powershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>"
```

Kommandot motsvarande är också [tillgängliga via Azure CLI](../active-directory/role-based-access-control-manage-access-azure-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --resourceGroup "<resource group name>"
```

### <a name="zone-level-rbac"></a>Zonen nivå RBAC

Azure RBAC-regler kan användas till en prenumeration, resursgrupp eller till en enskild resurs. Vid Azure DNS kan resursen vara en enskild DNS-zon eller även en enskild uppsättning av poster.

Anta exempelvis att resurs grupp 'myzones' innehåller zonen contoso.com och en underdomänen 'customers.contoso.com' där CNAME-poster skapas för varje kundkonto.  Det konto som används för att hantera dessa CNAME-poster ska tilldelas behörigheter att skapa poster i zonen 'customers.contoso.com', den inte ska ha åtkomst till andra zoner.

Zonen RBAC-behörighet kan beviljas via Azure portal.  Öppna bladet 'Åtkomstkontroll (IAM)' för zonen, på ”Lägg till” och sedan välja rollen DNS-zonen-deltagare och välj nödvändiga användare eller grupper för att bevilja behörighet.

![DNS-zonen nivå RBAC via Azure portal](./media/dns-protect-zones-recordsets/rbac2.png)

Behörigheter kan också vara [beviljas med hjälp av Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md):

```powershell
# Grant 'DNS Zone Contributor' permissions to a specific zone
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>" -ResourceName "<zone name>" -ResourceType Microsoft.Network/DNSZones
```

Kommandot motsvarande är också [tillgängliga via Azure CLI](../active-directory/role-based-access-control-manage-access-azure-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone
azure role assignment create --signInName <user email address> --roleName "DNS Zone Contributor" --resource-name <zone name> --resource-type Microsoft.Network/DNSZones --resource-group <resource group name>
```

### <a name="record-set-level-rbac"></a>Postuppsättning nivå RBAC

Vi kan ta ytterligare ett steg. Överväg att e-administratören för Contoso Corporation, som behöver åtkomst till MX och TXT-poster på apex i zonen ”contoso.com”.  Hon behöver inte åtkomst till alla andra MX och TXT-poster, eller alla poster för alla andra typer.  Azure DNS kan du tilldela behörigheter på nivån postuppsättning exakt för poster som e-administratör behöver åtkomst till.  E-administratör beviljas exakt kontrollera hon måste och kan inte göra några andra ändringar.

Postuppsättningen behörighet på användarnivå RBAC kan konfigureras via Azure-portalen med hjälp av knappen ”användare” i bladet postuppsättning:

![Postuppsättning nivå RBAC via Azure portal](./media/dns-protect-zones-recordsets/rbac3.png)

Postuppsättningen behörighet på användarnivå RBAC kan också vara [beviljas med hjälp av Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md):

```powershell
# Grant permissions to a specific record set
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -Scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

Kommandot motsvarande är också [tillgängliga via Azure CLI](../active-directory/role-based-access-control-manage-access-azure-cli.md):

```azurecli
# Grant permissions to a specific record set
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Anpassade roller

Inbyggda DNS-zonen-deltagare-rollen ger fullständig kontroll över DNS-resursposter. Du kan också skapa egna kunden Azure roller, som ger även mer detaljerad kontroll.

Studera igen exemplet som en CNAME-post i zonen 'customers.contoso.com' skapas för varje kundkonto för Contoso Corporation.  Det konto som används för att hantera dessa skapa CNAME-poster ska beviljas behörighet att hantera CNAME-poster.  Det är sedan det går inte att ändra poster för andra typer (till exempel ändra MX-poster) eller utföra åtgärder på zonnivå, till exempel ta bort zonen.

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

* `Microsoft.Network/dnsZones/CNAME/*`ger fullständig kontroll över CNAME-poster
* `Microsoft.Network/dnsZones/read`ger behörighet att läsa DNS-zoner, men inte ändra dem, så att du ser zonen där CNAME håller på att skapas.

De återstående åtgärderna kopieras från den [DNS-zonen inbyggda deltagarrollen](../active-directory/role-based-access-built-in-roles.md#dns-zone-contributor).

> [!NOTE]
> Använda en anpassad RBAC-roll för att förhindra att ta bort postuppsättningar fortfarande så att de kan uppdateras inte är en effektiv kontroll. Det förhindrar postuppsättningar tas bort, men den förhindrar inte dem som ska ändras.  Tillåtna ändringar lägga till och ta bort poster från uppsättningen av poster, inklusive ta bort alla poster om du vill lämna en 'empty-postuppsättning. Detta har samma effekt som tar bort posten från en DNS-matchning synvinkel.

Anpassade Rolldefinitioner kan för närvarande inte definieras via Azure portal. En anpassad roll som baseras på den här rolldefinition kan skapas med hjälp av Azure PowerShell:

```powershell
# Create new role definition based on input file
New-AzureRmRoleDefinition -InputFile <file path>
```

Det kan även skapas via Azure CLI:

```azurecli
# Create new role definition based on input file
azure role create -inputfile <file path>
```

Rollen kan sedan tilldelas på samma sätt som inbyggda roller, enligt beskrivningen tidigare i den här artikeln.

Mer information om hur du skapa, hantera och tilldela anpassade roller finns [anpassade roller i Azure RBAC](../active-directory/role-based-access-control-custom-roles.md).

## <a name="resource-locks"></a>Resurslås

Förutom RBAC stöder en annan typ av säkerhetskontroll, nämligen möjligheten att ”låsa” resurser i Azure Resource Manager. Där RBAC regler kan du styra åtgärder för specifika användare och grupper, resurslås som tillämpas på resursen och börjar gälla för alla användare och roller. Mer information finns i [Låsa resurser med Azure Resource Manager](../azure-resource-manager/resource-group-lock-resources.md).

Det finns två typer av resurslås: **DoNotDelete** och **ReadOnly**. Dessa kan användas till en DNS-zon eller till en enskild uppsättning av poster.  I följande avsnitt beskrivs några vanliga scenarier och hur du stöder dem med hjälp av resource Lås.

### <a name="protecting-against-all-changes"></a>Skydd mot alla ändringar

För att förhindra att alla ändringar som gäller en ReadOnly-Lås för zonen.  Detta förhindrar att nya postuppsättningar skapas och befintliga postuppsättningar från att ändras eller tas bort.

Zonen nivån resurslås kan skapas via Azure portal.  I bladet DNS-zonen klickar du på Lås, Lägg sedan 'till':

![Zonen nivån resurslås via Azure portal](./media/dns-protect-zones-recordsets/locks1.png)

På zonnivå resurs Lås kan även skapas via Azure PowerShell:

```powershell
# Lock a DNS zone
New-AzureRmResourceLock -LockLevel <lock level> -LockName <lock name> -ResourceName <zone name> -ResourceType Microsoft.Network/DNSZones -ResourceGroupName <resource group name>
```

Konfigurera Azure-resurslås stöds inte för närvarande via Azure CLI.

### <a name="protecting-individual-records"></a>Skydda enskilda poster

För att förhindra att en befintlig DNS-post mot ändring gäller en ReadOnly-Lås för uppsättningen av poster.

> [!NOTE]
> Tillämpa DoNotDelete lås på en postuppsättning är inte en effektiv kontroll. Det förhindrar att posten tas bort, men den förhindrar inte att den kan ändras.  Tillåtna ändringar lägga till och ta bort poster från uppsättningen av poster, inklusive ta bort alla poster om du vill lämna en 'empty-postuppsättning. Detta har samma effekt som tar bort posten från en DNS-matchning synvinkel.

Postuppsättning nivån resurslås för närvarande kan bara konfigureras med Azure PowerShell.  De stöds inte i Azure-portalen eller Azure CLI.

```powershell
# Lock a DNS record set
New-AzureRmResourceLock -LockLevel <lock level> -LockName "<lock name>" -ResourceName "<zone name>/<record set name>" -ResourceType "Microsoft.Network/DNSZones/<record type>" -ResourceGroupName "<resource group name>"
```

### <a name="protecting-against-zone-deletion"></a>Skydd mot zon borttagning

När en zon tas bort i Azure DNS, raderas även alla uppsättningar av poster i zonen.  Den här åtgärden kan inte ångras.  Oavsiktlig borttagning av en kritisk zon har möjlighet att ha en betydande marknadsfördelar.  Därför är det mycket viktigt att skydda mot oavsiktlig zonen borttagning.

Tillämpa DoNotDelete lås på en zon förhindrar att zonen tas bort.  Men eftersom Lås ärvs av underordnade resurser den förhindrar också att alla uppsättningar av poster i zonen tas bort, vilket kan vara önskvärt.  Dessutom enligt beskrivningen i noteringen ovan, är det också ineffektiv eftersom poster fortfarande kan tas bort från de befintliga postuppsättningar.

Överväg att använda ett DoNotDelete Lås till en post i zonen, till exempel uppsättningen SOA-poster som ett alternativ.  Eftersom zonen inte kan tas bort utan att även ta bort postuppsättningar, skyddar detta mot zon borttagning, men ändå låta postuppsättningar i zonen ska ändras fritt. Om ett försök görs att ta bort zonen identifierar Azure Resource Manager detta skulle även att ta bort uppsättningen av SOA-poster och blockerar anropet eftersom SOA är låst.  Inga postuppsättningar tas bort.

Följande PowerShell-kommando skapar ett DoNotDelete Lås mot SOA-post på den angivna zonen:

```powershell
# Protect against zone delete with DoNotDelete lock on the record set
New-AzureRmResourceLock -LockLevel DoNotDelete -LockName "<lock name>" -ResourceName "<zone name>/@" -ResourceType" Microsoft.Network/DNSZones/SOA" -ResourceGroupName "<resource group name>"
```

Ett annat sätt att förhindra oavsiktlig zonen borttagning är med hjälp av en anpassad roll så operatorn och tjänstkonton som används för att hantera zoner har inte behörighet zonen. Du kan tillämpa en tvåstegsverifiering delete, första beviljande zonen behörighet att ta bort (definitionsområdet zonen, så att du tar bort fel zon) och andra för att ta bort zonen när du behöver ta bort en zon.

Den här andra tillvägagångssättet har fördelen att det fungerar för alla zoner som har åtkomst till dessa konton utan att behöva komma ihåg att skapa någon Lås. Den har nackdelen att alla konton med behörighet att zonen ta bort, till exempel prenumerationsägaren, fortfarande av misstag kan ta bort en zon för kritiska.

Det är möjligt att använda båda metoderna - resurslås och anpassade roller - samtidigt som en metod för skydd på djupet för DNS-zonen skydd.

## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du arbetar med RBAC [Kom igång med åtkomsthantering i Azure portal](../active-directory/role-based-access-control-what-is.md).
* Mer information om hur du arbetar med resurslås finns [låsa resurser med Azure Resource Manager](../azure-resource-manager/resource-group-lock-resources.md).

