---
title: Arbetsflöde för godkännande för Azure-resursroller i Privileged Identity Management | Microsoft Docs
description: Beskriver godkännande arbetsflödesprocessen för Azure-resurser.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: de15a02e706ec7f7b4cff0af303ea30fc87b8f34
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233775"
---
# <a name="approval-workflow-for-azure-resource-roles-in-privileged-identity-management"></a>Arbetsflöde för godkännande för Azure-resursroller i Privileged Identity Management

Med arbetsflödet för godkännande i Privileged Identity Management (PIM) för Azure-resursroller, kan administratörer ytterligare skydda eller begränsa åtkomsten till viktiga resurser. Administratörer kan att kräva godkännande för att aktivera rolltilldelningar. 

Konceptet för en resurs hierarki är unikt i Azure-resurs-roller. Den här hierarkin kan ärvda rolltilldelningar från en överordnad resursobjektet nedåt till alla underordnade resurser i den överordnade behållaren. 

Till exempel: Bob, administratör av en resurs kan används PIM Alice som en berättigad medlem tilldelas rollen som ägare i Contoso-prenumeration. Med den här tilldelningen äger Alice berättigade alla resurs grupp behållare i Contoso-prenumerationen. Alice är också en berättigad ägare av alla resurser (t.ex. virtuella datorer) i varje resursgrupp för prenumerationen. 

Antar vi att det finns tre resursgrupper i Contoso-prenumeration: Fabrikam Test och Fabrikam Dev Fabrikam produktprenumeration. Var och en av de här resursgrupperna innehåller en enskild virtuell dator.

PIM-inställningarna är konfigurerade för varje roll för en resurs. Till skillnad från tilldelningar inställningarna ärvs inte och gäller enbart för resursrollen. [Läs mer om tillgängliga tilldelningar och synlighet](pim-resource-roles-eligible-visibility.md).

Fortsättning på exemplet: Bob använder PIM vill kräva att alla medlemmar i rollen som ägare av Contoso prenumeration begäran om godkännande som ska aktiveras. Om du vill skydda resurser i resursgruppen Fabrikam Prod kräver Bob även godkännande för medlemmar i rollen som ägare av den här resursen. Rollerna ägare i Fabrikam Test- och Fabrikam Dev kräver inte godkännande för aktivering.

När Alice begär aktivering av sitt ägarrollen för Contoso-prenumeration, måste godkännare godkänna eller neka sin begäran innan hon blir aktiv i rollen. Om Alice beslutar att [omfång sin aktivering](pim-resource-roles-activate-your-roles.md#apply-just-enough-administration-practices) till resursgruppen Fabrikam Prod godkännare måste godkänna eller avvisa denna begäran för. Men om Alice beslutar att omfatta sin aktivering antingen eller båda Fabrikam Test eller Fabrikam Dev, godkännande krävs inte.

Arbetsflöde för godkännande kanske inte är nödvändiga för alla medlemmar i en roll. Överväg ett scenario där din organisation anlitar flera kontrakt har associerats till hjälp med utvecklingen av ett program som körs i en Azure-prenumeration. Som administratör av en resurs kan du vilja att anställda i berättigade behörighet utan godkännande krävs, men kontraktet associerats måste begära godkännande. Du kan skapa en anpassad roll med samma behörigheter som rollen tilldelas medarbetare om du vill konfigurera arbetsflöde för godkännande för endast kontrakt associerats. Du kan kräva godkännande för att aktivera den anpassade rollen. [Mer information om anpassade roller](pim-resource-roles-custom-role-policy.md).

Använd följande procedurer för att konfigurera arbetsflöde för godkännande och ange vem som kan godkänna eller neka förfrågningar.

## <a name="require-approval-to-activate"></a>Kräv godkännande för att aktivera

1. Bläddra till PIM i Azure-portalen och väljer du en resurs i listan.

   ![”Azure-resurser” rutan med en markerad resurs](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

2. I den vänstra rutan, Välj **rollinställningar**.

3. Sök efter och välj en roll och välj sedan **redigera** att ändra inställningarna.

   ![”Redigera” knappen för operatörsrollen](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_view_settings.png)

4. I den **aktivering** väljer den **Kräv godkännande för att aktivera** kryssrutan.

   ![Avsnittet ”aktivering” av serverroll-inställningar](media/azure-pim-resource-rbac/aadpim_rbac_settings_require_approval_checkbox.png)

## <a name="specify-approvers"></a>Ange godkännare

Klicka på **Markera godkännare** att öppna den **väljer en användare eller grupp** fönstret.

>[!NOTE]
>Du måste välja minst en användare eller grupp för att uppdatera inställningen. Det finns ingen standard godkännare.

Resurs-administratörer kan lägga till valfri kombination av användare och grupper i listan över granskare. 

![”Välj en användare eller grupp” fönstret till en användare som har valts](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_select_approvers.png)

## <a name="request-approval-to-activate"></a>Begära tillstånd att aktivera

Begära godkännande har ingen inverkan på den procedur som en medlem måste följa för att aktivera. [Gå igenom stegen för att aktivera en roll](pim-resource-roles-activate-your-roles.md).

Om en medlem begärt aktivering av en roll som kräver godkännande och rollen krävs inte längre avbryta medlemmen begäran i PIM.

Om du vill avbryta, bläddra till PIM och välj **Mina förfrågningar**. Leta reda på begäran och välj **Avbryt**.

![”Mina förfrågningar” fönstret](media/azure-pim-resource-rbac/aadpim_rbac_role_approval_request_pending.png)

## <a name="approve-or-deny-a-request"></a>Godkänna eller avvisa en begäran

Om du vill godkänna eller neka en begäran, måste du vara medlem av listan över granskare. 

1. Välj i PIM, **godkänna begäranden** från fliken på den vänstra menyn och leta reda på begäran.

   ![”Godkänna begäranden” fönstret](media/azure-pim-resource-rbac/aadpim_rbac_approve_requests_list.png)

2. Välj begäran, ange en motivering för beslutet och välj **Godkänn** eller **neka**. Sedan har begäran lösts.

   ![Begäran som valts med detaljerad information](media/azure-pim-resource-rbac/aadpim_rbac_approve_request_approved.png)

## <a name="workflow-notifications"></a>Arbetsflödesmeddelanden

Här följer fakta om arbetsflödesmeddelanden:

- Alla medlemmar i listan godkännare meddelas via e-post när en begäran om en roll är väntande granskning. E-postmeddelanden innehåller en direktlänk till begäran, där godkännaren kan godkänna eller neka.
- Begäranden kan lösas med den första medlemmen i listan som godkänner eller nekar. 
- När en godkännare svarar på begäran, meddelas alla medlemmar i listan godkännare för åtgärden. 
- Resursen administratörer meddelas när en godkänd medlem aktiveras i deras roll. 

>[!Note]
>En resurs-administratör som anser att en godkänd medlem inte får vara aktiva kan ta bort aktiva rolltilldelningen i PIM. Även om resursen administratörer inte meddelas om väntande begäranden om de är medlemmar i listan över granskare, kan de visa och Avbryt väntande begäranden för alla användare genom att visa väntande begäranden i PIM. 

## <a name="next-steps"></a>Nästa steg

[Tillämpa PIM-inställningar till unika grupper av användare](pim-resource-roles-custom-role-policy.md)
