---
title: Lägg till ägare och användare i Azure DevTest Labs| Microsoft-dokument
description: Lägga till ägare och användare i Azure DevTest Labs med azure-portalen eller PowerShell
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 4f51d9a5-2702-45f0-a2d5-a3635b58c416
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: spelluru
ms.openlocfilehash: a9426c20ae23fd3dad4cdba25590ff2eac271896
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284283"
---
# <a name="add-owners-and-users-in-azure-devtest-labs"></a>Lägga till ägare och användare i Azure DevTest Labs
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/How-to-set-security-in-your-DevTest-Lab/player]
> 
> 

Åtkomst i Azure DevTest Labs styrs av [RBAC (Azure Role-Based Access Control).](../role-based-access-control/overview.md) Med hjälp av RBAC kan du segregera uppgifter inom ditt team i *roller* där du bara ger användarna den mängd åtkomst som krävs för att utföra sina jobb. Tre av dessa RBAC-roller är *Ägare,* *DevTest Labs User*och *Contributor*. I den här artikeln får du lära dig vilka åtgärder som kan utföras i var och en av de tre viktigaste RBAC-rollerna. Därifrån får du lära dig hur du lägger till användare i ett labb - både via portalen och via ett PowerShell-skript, och hur du lägger till användare på prenumerationsnivå.

## <a name="actions-that-can-be-performed-in-each-role"></a>Åtgärder som kan utföras i varje roll
Det finns tre huvudroller som du kan tilldela en användare:

* Ägare
* DevTest Labs Användare
* Deltagare

I följande tabell visas de åtgärder som kan utföras av användare i var och en av dessa roller:

| **Åtgärder som användare i den här rollen kan utföra** | **DevTest Labs Användare** | **Ägare** | **Deltagare** |
| --- | --- | --- | --- |
| **Labbuppgifter** | | | |
| Lägga till användare i ett labb |Inga |Ja |Inga |
| Uppdatera kostnadsinställningar |Inga |Ja |Ja |
| **Vm-basuppgifter** | | | |
| Lägga till och ta bort anpassade bilder |Inga |Ja |Ja |
| Lägga till, uppdatera och ta bort formler |Ja |Ja |Ja |
| Vitlista Azure Marketplace-avbildningar |Inga |Ja |Ja |
| **VM-uppgifter** | | | |
| Skapa VM:ar |Ja |Ja |Ja |
| Starta, stoppa och ta bort virtuella datorer |Endast virtuella datorer som skapats av användaren |Ja |Ja |
| Uppdatera principer för virtuella datorer |Inga |Ja |Ja |
| Lägga till/ta bort datadiskar till/från virtuella datorer |Endast virtuella datorer som skapats av användaren |Ja |Ja |
| **Artefaktuppgifter** | | | |
| Lägga till och ta bort artefaktdatabaser |Inga |Ja |Ja |
| Använda artefakter |Ja |Ja |Ja |

> [!NOTE]
> När en användare skapar en virtuell dator tilldelas den användaren automatiskt ägarrollen **för** den skapade virtuella datorn.
> 
> 

## <a name="add-an-owner-or-user-at-the-lab-level"></a>Lägga till en ägare eller användare på labbnivå
Ägare och användare kan läggas till på labbnivå via Azure-portalen. En användare kan vara en extern användare med ett giltigt [Microsoft-konto (MSA)](devtest-lab-faq.md#what-is-a-microsoft-account).
Följande steg hjälper dig att lägga till en ägare eller användare i ett labb i Azure DevTest Labs:

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Välj **Alla tjänster**och välj sedan **DevTest Labs** i listan.
3. Välj önskat labb i listan över labb.
4. På labbets blad väljer du **Konfiguration och principer**. 
5. På sidan **Konfiguration och principer** väljer du **Åtkomstkontroll (IAM)** på menyn till vänster. 
6. Välj **Lägg till rolltilldelning** i verktygsfältet om du vill lägga till en användare i en roll.
1. Gör följande i fönstret **Lägg till behörigheter:** 
    1. Välj en roll (till exempel DevTest Labs User). I avsnittet [Åtgärder som kan utföras i varje roll](#actions-that-can-be-performed-in-each-role) visas de olika åtgärder som kan utföras av användare i rollerna Ägare, DevTest-användare och Deltagare.
    2. Välj den användare som ska läggas till i rollen. 
    3. Välj **Spara**. 
11. När du återvänder till bladet **Användare** har användaren lagts till.  

## <a name="add-an-external-user-to-a-lab-using-powershell"></a>Lägga till en extern användare i ett labb med PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Förutom att lägga till användare i Azure-portalen kan du lägga till en extern användare i labbet med ett PowerShell-skript. I följande exempel ändrar du parametervärdena under kommentaren **Värden som ska ändras.**
Du kan `subscriptionId`hämta `labResourceGroup`, `labName` och värden från labbbladet i Azure-portalen.

> [!NOTE]
> Exempelskriptet förutsätter att den angivna användaren har lagts till som gäst i Active Directory och misslyckas om så inte är fallet. Om du vill lägga till en användare som inte är i Active Directory i ett labb använder du Azure-portalen för att tilldela användaren till en roll som visas i [avsnittet, Lägg till en ägare eller användare på labbnivå](#add-an-owner-or-user-at-the-lab-level).   
> 
> 

    # Add an external user in DevTest Labs user role to a lab
    # Ensure that guest users can be added to the Azure Active directory:
    # https://azure.microsoft.com/documentation/articles/active-directory-create-users/#set-guest-user-access-policies

    # Values to change
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource name here>"
    $labName = "<Enter lab name here>"
    $userDisplayName = "<Enter user's display name here>"

    # Log into your Azure account
    Connect-AzAccount

    # Select the Azure subscription that contains the lab. 
    # This step is optional if you have only one subscription.
    Select-AzSubscription -SubscriptionId $subscriptionId

    # Retrieve the user object
    $adObject = Get-AzADUser -SearchString $userDisplayName

    # Create the role assignment. 
    $labId = ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    New-AzRoleAssignment -ObjectId $adObject.Id -RoleDefinitionName 'DevTest Labs User' -Scope $labId

## <a name="add-an-owner-or-user-at-the-subscription-level"></a>Lägga till en ägare eller användare på prenumerationsnivå
Azure-behörigheter sprids från överordnat scope till underordnade scope i Azure. Därför är ägare av en Azure-prenumeration som innehåller labb automatiskt ägare av dessa labb. De äger också de virtuella datorerna och andra resurser som skapats av labbets användare och Azure DevTest Labs-tjänsten. 

Du kan lägga till ytterligare ägare i ett labb via labbets blad i [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040). Den tillagda ägarens administrationsområde är dock smalare än prenumerationsägarens omfattning. De tillagda ägarna har till exempel inte fullständig åtkomst till vissa av de resurser som skapas i prenumerationen av devtest labs-tjänsten. 

Så här lägger du till en ägare i en Azure-prenumeration:

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Välj **Alla tjänster**och välj sedan **Prenumerationer** i listan.
3. Välj önskad prenumeration.
4. Välj **Access** access-ikonen. 
   
    ![Komma åt användare](./media/devtest-lab-add-devtest-user/access-users.png)
5. På bladet **Användare** väljer du **Lägg till**.
   
    ![Lägga till användare](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
6. Välj **Ägare**på bladet **Välj en roll** .
7. På bladet **Lägg till användare** anger du e-postadressen eller namnet på den användare som du vill lägga till som ägare. Om användaren inte kan hittas får du ett felmeddelande som förklarar problemet. Om användaren hittas visas användaren under textrutan **Användare.**
8. Välj det lokaliserade användarnamnet.
9. Välj **Välj**.
10. Välj **OK** för att stänga **bladet Lägg till åtkomst.**
11. När du återvänder till bladet **Användare** har användaren lagts till som ägare. Den här användaren är nu ägare till alla labb som skapats under den här prenumerationen och kan därmed utföra ägaruppgifter. 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

