---
title: Privileged Identity Management - arbetsflöde för godkännande för Azure-resursroller | Microsoft Docs
description: Beskriver hur Godkännandearbetsflöde process för Azure-resurser.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: c02d595d75b2d63558896054c185102ebb23cc9e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---approve"></a>Privileged Identity Management - resursroller - godkänna

Med arbetsflöden för godkännande i PIM för Azure-resursroller, kan administratörer ytterligare skydda eller begränsa åtkomsten till viktiga resurser genom att kräva godkännande för att aktivera rolltilldelningar. Unikt till Azure-resursroller är variant av en resurs-hierarki. Den här hierarkin kan ärvda rolltilldelningar från en överordnad resursobjektet nedåt till alla underordnade underordnade resurser i den överordnade behållaren. 

Till exempel Bob, en resursadministratör använder PIM tilldela Alice som en medlem som är berättigade till rollen som ägare i Contoso-prenumeration. Med den här tilldelningen är Alice också en berättigad ägare av alla resurs grupp behållare i Contoso-prenumeration och alla resurser (t.ex. virtuella datorer) som ingår i varje resursgrupp för prenumerationen. Antar vi att det finns tre resursgrupper i Contoso-prenumeration; Fabrikam Test Fabrikam Dev och Fabrikam produktprenumeration. Var och en av de här resursgrupperna innehåller en enskild virtuell dator.

PIM-inställningarna är konfigurerade för varje roll för en resurs och till skillnad från tilldelningar inställningarna inte ärvs och gäller enbart för resursrollen. [Läs mer om tillgängliga tilldelningar och synlighet.](pim-resource-roles-eligible-visibility.md)

I exemplet ovan använder Bob PIM vill kräva att alla medlemmar i rollen som ägare av Contoso prenumeration begäran om godkännande att aktivera. För att skydda resurser i resursgruppen Fabrikam Prod kräver Bob godkännande för medlemmar i rollen som ägare av den här resursen också. Rollerna ägare i Fabrikam Test- och Fabrikam Dev kräver inte godkännande för att aktivera.

När Alice begäranden aktivering av sitt ägarrollen för Contoso prenumerationen godkännare måste godkänna eller neka sin begäran innan hon blir aktiv i rollen. Dessutom om Alice beslutar att [omfång sin aktivering](pim-resource-roles-activate-your-roles.md#just-enough-administration) till resursgruppen Fabrikam Prod godkännare måste godkänna eller avvisa denna begäran för. Men om Alice beslutar att omfatta sin aktivering antingen eller båda Fabrikam Test eller Fabrikam Dev, godkännande inte krävs.

Arbetsflöde för godkännande får inte vara nödvändiga för alla medlemmar i en roll. Föreställ dig ett scenario där din organisation anlitar flera kontrakt har associerats till att utvecklingen av ett program som körs i en Azure-prenumeration. Som administratör av en resurs kan du vill att anställda har berättigade åtkomst utan godkännande krävs, men kontraktet associerats måste begära godkännande. Konfigurera arbetsflöde för godkännande för endast kontraktet du kan skapa en anpassad roll med samma behörigheter som rollen har associerats tilldelad till anställda och kräver godkännande för att aktivera anpassade rollen. [Mer information om anpassade roller](pim-resource-roles-custom-role-policy.md).

Följ stegen nedan för att konfigurera arbetsflöde för godkännande och ange vem som kan godkänna eller neka förfrågningar.

## <a name="require-approval-to-activate"></a>Kräv godkännande för att aktivera

Navigera till PIM i Azure-portalen och väljer du en resurs i listan.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

Välj den vänstra navigeringsmenyn **rollinställningar**.

Sök efter och välj en roll och på **redigera** att ändra inställningarna.

![](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_view_settings.png)

I aktiveringen avsnittet Markera kryssrutan **Kräv godkännande för att aktivera**.

![](media/azure-pim-resource-rbac/aadpim_rbac_settings_require_approval_checkbox.png)

## <a name="specify-approvers"></a>Ange godkännare

Klicka på **Markera godkännare** att öppna urvalssidan.

>[!NOTE]
>Du måste välja minst en användare eller grupp för att uppdatera inställningen. Det finns ingen standard godkännare.

Resurs-administratörer kan lägga till valfri kombination av användare och grupper i listan över granskare. 

![](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_select_approvers.png)

## <a name="request-approval-to-activate"></a>Begära tillstånd att aktivera

Begär godkännande har ingen inverkan på proceduren måste medlem följa för att aktivera. [Gå igenom stegen för att aktivera en roll](pim-resource-roles-activate-your-roles.md).

Om en medlem begärt aktivering av en roll som kräver godkännande och rollen krävs inte längre avbryta medlemmen begäran i PIM.

Om du vill avbryta, navigera till PIM och Välj ”Mina förfrågningar”. Leta upp begäran och klicka på ”Avbryt”.

![](media/azure-pim-resource-rbac/aadpim_rbac_role_approval_request_pending.png)

## <a name="approve-or-deny-a-request"></a>Godkänna eller avvisa en begäran

För att godkänna eller avvisa en begäran måste du vara medlem i listan över granskare. Välj ”godkänna begäranden” på fliken i den vänstra navigeringsmenyn i PIM, och leta reda på begäran.

![](media/azure-pim-resource-rbac/aadpim_rbac_approve_requests_list.png)

Välj begäran, ange en motivering för beslutet och välj godkänna eller neka lösa begäran.

![](media/azure-pim-resource-rbac/aadpim_rbac_approve_request_approved.png)

## <a name="workflow-notifications"></a>Arbetsflödesmeddelanden

- Alla medlemmar i listan godkännare meddelas via e-post när en begäran om en roll är väntande granskning. E-postmeddelanden innehåller en direktlänk till begäran där godkännaren kan godkänna eller neka.
- Begäranden kan lösas med den första medlemmen i listan som godkänner eller nekar. 
- När en godkännare svarar på begäran, meddelas alla medlemmar i listan godkännare för åtgärden. 
- Resursen administratörer meddelas när en godkänd medlem aktiveras i deras roll. 

>[!Note]
>I fall där en resursadministratör anser godkända medlemmen inte får vara aktiva, kan de ta bort aktiva rolltilldelningen i PIM. Även om resursen administratörer inte meddelas om väntande begäranden om de är medlemmar i listan över granskare, kan de visa och Avbryt väntande begäranden för alla användare genom att visa väntande begäranden i PIM. 

## <a name="next-steps"></a>Nästa steg

[Tillämpa PIM-inställningar till unika grupper av användare](pim-resource-roles-custom-role-policy.md)
