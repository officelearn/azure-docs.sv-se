---
title: Lägg till ägare och användare i Azure DevTest Labs | Microsoft Docs
description: Lägg till ägare och användare i Azure DevTest Labs med Azure-portalen eller PowerShell
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: ''
ms.assetid: 4f51d9a5-2702-45f0-a2d5-a3635b58c416
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: v-craic
ms.openlocfilehash: d0fdd8441143e2d2eb49399a10dc12d38fbf6823
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="add-owners-and-users-in-azure-devtest-labs"></a>Lägg till ägare och användare i Azure DevTest Labs
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/How-to-set-security-in-your-DevTest-Lab/player]
> 
> 

Åtkomst i Azure DevTest Labs styrs av [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md). Med RBAC kan du särskilja uppgifter i din grupp i *roller* där du beviljar bara mängden åtkomst för användare att utföra sitt arbete. Tre av dessa RBAC-roller är *ägare*, *DevTest Labs användaren*, och *deltagare*. I den här artikeln får du lära dig vilka åtgärder kan utföras i var och en av de tre huvudsakliga RBAC-rollerna. Därifrån kan dig du hur du lägger till användare i ett labb - både via portalen och via ett PowerShell-skript och hur du lägger till användare på prenumerationsnivån.

## <a name="actions-that-can-be-performed-in-each-role"></a>Åtgärder som kan utföras i varje roll
Det finns tre huvudsakliga roller som du kan tilldela en användare:

* Ägare
* DevTest Labs-användare
* Deltagare

I följande tabell visas de åtgärder som kan utföras av användare i dessa roller:

| **Åtgärder som användarna i den här rollen kan utföra** | **DevTest Labs användare** | **Ägare** | **Deltagare** |
| --- | --- | --- | --- |
| **Uppgifter för övningen** | | | |
| Lägga till användare i ett labb |Nej |Ja |Nej |
| Uppdatera inställningarna för kostnad |Nej |Ja |Ja |
| **Grundläggande uppgifter för Virtuella datorer** | | | |
| Lägga till och ta bort anpassade avbildningar |Nej |Ja |Ja |
| Lägga till, uppdatera och ta bort formler |Ja |Ja |Ja |
| Godkända Azure Marketplace-bilder |Nej |Ja |Ja |
| **Uppgifter för Virtuella datorer** | | | |
| Skapa VM:ar |Ja |Ja |Ja |
| Starta, stoppa och ta bort virtuella datorer |Endast virtuella datorer som skapats av användaren |Ja |Ja |
| Uppdatera VM-principer |Nej |Ja |Ja |
| Lägg till/ta bort datadiskar till och från virtuella datorer |Endast virtuella datorer som skapats av användaren |Ja |Ja |
| **Artefakt uppgifter** | | | |
| Lägga till och ta bort artefakt databaser |Nej |Ja |Ja |
| Tillämpa artefakter |Ja |Ja |Ja |

> [!NOTE]
> När en användare skapar en virtuell dator, som användaren tilldelas automatiskt den **ägare** rollen för den skapade virtuella datorn.
> 
> 

## <a name="add-an-owner-or-user-at-the-lab-level"></a>Lägg till en ägare eller användare på nivån labb
Ägare och användare kan läggas på nivån lab via Azure portal. Detta omfattar även externa användare med ett giltigt [Microsoft-konto (MSA)](devtest-lab-faq.md#what-is-a-microsoft-account).
Följande steg hjälper dig att lägga till en ägare eller användare i ett labb i Azure DevTest Labs:

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Välj **alla tjänster**, och välj sedan **DevTest Labs** från listan.
3. Lista över labs, Välj önskade labbet.
4. På den testmiljön bladet välj **Configuration**. 
5. På den **Configuration** bladet väljer **användare**.
6. På den **användare** bladet väljer **+ Lägg till**.
   
    ![Lägga till användare](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
7. På den **Välj en roll** bladet Välj önskad roll. Avsnittet [åtgärder som kan utföras i varje roll](#actions-that-can-be-performed-in-each-role) visas de olika åtgärder som kan utföras av användare i rollerna ägare, DevTest användare och deltagare.
8. På den **lägga till användare** bladet anger du e-postadress eller namnet på den användare som du vill lägga till i rollen som du angav. Om användaren inte hittas, förklaras problemet med ett felmeddelande. Om användaren hittas, visas den användaren och valt. 
9. Välj **Välj**.
10. Välj **OK** att stänga den **Lägg till åtkomst** bladet.
11. När du kommer tillbaka till den **användare** bladet användaren har lagts till.  

## <a name="add-an-external-user-to-a-lab-using-powershell"></a>Lägg till en extern användare i ett testlabb med hjälp av PowerShell
Förutom att lägga till användare i Azure-portalen kan du lägga till en extern användare ditt labb använder ett PowerShell-skript. I följande exempel bara ändra parametervärden under den **värden för att ändra** kommentar.
Du kan hämta den `subscriptionId`, `labResourceGroup`, och `labName` värdena från bladet labb i Azure-portalen.

> [!NOTE]
> Exempelskriptet förutsätter att den angivna användaren har lagts till som gäst till Active Directory och kommer att misslyckas om det inte är fallet. Använd Azure-portalen tilldela användaren till en roll enligt beskrivningen i avsnittet om du vill lägga till en användare inte i Active Directory i ett labb [lägga till en ägare eller användare på nivån lab](#add-an-owner-or-user-at-the-lab-level).   
> 
> 

    # Add an external user in DevTest Labs user role to a lab
    # Ensure that guest users can be added to the Azure Active directory:
    # https://azure.microsoft.com/en-us/documentation/articles/active-directory-create-users/#set-guest-user-access-policies

    # Values to change
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource name here>"
    $labName = "<Enter lab name here>"
    $userDisplayName = "<Enter user's display name here>"

    # Log into your Azure account
    Login-AzureRmAccount

    # Select the Azure subscription that contains the lab. 
    # This step is optional if you have only one subscription.
    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    # Retrieve the user object
    $adObject = Get-AzureRmADUser -SearchString $userDisplayName

    # Create the role assignment. 
    $labId = ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    New-AzureRmRoleAssignment -ObjectId $adObject.Id -RoleDefinitionName 'DevTest Labs User' -Scope $labId

## <a name="add-an-owner-or-user-at-the-subscription-level"></a>Lägg till en ägare eller användare på prenumerationsnivån
Azure behörigheter sprids från överordnade omfattningen till underordnade omfattningen i Azure. Ägarna av en Azure-prenumeration som innehåller övningar är därför automatiskt ägare till dessa övningar. De egna virtuella datorer och andra resurser som skapats av användare i labbet och tjänsten Azure DevTest Labs. 

Du kan lägga till ytterligare ägare till ett labb via den testmiljön bladet i den [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040). Tillagda ägaren omfattning administration är dock mer restriktiva än den prenumerationsägaren omfång. Till exempel har inte lagts till ägare fullständig åtkomst till vissa av de resurser som skapas av tjänsten DevTest Labs i prenumerationen. 

Följ dessa steg för att lägga till en ägare till en Azure-prenumeration:

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Välj **alla tjänster**, och välj sedan **prenumerationer** från listan.
3. Välj den önskade prenumerationen.
4. Välj **åtkomst** ikon. 
   
    ![-Användare](./media/devtest-lab-add-devtest-user/access-users.png)
5. På den **användare** bladet väljer **Lägg till**.
   
    ![Lägga till användare](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
6. På den **Välj en roll** bladet välj **ägare**.
7. På den **lägga till användare** bladet anger du e-postadress eller namnet på den användare som du vill lägga till som ägare. Om användaren inte hittas kan få du ett felmeddelande som förklarar problemet. Om användaren hittas, som visas under den **användaren** textruta.
8. Välj hitta användarnamnet.
9. Välj **Välj**.
10. Välj **OK** att stänga den **Lägg till åtkomst** bladet.
11. När du kommer tillbaka till den **användare** bladet användaren har lagts till som en ägare. Den här användaren är nu en ägare av alla labb som skapats under den här prenumerationen och därför att kunna utföra uppgifter för ägare. 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

