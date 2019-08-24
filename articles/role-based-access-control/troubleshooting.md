---
title: Felsöka RBAC för Azure-resurser | Microsoft Docs
description: Felsök problem med rollbaserad åtkomst kontroll (RBAC) för Azure-resurser.
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
ms.date: 08/22/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: c3ca873cb4dd49d77ba818b3d05c3aa41e89276f
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982726"
---
# <a name="troubleshoot-rbac-for-azure-resources"></a>Felsöka RBAC för Azure-resurser

Den här artikeln besvarar vanliga frågor om rollbaserad åtkomst kontroll (RBAC) för Azure-resurser, så att du vet vad som ska förväntas när du använder rollerna i Azure Portal och kan felsöka åtkomst problem.

## <a name="problems-with-rbac-role-assignments"></a>Problem med RBAC-rolltilldelningar

- Om du inte kan lägga till en roll tilldelning i Azure Portal på **åtkomst kontroll (IAM)** eftersom alternativet **Lägg** > till**roll tilldelning** för tillägg är inaktiverat eller om du får behörighets felet "klienten med objekt-ID: t har inte behörighet att utföra åtgärden, kontrol lera att du är inloggad med en användare som har `Microsoft.Authorization/roleAssignments/write` tilldelats en roll med behörighet som [ägare](built-in-roles.md#owner) eller administratör för [användar åtkomst](built-in-roles.md#user-access-administrator) i den omfattning som du försöker tilldela rollen.
- Om du får fel meddelandet "det går inte att skapa fler roll tilldelningar (kod: RoleAssignmentLimitExceeded)” när du försöker tilldela en roll kan du försöka att minska antalet rolltilldelningar genom att tilldela roller till grupper i stället. Azure har stöd för upp till **2000** rolltilldelningar per prenumeration.

## <a name="problems-with-custom-roles"></a>Problem med anpassade roller

- Om du behöver anvisningar för hur du skapar en anpassad roll kan du läsa självstudierna för den anpassade rollen med hjälp av [Azure PowerShell](tutorial-custom-role-powershell.md) eller [Azure CLI](tutorial-custom-role-cli.md).
- Om du inte kan uppdatera en befintlig anpassad roll kontrollerar du att du är inloggad med en användare som har `Microsoft.Authorization/roleDefinition/write` tilldelats en roll med behörighet som [ägare](built-in-roles.md#owner) eller administratör för [användar åtkomst](built-in-roles.md#user-access-administrator).
- Om du inte lyckas ta bort en anpassad roll och får felmeddelandet om att det finns befintliga rolltilldelningar som refererar till rollen (kod: RoleDefinitionHasAssignments), så finns det rolltilldelningar som fortfarande använder den anpassade rollen. Ta bort dessa rolltilldelningar och försök att ta bort den anpassade rollen igen.
- Om du får felmeddelandet ”Det högsta tillåtna antalet rolldefinitioner har överskridits. Det går inte att skapa fler roll definitioner (kod: RoleDefinitionLimitExceeded) "när du försöker skapa en ny anpassad roll tar du bort alla anpassade roller som inte används. Azure har stöd för upp till **5000** anpassade roller i en klient organisation. (För särskilda moln, till exempel Azure Government, Azure Tyskland och Azure Kina 21Vianet är gränsen 2000 anpassade roller.)
- Om du får ett fel som liknar "klienten har behörighet att utföra åtgärden" Microsoft. Authorization/roleDefinitions/Write "i omfånget '/Subscriptions/{subscriptionId} ', gick det inte att hitta den länkade prenumerationen" när du försöker uppdatera en anpassad roll kontrollerar du om ett eller flera [tilldelnings bara scope](role-definitions.md#assignablescopes) har tagits bort i klienten. Om omfånget har tagits bort ska du skapa en supportbegäran eftersom det inte finns någon självbetjäningslösning tillgänglig just nu.

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>Återställa RBAC när prenumerationer flyttas mellan klienter

- Om du behöver steg för att överföra en prenumeration till en annan Azure AD-klient läser du [överföra ägarskapet för en Azure-prenumeration till ett annat konto](../billing/billing-subscription-transfer.md).
- Om du överför en prenumeration till en annan Azure AD-klient tas alla rolltilldelningar bort permanent från den Azure AD-klient som är källan och migreras inte till den Azure AD-klient som är målet. Du måste återskapa rolltilldelningarna i målklienten. Du måste också manuellt återskapa hanterade identiteter för Azure-resurser. Mer information finns i [vanliga frågor och svar och kända problem med hanterade identiteter](../active-directory/managed-identities-azure-resources/known-issues.md).
- Om du är global administratör i Azure AD och du inte har åtkomst till en prenumeration när den har flyttats mellan klienter, använder du **åtkomst hantering för Azure-resurser** för att tillfälligt [öka åtkomsten](elevate-access-global-admin.md) till prenumerationen.

## <a name="issues-with-service-admins-or-co-admins"></a>Problem med tjänstadministratörer eller medadministratörer

- Om du har problem med tjänst administratören eller medadministratörer kan du läsa [Lägg till eller ändra Azure-prenumerations administratörer](../billing/billing-add-change-azure-subscription-administrator.md) och [klassiska prenumerations administratörs roller, Azure RBAC-roller och Azure AD-administratörskonsolen](rbac-and-directory-admin-roles.md).

## <a name="access-denied-or-permission-errors"></a>Åtkomst nekad eller behörighets fel

- Om du får behörighetsfelet ”Klienten med objekt-ID har inte behörighet att utföra åtgärden över område (kod: AuthorizationFailed)” när du försöker skapa en resurs kan du kontrollera att du är inloggad med en användare med en roll som har skrivbehörighet till resursen i det valda omfånget. Om du vill hantera virtuella datorer i en resursgrupp ska du till exempel ha rollen [Virtuell datordeltagare](built-in-roles.md#virtual-machine-contributor) på den resursgruppen (eller ett överordnat område). En lista med behörigheter för alla inbyggda roller finns i [Inbyggda roller för Azure-resurser](built-in-roles.md).
- Om du får behörighets fel "du har inte behörighet att skapa en supportbegäran" när du försöker skapa eller uppdatera ett support ärende, kontrollerar du att du är inloggad med en användare som har `Microsoft.Support/supportTickets/write` behörighet, till exempel [ Support förfrågan deltagare](built-in-roles.md#support-request-contributor).

## <a name="role-assignments-without-a-security-principal"></a>Roll tilldelningar utan säkerhets objekt

När du listar roll tilldelningarna med Azure PowerShell kan du se tilldelningar med en tom `DisplayName` och en `ObjectType` uppsättning till okänd. [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) returnerar till exempel en roll tilldelning som liknar följande:

```azurepowershell
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

När du listar roll tilldelningarna med Azure CLI kan du också se tilldelningar med en tom `principalName`. Till exempel returnerar [AZ roll tilldelnings lista](/cli/azure/role/assignment#az-role-assignment-list) en roll tilldelning som liknar följande:

```azurecli
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

Roll tilldelningarna sker när du tilldelar en roll till ett säkerhets objekt (användare, grupp, tjänstens huvud namn eller hanterad identitet) och senare tar bort säkerhets objekt. Roll tilldelningarna visas inte i Azure Portal och det är inte ett problem att lämna dem. Men om du vill kan du ta bort roll tilldelningarna.

Ta bort roll tilldelningarna genom att använda borttagnings kommandona [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) eller [AZ roll tilldelning](/cli/azure/role/assignment#az-role-assignment-delete) .

Om du i PowerShell försöker ta bort roll tilldelningarna med objekt-ID: t och roll definitions namnet, och mer än en roll tilldelning matchar dina parametrar, får du fel meddelandet: "Den angivna informationen mappas inte till en roll tilldelning". Följande visar ett exempel på fel meddelandet:

```Example
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

Om du får det här fel meddelandet ser du till att du även `-Scope` anger `-ResourceGroupName` parametrarna eller.

```Example
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="rbac-changes-are-not-being-detected"></a>RBAC-ändringar identifieras inte

Azure Resource Manager cachelagrar ibland konfigurationer och data för att förbättra prestanda. När du skapar eller tar bort roll tilldelningar kan det ta upp till 30 minuter innan ändringarna börjar gälla. Om du använder Azure Portal, Azure PowerShell eller Azure CLI kan du framtvinga en uppdatering av roll tilldelnings ändringarna genom att logga ut och logga in. Om du gör roll tilldelnings ändringar med REST API-anrop kan du framtvinga en uppdatering genom att uppdatera åtkomst-token.

## <a name="web-app-features-that-require-write-access"></a>Webb program funktioner som kräver skriv åtkomst

Om du beviljar en användare skrivskyddad åtkomst till en enda webbapp inaktive ras vissa funktioner som du kanske inte förväntar dig. Följande hanterings funktioner kräver **Skriv** åtkomst till en webbapp (antingen deltagare eller ägare) och är inte tillgängliga i alla skrivskyddade scenarier.

* Kommandon (t. ex. Start, stopp osv.)
* Ändra inställningar som allmän konfiguration, skalnings inställningar, säkerhets kopierings inställningar och övervaknings inställningar
* Åtkomst till autentiseringsuppgifter för publicering och andra hemligheter som appinställningar och anslutnings strängar
* Direktuppspelningsloggar
* Konfiguration av diagnostiska loggar
* Konsol (kommando tolk)
* Aktiva och nya distributioner (för lokal git-kontinuerlig distribution)
* Uppskattade utgifter
* Webbtest
* Virtuellt nätverk (endast synligt för en läsare om ett virtuellt nätverk tidigare har kon figurer ATS av en användare med Skriv behörighet).

Om du inte kan komma åt någon av dessa paneler måste du be administratören om deltagar åtkomst till webbappen.

## <a name="web-app-resources-that-require-write-access"></a>Webb program resurser som kräver skriv åtkomst

Webbappar är komplicerade genom förekomst av några olika resurser som spelas upp. Här är en typisk resurs grupp med ett par webbplatser:

![Resurs grupp för webb program](./media/troubleshooting/website-resource-model.png)

Det innebär att om du beviljar någon åtkomst till webbappen är många av funktionerna på bladet webbplats i Azure Portal inaktiverade.

De här objekten kräver **Skriv** åtkomst till den **App Service plan** som motsvarar din webbplats:  

* Visa Web Apps pris nivå (kostnads fri eller standard)  
* Skalnings konfiguration (antal instanser, storlek på virtuell dator, inställningar för automatisk skalning)  
* Kvoter (lagring, bandbredd, CPU)  

De här objekten kräver **Skriv** åtkomst till hela **resurs gruppen** som innehåller din webbplats:  

* SSL-certifikat och bindningar (SSL-certifikat kan delas mellan platser i samma resurs grupp och Geo-plats)  
* Varningsregler  
* Inställningar för autoskalning  
* Application Insights-komponenter  
* Webbtest  

## <a name="virtual-machine-features-that-require-write-access"></a>Funktioner för virtuella datorer som kräver skriv åtkomst

På liknande sätt som Web Apps kräver vissa funktioner på bladet virtuell dator skriv åtkomst till den virtuella datorn eller till andra resurser i resurs gruppen.

Virtuella datorer är relaterade till domän namn, virtuella nätverk, lagrings konton och varnings regler.

De här objekten kräver **Skriv** åtkomst till den **virtuella datorn**:

* Slutpunkter  
* IP-adresser  
* Diskar  
* Tillägg  

Dessa kräver **Skriv** behörighet till både den **virtuella datorn**och **resurs gruppen** (tillsammans med domän namnet) som den finns i:  

* Tillgänglighetsuppsättning  
* Belastningsutjämnad uppsättning  
* Varningsregler  

Om du inte kan komma åt någon av dessa paneler kan du be administratören om deltagar åtkomst till resurs gruppen.

## <a name="azure-functions-and-write-access"></a>Azure Functions-och skriv åtkomst

Vissa funktioner i [Azure Functions](../azure-functions/functions-overview.md) kräver skriv åtkomst. Om en användare till exempel har tilldelats rollen [läsare](built-in-roles.md#reader) kan de inte Visa funktionerna i en Function-app. Portalen visar **(ingen åtkomst)** .

![Function-appar ingen åtkomst](./media/troubleshooting/functionapps-noaccess.png)

En läsare kan klicka på fliken **plattforms funktioner** och sedan klicka på **alla inställningar** för att visa vissa inställningar som är relaterade till en Function-app (liknar en webbapp), men de kan inte ändra någon av dessa inställningar. Du behöver deltagar rollen för att få åtkomst [](built-in-roles.md#contributor) till dessa funktioner.

## <a name="next-steps"></a>Nästa steg
* [Hantera åtkomst till Azure-resurser med hjälp av RBAC och Azure-portalen](role-assignments-portal.md)
* [Visa aktivitets loggar för RBAC-ändringar till Azure-resurser](change-history-report.md)

