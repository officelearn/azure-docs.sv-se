---
title: Felsöka Azure RBAC
description: Felsöka problem med Azure-rollbaserad åtkomstkontroll (Azure RBAC).
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 09d5b7a126a1b8832bfe40e2e25dd4000d5d9155
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548294"
---
# <a name="troubleshoot-azure-rbac"></a>Felsöka Azure RBAC

Den här artikeln besvarar några vanliga frågor om Azure-rollbaserad åtkomstkontroll (Azure RBAC), så att du vet vad som väntar när du använder rollerna och kan felsöka åtkomstproblem.

## <a name="azure-role-assignments-limit"></a>Begränsa Azure-rolltilldelningar

Azure har stöd för upp till **2000** rolltilldelningar per prenumeration. Om felmeddelandet "Inga fler rolltilldelningar kan skapas (kod: RoleAssignmentLimitExceed)" när du försöker tilldela en roll försöker du minska antalet rolltilldelningar i prenumerationen.

> [!NOTE]
> **Gränsen för 2000-rolltilldelningar** per prenumeration är fast och kan inte ökas.

Om du närmar dig den här gränsen kan du minska antalet rolltilldelningar på följande sätt:

- Lägg till användare i grupper och tilldela roller till grupperna i stället. 
- Kombinera flera inbyggda roller med en anpassad roll. 
- Gör vanliga rolltilldelningar med ett högre omfång, till exempel prenumeration eller hanteringsgrupp.
- Om du har Azure AD Premium P2 gör du rolltilldelningar kvalificerade i [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md) i stället för permanent tilldelade. 
- Lägg till ytterligare en prenumeration. 

Om du vill ha antalet rolltilldelningar kan du visa [diagrammet på sidan Åtkomstkontroll (IAM)](role-assignments-list-portal.md#list-number-of-role-assignments) i Azure-portalen. Du kan också använda följande Azure PowerShell-kommandon:

```azurepowershell
$scope = "/subscriptions/<subscriptionId>"
$ras = Get-AzRoleAssignment -Scope $scope | Where-Object {$_.scope.StartsWith($scope)}
$ras.Count
```

## <a name="problems-with-azure-role-assignments"></a>Problem med Azure-rolltilldelningar

- Om du inte kan lägga till en rolltilldelning i Azure-portalen på **Åtkomstkontroll (IAM)** eftersom alternativet **Lägg** > **till rolltilldelning** är inaktiverat eller för att du får behörighetsfelet "Klienten med `Microsoft.Authorization/roleAssignments/write` objekt-ID har inte behörighet att utföra åtgärd", kontrollerar du att du för närvarande är inloggad med en användare som har tilldelats en roll som har behörighet som [Ägare](built-in-roles.md#owner) eller Administratör för [användaråtkomst](built-in-roles.md#user-access-administrator) i det scope som du försöker tilldela rollen.

## <a name="problems-with-custom-roles"></a>Problem med anpassade roller

- Om du behöver steg för hur du skapar en anpassad roll läser du självstudierna för anpassade roller med [Azure-portalen](custom-roles-portal.md) (för närvarande i förhandsversion), [Azure PowerShell](tutorial-custom-role-powershell.md)eller [Azure CLI](tutorial-custom-role-cli.md).
- Om du inte kan uppdatera en befintlig anpassad roll kontrollerar du att du för närvarande `Microsoft.Authorization/roleDefinition/write` är inloggad med en användare som har tilldelats en roll som har behörighet som [Ägare](built-in-roles.md#owner) eller [Administratör för användaråtkomst](built-in-roles.md#user-access-administrator).
- Om du inte kan ta bort en anpassad roll och får felmeddelandet "Det finns befintliga rolltilldelningar som refererar till roll (kod: RollDefinitionHasAssignments)", finns det rolltilldelningar som fortfarande använder den anpassade rollen. Ta bort dessa rolltilldelningar och försök att ta bort den anpassade rollen igen.
- Om du får felmeddelandet ”Det högsta tillåtna antalet rolldefinitioner har överskridits. Inga fler rolldefinitioner kan skapas (kod: RoleDefinitionLimitExceed)" när du försöker skapa en ny anpassad roll tar du bort alla anpassade roller som inte används. Azure har stöd för upp till **5 000** anpassade roller i en katalog. (För Azure Germany och Azure China 21Vianet är gränsen 2000 anpassade roller.)
- Om du får ett fel som liknar "Klienten har behörighet att utföra åtgärden 'Microsoft.Authorization/roleDefinitions/write' på scope'/subscriptions/{subscriptionid}', men den länkade prenumerationen hittades inte" när du försöker uppdatera en anpassad roll, kontrollerar du om ett eller flera [assignable scope](role-definitions.md#assignablescopes) har tagits bort i katalogen. Om omfånget har tagits bort ska du skapa en supportbegäran eftersom det inte finns någon självbetjäningslösning tillgänglig just nu.

## <a name="custom-roles-and-management-groups"></a>Anpassade roller och hanteringsgrupper

- Du kan bara definiera `AssignableScopes` en hanteringsgrupp i en anpassad roll. Att lägga till `AssignableScopes` en hanteringsgrupp i är för närvarande i förhandsversion.
- Anpassade roller `DataActions` med kan inte tilldelas i hanteringsgruppens scope.
- Azure Resource Manager validerar inte hanteringsgruppens existens i rolldefinitionens assignable scope.
- Mer information om anpassade roller och hanteringsgrupper finns i [Ordna dina resurser med Azure-hanteringsgrupper](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment).

## <a name="transferring-a-subscription-to-a-different-directory"></a>Överföra en prenumeration till en annan katalog

- Om du behöver steg för hur du överför en prenumeration till en annan Azure AD-katalog läser du [Överföra ägarskap för en Azure-prenumeration till ett annat konto](../cost-management-billing/manage/billing-subscription-transfer.md).
- Om du överför en prenumeration till en annan Azure AD-katalog tas alla rolltilldelningar **bort permanent** från källkatalogen Azure AD och migreras inte till azure AD-katalogen för mål. Du måste återskapa dina rolltilldelningar i målkatalogen. Du måste också manuellt återskapa hanterade identiteter för Azure-resurser. Mer information finns i [Vanliga frågor och andra frågor och kända problem med hanterade identiteter](../active-directory/managed-identities-azure-resources/known-issues.md).
- Om du är global Azure AD-administratör och inte har åtkomst till en prenumeration efter att den har överförts mellan kataloger använder du **åtkomsthanteringen för Azure-resurser som** tillfälligt [höjer din åtkomst](elevate-access-global-admin.md) för att få åtkomst till prenumerationen.

## <a name="issues-with-service-admins-or-co-admins"></a>Problem med tjänstadministratörer eller medadministratörer

- Om du har problem med tjänstadministratör eller medadministratörer läser du [Lägga till eller ändra Azure-prenumerationsadministratörer](../cost-management-billing/manage/add-change-subscription-administrator.md) och klassiska [administratörsroller för prenumerationer, Azure-roller och Azure AD-administratörsroller](rbac-and-directory-admin-roles.md).

## <a name="access-denied-or-permission-errors"></a>Åtkomst nekad eller behörighetsfel

- Om du får behörighetsfelet "Klienten med objekt-ID har inte behörighet att utföra åtgärd över scope (kod: AuthorizationFailed)" när du försöker skapa en resurs kontrollerar du att du för närvarande är inloggad med en användare som har tilldelats en roll som har skrivbehörighet till resursen i det valda scopet. Om du vill hantera virtuella datorer i en resursgrupp ska du till exempel ha rollen [Virtuell datordeltagare](built-in-roles.md#virtual-machine-contributor) på den resursgruppen (eller ett överordnat område). En lista med behörigheter för alla inbyggda roller finns i [Inbyggda roller för Azure-resurser](built-in-roles.md).
- Om du får behörighetsfelet "Du har inte behörighet att skapa en supportbegäran" när du försöker skapa eller uppdatera en supportbiljett kontrollerar `Microsoft.Support/supportTickets/write` du att du för närvarande är inloggad med en användare som har tilldelats en roll som har behörighet, till exempel [Supportbegäran.](built-in-roles.md#support-request-contributor)

## <a name="role-assignments-with-unknown-security-principal"></a>Rolltilldelningar med Okänt säkerhetsobjekt

Om du tilldelar en roll till ett säkerhetsobjekt (användare, grupp, tjänsthuvudnamn eller hanterad identitet) och sedan senare tar bort säkerhetsobjektet utan att ta bort rolltilldelningen, visas säkerhetsobjekttypen för rolltilldelningen som **Okänd**. Följande skärmbild visar ett exempel i Azure-portalen. Säkerhetsnamnet visas som **Identitet borttagen** och **identitet finns inte längre**. 

![Resursgrupp för webbapp](./media/troubleshooting/unknown-security-principal.png)

Om du listar den här rolltilldelningen `DisplayName` med `ObjectType` Azure PowerShell visas en tom och en uppsättning till Okänd. [Get-AzRoleAssignment returnerar](/powershell/module/az.resources/get-azroleassignment) till exempel en rolltilldelning som liknar följande:

```
RoleAssignmentId   : /subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111
DisplayName        :
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 33333333-3333-3333-3333-333333333333
ObjectType         : Unknown
CanDelegate        : False
```

Om du listar den här rolltilldelningen med `principalName`Azure CLI visas en tom . Till exempel returnerar [a-rolltilldelningslistan](/cli/azure/role/assignment#az-role-assignment-list) en rolltilldelning som liknar följande:

```
{
    "canDelegate": null,
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222",
    "name": "22222222-2222-2222-2222-222222222222",
    "principalId": "33333333-3333-3333-3333-333333333333",
    "principalName": "",
    "roleDefinitionId": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
    "roleDefinitionName": "Storage Blob Data Contributor",
    "scope": "/subscriptions/11111111-1111-1111-1111-111111111111",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

Det är inte ett problem att lämna dessa rolltilldelningar, men du kan ta bort dem med hjälp av steg som liknar andra rolltilldelningar. Information om hur du tar bort rolltilldelningar finns i [Azure portal,](role-assignments-portal.md#remove-a-role-assignment) [Azure PowerShell](role-assignments-powershell.md#remove-a-role-assignment)eller [Azure CLI](role-assignments-cli.md#remove-a-role-assignment)

Om du i PowerShell försöker ta bort rolltilldelningarna med objekt-ID och rolldefinitionsnamn, och mer än en rolltilldelning matchar dina parametrar, får du felmeddelandet: "Den angivna informationen mappas inte till en rolltilldelning". Följande visar ett exempel på felmeddelandet:

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

Om du får det här felmeddelandet `-Scope` kontrollerar du att du också anger parametrarna eller. `-ResourceGroupName`

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="role-assignment-changes-are-not-being-detected"></a>Ändringar av rolltilldelning identifieras inte

Azure Resource Manager cachelagrar ibland konfigurationer och data för att förbättra prestanda. När du lägger till eller tar bort rolltilldelningar kan det ta upp till 30 minuter innan ändringarna börjar gälla. Om du använder Azure-portalen, Azure PowerShell eller Azure CLI kan du tvinga fram en uppdatering av dina rolltilldelningsändringar genom att logga ut och logga in. Om du gör rolltilldelningsändringar med REST API-anrop kan du tvinga fram en uppdatering genom att uppdatera åtkomsttoken.

Om du lägger till eller tar bort en `DataActions`rolltilldelning i hanteringsgruppens omfattning och rollen har, kanske åtkomsten på dataplanet inte uppdateras på flera timmar. Detta gäller endast hanteringsgruppomfånget och dataplanet.

## <a name="web-app-features-that-require-write-access"></a>Webbappfunktioner som kräver skrivåtkomst

Om du ger en användare skrivskyddad åtkomst till en enda webbapp inaktiveras vissa funktioner som du kanske inte förväntar dig. Följande hanteringsfunktioner kräver **skrivåtkomst** till en webbapp (antingen Deltagare eller Ägare) och är inte tillgängliga i något skrivskyddat scenario.

* Kommandon (som start, stopp, etc.)
* Ändra inställningar som allmän konfiguration, skalningsinställningar, inställningar för säkerhetskopiering och övervakningsinställningar
* Komma åt publiceringsuppgifter och andra hemligheter som appinställningar och anslutningssträngar
* Strömmande loggar
* Konfiguration av diagnostikloggar
* Konsol (kommandotolk)
* Aktiva och senaste distributioner (för lokal kontinuerlig git-distribution)
* Beräknade utgifter
* Webbtester
* Virtuellt nätverk (endast synligt för en läsare om ett virtuellt nätverk tidigare har konfigurerats av en användare med skrivåtkomst).

Om du inte kan komma åt någon av dessa paneler måste du be administratören om deltagaråtkomst till webbappen.

## <a name="web-app-resources-that-require-write-access"></a>Webbappresurser som kräver skrivåtkomst

Webbappar kompliceras av förekomsten av några olika resurser som samspelar. Här är en typisk resursgrupp med ett par webbplatser:

![Resursgrupp för webbapp](./media/troubleshooting/website-resource-model.png)

Om du ger någon åtkomst till bara webbappen inaktiveras därför mycket av funktionerna på webbplatsbladet i Azure-portalen.

Dessa objekt kräver **skrivåtkomst** till **App Service-planen** som motsvarar din webbplats:  

* Visa webbappens prisnivå (ledig eller standard)  
* Skalningskonfiguration (antal instanser, storlek på virtuella datorer, inställningar för automatisk skalning)  
* Kvoter (lagring, bandbredd, CPU)  

Dessa objekt kräver **skrivåtkomst** till hela **resursgruppen** som innehåller din webbplats:  

* TLS/SSL-certifikat och bindningar (TLS/SSL-certifikat kan delas mellan platser i samma resursgrupp och geografisk plats)  
* Varningsregler  
* Inställningar för automatisk skalning  
* Komponenter för programinsikter  
* Webbtester  

## <a name="virtual-machine-features-that-require-write-access"></a>Funktioner för virtuella datorer som kräver skrivåtkomst

I likhet med webbappar kräver vissa funktioner på bladet för virtuella datorer skrivåtkomst till den virtuella datorn eller till andra resurser i resursgruppen.

Virtuella datorer är relaterade till domännamn, virtuella nätverk, lagringskonton och varningsregler.

Dessa objekt kräver **skrivåtkomst** till den **virtuella datorn:**

* Slutpunkter  
* IP-adresser  
* Diskar  
* Tillägg  

Dessa kräver **skrivåtkomst** till både den **virtuella datorn**och **resursgruppen** (tillsammans med domännamnet) som den finns i:  

* Tillgänglighetsuppsättning  
* Belastningsbalanserad uppsättning  
* Varningsregler  

Om du inte kan komma åt någon av dessa paneler ber du administratören om deltagaråtkomst till resursgruppen.

## <a name="azure-functions-and-write-access"></a>Azure-funktioner och skrivåtkomst

Vissa funktioner [i Azure Functions](../azure-functions/functions-overview.md) kräver skrivåtkomst. Om en användare till exempel tilldelas rollen [Läsare](built-in-roles.md#reader) kan de inte visa funktionerna i en funktionsapp. Portalen visas **(ingen åtkomst)**.

![Funktionsappar ingen åtkomst](./media/troubleshooting/functionapps-noaccess.png)

En läsare kan klicka på fliken **Plattformsfunktioner** och sedan klicka på **Alla inställningar** för att visa vissa inställningar som är relaterade till en funktionsapp (liknande en webbapp), men de kan inte ändra någon av dessa inställningar. För att komma åt dessa funktioner behöver du rollen [Deltagare.](built-in-roles.md#contributor)

## <a name="next-steps"></a>Nästa steg

- [Felsöka för gästanvändare](role-assignments-external-users.md#troubleshoot)
- [Hantera åtkomst till Azure-resurser med hjälp av RBAC och Azure-portalen](role-assignments-portal.md)
- [Visa aktivitetsloggar för RBAC-ändringar i Azure-resurser](change-history-report.md)
