---
title: Lägg till ägare och användare i Azure DevTest Labs | Microsoft Docs
description: Lägg till ägare och användare i Azure DevTest Labs med Azure-portalen eller PowerShell
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60311427"
---
# <a name="add-owners-and-users-in-azure-devtest-labs"></a>Lägg till ägare och användare i Azure DevTest Labs
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/How-to-set-security-in-your-DevTest-Lab/player]
> 
> 

Åtkomst i Azure DevTest Labs styrs av [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md). Med RBAC kan du hålla isär uppgifter i ditt team i *roller* där du kan bevilja endast mängden åtkomst till användare att utföra sitt arbete. Tre av dessa RBAC-roller är *ägare*, *DevTest Labs-användare*, och *deltagare*. I den här artikeln får du lära dig vilka åtgärder kan utföras i var och en av tre huvudsakliga RBAC-roller. Därifrån kan dig du hur du lägger till användare till ett labb - både via portalen och via ett PowerShell-skript och hur du lägger till användare på prenumerationsnivån.

## <a name="actions-that-can-be-performed-in-each-role"></a>Åtgärder som kan utföras i varje roll
Det finns tre huvudsakliga roller som du tilldelar en användare:

* Ägare
* DevTest Labs-användare
* Deltagare

I följande tabell visar de åtgärder som kan utföras av användare i var och en av dessa roller:

| **Åtgärder som användarna i den här rollen kan utföra** | **DevTest Labs-användare** | **Ägare** | **Deltagare** |
| --- | --- | --- | --- |
| **Labbuppgifter** | | | |
| Lägga till användare i ett labb |Nej |Ja |Nej |
| Uppdatera inställningarna för kostnad |Nej |Ja |Ja |
| **Grundläggande uppgifter för virtuella datorer** | | | |
| Lägga till och ta bort anpassade avbildningar |Nej |Ja |Ja |
| Lägga till, uppdatera och ta bort formler |Ja |Ja |Ja |
| Lista över tillåtna Azure Marketplace-avbildningar |Nej |Ja |Ja |
| **Uppgifter för virtuella datorer** | | | |
| Skapa VM:ar |Ja |Ja |Ja |
| Starta, stoppa och ta bort virtuella datorer |Endast virtuella datorer som skapas av användaren |Ja |Ja |
| Uppdatera principer för virtuella datorer |Nej |Ja |Ja |
| Lägg till/ta bort datadiskar till/från virtuella datorer |Endast virtuella datorer som skapas av användaren |Ja |Ja |
| **Artefakten uppgifter** | | | |
| Lägga till och ta bort artefakt databaser |Nej |Ja |Ja |
| Tillämpa artefakter |Ja |Ja |Ja |

> [!NOTE]
> När en användare skapar en virtuell dator, som användaren tilldelas automatiskt till den **ägare** rollen för den skapade virtuella datorn.
> 
> 

## <a name="add-an-owner-or-user-at-the-lab-level"></a>Lägg till ägare eller användare på nivån lab
Ägare och användare kan läggas på nivån lab via Azure portal. En användare kan vara en extern användare med en giltig [Microsoft-konto (MSA)](devtest-lab-faq.md#what-is-a-microsoft-account).
Följande steg vägleder dig genom processen att lägga till ägare eller användare i ett labb i Azure DevTest Labs:

1. Logga in på [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Välj **alla tjänster**, och välj sedan **DevTest Labs** i listan.
3. Listan över labbar, Välj önskade labbet.
4. På den labb-bladet och välj **konfiguration och principer**. 
5. På den **konfiguration och principer** väljer **åtkomstkontroll (IAM)** på menyn till vänster. 
6. Välj **Lägg till rolltilldelning** i verktygsfältet för att lägga till en användare till en roll.
1. I den **Lägg till behörigheter** fönstret göra följande: 
    1. Välj en roll (till exempel: DevTest Labs-användare). Avsnittet [åtgärder som kan utföras i varje roll](#actions-that-can-be-performed-in-each-role) visar de olika åtgärder som kan utföras av användare i rollerna ägare, DevTest-användare och deltagare.
    2. Välj användaren som ska läggas till rollen. 
    3. Välj **Spara**. 
11. När du återgår till den **användare** bladet användaren har lagts till.  

## <a name="add-an-external-user-to-a-lab-using-powershell"></a>Lägga till en extern användare i ett labb med hjälp av PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Förutom att lägga till användare i Azure-portalen, kan du lägga till en extern användare labbet med ett PowerShell-skript. I följande exempel, ändra parametervärden under den **värden för att ändra** kommentar.
Du kan hämta den `subscriptionId`, `labResourceGroup`, och `labName` värdena från bladet labb i Azure-portalen.

> [!NOTE]
> Exempelskriptet förutsätter att den angivna användaren har lagts till som en gäst till Active Directory och kommer att misslyckas om så inte är fallet. Om du vill lägga till en användare inte i Active Directory till ett labb använder du Azure-portalen för att tilldela användaren till en roll, enligt beskrivningen i avsnittet [Lägg till ägare eller användare på nivån lab](#add-an-owner-or-user-at-the-lab-level).   
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

## <a name="add-an-owner-or-user-at-the-subscription-level"></a>Lägg till ägare eller användaren på prenumerationsnivån
Azure-behörigheter har spridits från överordnad omfattning till underordnade omfång i Azure. Ägare till en Azure-prenumeration som innehåller labs är därför automatiskt ägare till dessa labs. De äger de virtuella datorerna och andra resurser som skapats av den labbanvändare och tjänsten Azure DevTest Labs. 

Du kan lägga till ytterligare ägare till ett labb via den labb-bladet i den [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040). Har lagts till ägarens omfånget för administration är dock smalare än den prenumerationsägaren omfång. Till exempel har har lagts till ägare inte fullständig åtkomst till vissa av de resurser som skapats i prenumerationen med DevTest Labs-tjänsten. 

Följ dessa steg för att lägga till en ägare till en Azure-prenumeration:

1. Logga in på [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Välj **alla tjänster**, och välj sedan **prenumerationer** i listan.
3. Välj den önskade prenumerationen.
4. Välj **åtkomst** ikon. 
   
    ![Användare med](./media/devtest-lab-add-devtest-user/access-users.png)
5. På den **användare** bladet väljer **Lägg till**.
   
    ![Lägga till användare](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)
6. På den **Välj en roll** bladet välj **ägare**.
7. På den **lägga till användare** bladet anger du e-postadress eller namnet på den användare som du vill lägga till som ägare. Om användaren inte kan hittas kan få du ett felmeddelande som förklarar problemet. Om användaren hittas, användaren i listan under den **användaren** textrutan.
8. Välj hitta användarnamnet.
9. Välj **Välj**.
10. Välj **OK** att Stäng den **Lägg till åtkomst** bladet.
11. När du återgår till den **användare** bladet användaren har lagts till som ägare. Den här användaren är nu en ägare av alla labs som skapats under den här prenumerationen och är därför utföra ägare aktiviteter. 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

