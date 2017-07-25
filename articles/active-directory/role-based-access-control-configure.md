---
title: "Rollbaserad åtkomstkontroll i Azure Portal | Microsoft Docs"
description: "Kom igång med åtkomsthantering med rollbaserad åtkomstkontroll på Azure Portal. Använd rolltilldelningar för att tilldela behörigheter i dina resurser."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/17/2017
ms.author: kgremban
ms.reviewer: rqureshi
ms.translationtype: HT
ms.sourcegitcommit: c3ea7cfba9fbf1064e2bd58344a7a00dc81eb148
ms.openlocfilehash: aff943e797da93e95563c3de3f064dbbfc30a384
ms.contentlocale: sv-se
ms.lasthandoff: 07/19/2017

---
# <a name="use-role-based-access-control-to-manage-access-to-your-azure-subscription-resources"></a>Använda rollbaserad åtkomstkontroll för att hantera åtkomsten till dina Azure-prenumerationsresurser
> [!div class="op_single_selector"]
> * [Hantera åtkomst enligt användare eller grupp](role-based-access-control-manage-assignments.md)
> * [Hantera åtkomst enligt resurs](role-based-access-control-configure.md)

Rollbaserad åtkomstkontroll (RBAC) i Azure ger tillgång till ingående åtkomsthantering för Azure. Med RBAC kan du bevilja exakt den åtkomstnivå som användarna behöver för att kunna utföra sitt arbete. Den här artikeln hjälper dig att komma igång med RBAC på Azure-portalen. Mer information om hur RBAC kan hjälpa dig att hantera åtkomsten finns i [Vad är rollbaserad åtkomstkontroll?](role-based-access-control-what-is.md)

Du kan bevilja upp till 2 000 rolltilldelningar inom varje prenumeration. 

## <a name="view-access"></a>Visa åtkomst
Du kan se vem som har åtkomst till en resurs, resursgrupp eller prenumeration från dess huvudblad på [Azure-portalen](https://portal.azure.com). Vi vill till exempel se vem som har åtkomst till en av våra resursgrupper:

1. Välj **Resursgrupper** i navigeringsfältet till vänster.  
    ![Resursgrupper - ikon](./media/role-based-access-control-configure/resourcegroups_icon.png)
2. Välj namnet på resursgruppen från bladet **Resursgrupper**.
3. Välj **Åtkomstkontroll (IAM)** från den vänstra menyn.  
4. Åtkomstkontroll-bladet listar alla användare, grupper och program som har getts åtkomst till resursgruppen.  
   
    ![Skärmbild av bladet Användare – ärvd eller tilldelad åtkomst](./media/role-based-access-control-configure/view-access.png)

Observera att vissa roller är begränsade till **resursen** medan andra **ärver** den från ett annat omfång. Åtkomst tilldelas antingen specifikt till resursgruppen eller ärvs från en tilldelning till den överordnade prenumerationen.

> [!NOTE]
> Administratörer och medadministratörer för klassiska prenumerationer betraktas som ägare till prenumerationen i den nya RBAC-modellen.

## <a name="add-access"></a>Lägga till åtkomst
Du beviljar åtkomst inifrån resursen, resursgruppen eller prenumerationen som rolltilldelningen omfattar.

1. Välj **Lägg till** på åtkomstkontroll-bladet.  
2. Välj den roll som du vill tilldela från bladet **Välj en roll**.
3. Välj den användare, den grupp eller det program i katalogen som du vill bevilja åtkomst till. Du kan söka i katalogen med visningsnamn, e-postadresser och objektidentifierare.  
   
    ![Skärmbild av bladet Lägg till användare – sök](./media/role-based-access-control-configure/grant-access2.png)
4. Skapa tilldelningen genom att välja **OK**. Du kan följa förloppet på popup-menyn **Lägger till användare**.  
    ![Lägga till användarförloppsindikatorn - skärmbild](./media/role-based-access-control-configure/addinguser_popup.png)

När du har lagt till en rolltilldelning visas den på bladet **Användare**.

## <a name="remove-access"></a>Ta bort åtkomst
1. Håll markören över namnet på tilldelningen som du vill ta bort. En kryssruta visas bredvid namnet.
2. Använd kryssrutorna på för att välja en eller flera rolltilldelningar.
2. Välj **Ta bort**.  
3. Bekräfta borttagningen genom att välja **Ja**.

Ärvda tilldelningar kan inte tas bort. Om du behöver ta bort en ärvd tilldelning så måste du göra det i samma omfång som där rolltilldelningen skapades. I kolumnen **Omfång** bredvid **Ärvd** finns en länk som leder till resurserna där den här rollen har tilldelats. Gå till resursen som visas där om du vill ta bort rolltilldelningen.

![Skärmbild av bladet Användare – ärvd åtkomst inaktiverar knappen Ta bort](./media/role-based-access-control-configure/remove-access2.png)

## <a name="other-tools-to-manage-access"></a>Andra verktyg för att hantera åtkomst
Du kan tilldela roller och hantera åtkomst med Azure RBAC-kommandon i andra verktyg än Azure-portalen.  Följ länkarna om du vill lära dig mer om kraven och komma igång med Azure RBAC-kommandona.

* [Azure PowerShell](role-based-access-control-manage-access-powershell.md)
* [Azure kommandoradsgränssnittet](role-based-access-control-manage-access-azure-cli.md)
* [REST-API](role-based-access-control-manage-access-rest.md)

## <a name="next-steps"></a>Nästa steg
* [Skapa en rapport över åtkomständringshistorik](role-based-access-control-access-change-history-report.md)
* Gå till [Inbyggda RBAC-roller](role-based-access-built-in-roles.md)
* Definiera egna [anpassade roller i Azure RBAC](role-based-access-control-custom-roles.md)


