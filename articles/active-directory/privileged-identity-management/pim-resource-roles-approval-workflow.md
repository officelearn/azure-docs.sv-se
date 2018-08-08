---
title: Arbetsflöde för godkännande för Azure-resurs-roller i Privileged Identity Management | Microsoft Docs
description: Beskriver hur godkännande arbetsflöde för Azure-resurser.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: dcb306bda8ef7d93314390bd9a90327ffdd14a0e
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39619645"
---
# <a name="approval-workflow-for-azure-resource-roles-in-privileged-identity-management"></a>Arbetsflöde för godkännande för Azure-resurs-roller i Privileged Identity Management

Med Godkännandearbetsflöde i Privileged Identity Management (PIM) för Azure-resursroller administratörer ytterligare skydda och begränsa åtkomsten till viktiga resurser. Det innebär att administratörer kan kräva godkännande för att aktivera rolltilldelningar. 

En resurs-hierarki är unika för Azure-resursroller. Den här hierarkin kan ärvda rolltilldelningar från en överordnad resurs-objektet nedåt till alla underordnade resurser i den överordnade behållaren. 

Till exempel: Bob, administratör av en resurs kan används PIM Alice som en berättigad medlem tilldelas rollen ägare i Contoso-prenumeration. Med denna tilldelning är Alice berättigade ägare av alla resource group behållare i Contoso-prenumeration. Alice är också berättigade ägare av alla resurser (t.ex. virtuella datorer) i varje resursgrupp för prenumerationen. 

Vi antar att det finns tre resursgrupper i Contoso-prenumerationen: Fabrikam Test, Fabrikam Dev och Fabrikam Prod. Var och en av de här resursgrupperna innehåller en enda virtuell dator.

PIM-inställningarna har konfigurerats för varje roll för en resurs. Till skillnad från tilldelningar, dessa inställningar ärvs inte och gäller enbart för resursrollen. [Läs mer om berättigade uppgifter och synlighet](pim-resource-roles-eligible-visibility.md).

Fortsättning på exemplet: Bob använder PIM vill kräva att alla medlemmar i rollen ägare av Contoso-prenumerationsgodkännande för begäran aktiveras. För att skydda resurserna i resursgruppen Fabrikam Prod, kräver Bob även godkännande för medlemmar i rollen ägare för den här resursen. Rollerna ägare i Fabrikam Test- och Fabrikam Dev kräver inte godkännande för aktivering.

När Alice begär aktivering av sin ägarrollen för Contoso-prenumeration, måste en godkännare Godkänn eller neka hennes begäran innan hon blir aktiv i rollen. Om Alice beslutar att [omfång hennes aktivering](pim-resource-roles-activate-your-roles.md#apply-just-enough-administration-practices) till Fabrikam Prod resursgrupp, en godkännare måste godkänna eller neka denna begäran för. Men om Alice bestämmer sig att definiera omfattningen av sin aktivering till ena eller båda Fabrikam Test- eller Fabrikam Dev godkännande krävs inte.

Arbetsflöde för godkännande kanske inte är nödvändigt för alla medlemmar i en roll. Tänk dig ett scenario där din organisation nyanställda flera kontrakt associates som underlättar utvecklingen av ett program som körs i en Azure-prenumeration. Som administratör av en resurs kan du vilja att anställda ska ha berättigad åtkomst utan godkännande krävs dock kontrakt associates måste begära godkännande. Om du vill konfigurera arbetsflöde för godkännande för endast kontrakt associates, kan du skapa en anpassad roll med samma behörigheter som rolltilldelningen för anställda. Du kan kräva godkännande för att aktivera den anpassa rollen. [Mer information om anpassade roller](pim-resource-roles-custom-role-policy.md).

Följ anvisningarna nedan om du vill konfigurera arbetsflöde för godkännande och ange vem som kan godkänna eller neka förfrågningar.

## <a name="require-approval-to-activate"></a>Kräv godkännande för att aktivera

1. Bläddra till PIM i Azure-portalen och välj en resurs i listan.

   ![”Azure-resurser” fönstret med en vald resurs](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

2. I den vänstra rutan, Välj **rollinställningar**.

3. Sök efter och välj en roll och välj sedan **redigera** att ändra inställningarna.

   ![Knappen ”Redigera” för operatörsrollen](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_view_settings.png)

4. I den **aktivering** väljer den **kräver godkännande för att aktivera** markerar du kryssrutan.

   ![”Aktivera”-delen av rollinställningar](media/azure-pim-resource-rbac/aadpim_rbac_settings_require_approval_checkbox.png)

## <a name="specify-approvers"></a>Ange godkännare

Klicka på **Välj godkännare** att öppna den **väljer en användare eller grupp** fönstret.

>[!NOTE]
>Du måste välja minst en användare eller grupp för att uppdatera inställningen. Det finns inga standard godkännare.

Resurs-administratörer kan lägga till valfri kombination av användare och grupper i listan över godkännare. 

![”Välj en användare eller grupp” fönstret med en användare som har valts](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_select_approvers.png)

## <a name="request-approval-to-activate"></a>Begär godkännande för att aktivera

Begära godkännande har ingen inverkan på den procedur som en medlem måste följa för att aktivera. [Gå igenom stegen för att aktivera en roll](pim-resource-roles-activate-your-roles.md).

Om en medlem begärde att aktivering av en roll som kräver godkännande och rollen inte längre behövs, kan medlemmen avbryta begäran i PIM.

Om du vill avbryta, bläddra till PIM och välj **Mina förfrågningar**. Leta reda på begäran och välj **Avbryt**.

![Fönstret ”Mina begäranden”](media/azure-pim-resource-rbac/aadpim_rbac_role_approval_request_pending.png)

## <a name="approve-or-deny-a-request"></a>Godkänn eller avvisa en begäran

För att godkänna eller avvisa en begäran, måste du vara medlem i listan med godkännare. 

1. I PIM, Välj **godkänna förfrågningar** från fliken på den vänstra menyn och leta upp begäran.

   ![Fönstret ”godkänna begäranden”](media/azure-pim-resource-rbac/aadpim_rbac_approve_requests_list.png)

2. Välj begäran, ange en motivering för beslutet och välj **Godkänn** eller **neka**. Begäran är sedan löst.

   ![Valda begäran med detaljerad information](media/azure-pim-resource-rbac/aadpim_rbac_approve_request_approved.png)

## <a name="workflow-notifications"></a>Arbetsflödesmeddelanden

Här följer några fakta om arbetsflödesmeddelanden:

- Alla medlemmar i listan över godkännare meddelas via e-post när en begäran för en roll väntar på sina granskningar. E-postmeddelanden innehåller en direktlänk till begäran, där godkännaren kan godkänna eller neka.
- Begäranden kan matchas med den första medlemmen i listan som godkänner eller nekar. 
- När en godkännare svarar på begäran, meddelas alla medlemmar i listan över godkännare för åtgärden. 
- Resurs-administratörer får ett meddelande när en godkända medlemmen aktiveras i deras roll. 

>[!Note]
>En anser att en godkända medlemmen inte får vara aktiva resurs-administratör kan ta bort aktiv rolltilldelning i PIM. Även om resursen administratörer meddelas inte om väntande begäranden om de inte är medlemmar i listan över godkännare, kan de visa och avbryta väntande begäranden för alla användare genom att visa väntande begäranden i PIM. 

## <a name="next-steps"></a>Nästa steg

[Använd PIM-inställningar för unika grupper av användare](pim-resource-roles-custom-role-policy.md)
