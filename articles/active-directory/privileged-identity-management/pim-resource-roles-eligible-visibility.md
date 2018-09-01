---
title: Berättigade uppgifter och synlighet i PIM - Azure | Microsoft Docs
description: Beskriver hur du tilldelar medlemmar som är berättigade till Azure-resursroller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 59d51ba8edadd1fd71255271623b144cab94fc97
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344291"
---
# <a name="eligible-assignments-and-resource-visibility-in-pim"></a>Berättigade uppgifter och synlighet i PIM

Privileged Identity Management (PIM) för Azure-resursroller ger förbättrad säkerhet för organisationer som har viktiga Azure-resurser. Resurs-administratörer kan använda PIM för att tilldela medlemmar som är berättigade till resursroller. Läs mer om de olika tilldelningstyperna och tilldelning av tillstånden för Azure-resursroller i följande avsnitt. 

## <a name="assignment-types"></a>Tilldelningstyper

PIM för Azure-resurser tillhandahåller två distinkta tilldelningstyperna:

- Berättigad
- Active

Berättigade uppgifter kräver medlem i rollen att utföra en åtgärd för att använda rollen. Åtgärder kan innehålla lyckas en kontroll för multifaktorautentisering, vilket ger en motivering eller begära godkännande från utnämnda godkännare.

Aktiva uppgifter kräver inte medlemmen som ska utföra alla åtgärder för att använda rollen. Medlemmar som är tilldelad som aktiv har de behörigheter som tilldelats rollen hela tiden.

## <a name="assignment-duration"></a>Tilldelningsvaraktighet

Resurs-administratörer kan välja mellan två alternativ för varje tilldelning när de konfigurerar PIM-inställningar för en roll. De här alternativen blir standard maximal varaktighet när en medlem har tilldelats rollen i PIM. 

En administratör kan välja någon av tilldelningstyperna:

- Tillåt permanent berättigad uppgift
- Tillåt permanent aktiv uppgift

Eller så kan administratören välja någon av följande typer av tilldelning:

- Berättigade uppgifter upphör efter
- Aktiva uppgifter upphör efter

Om en resursadministratör väljer **Tillåt permanent berättigad uppgift** eller **Tillåt permanent aktiv uppgift**, alla administratörer som tilldelas resursen medlemmar kan tilldela permanent medlemskap.

Om en resursadministratör väljer **berättigade uppgifter upphör efter** eller **aktiva uppgifter upphör efter**, resursadministratör styr livscykeln tilldelningen genom att kräva som alla tilldelningar har ett visst datum för start- och slutdatum.

> [!NOTE] 
> Alla tilldelningar som har ett angivet slutdatum kan förnyas av resurs-administratörer. Medlemmar kan också initiera självbetjäning begäranden till [utöka eller förnya uppgifter](pim-resource-roles-renew-extend.md).


## <a name="assignment-states"></a>Tilldelning av tillstånd

PIM för Azure-resurser har två distinkta tilldelning tillstånd som visas på den **aktiva roller** fliken i den **Mina roller**, **roller**, och **medlemmar**vyer av PIM. Dessa tillstånd är:

- Tilldelad
- Aktiverat

När du visar ett medlemskap som anges i **aktiva roller**, du kan använda värdet i den **tillstånd** kolumnen för att skilja mellan användare som är **tilldelad** som aktiv och användare som **aktiverad** en berättigad uppgift, och är nu aktiv.

## <a name="azure-resource-role-approval-workflow"></a>Arbetsflöde för godkännande av rollen i Azure-resurs

Med Godkännandearbetsflöde i PIM för Azure-resursroller administratörer ytterligare skydda och begränsa åtkomsten till viktiga resurser. Det innebär att administratörer kan kräva godkännande för att aktivera rolltilldelningar.

En resurs-hierarki är unika för Azure-resursroller. Den här hierarkin kan ärvda rolltilldelningar från en överordnad resurs-objektet nedåt till alla underordnade resurser i den överordnade behållaren. 

Till exempel: Bob, administratör av en resurs kan används PIM Alice som en berättigad medlem tilldelas rollen ägare i Contoso-prenumeration. Med denna tilldelning är Alice berättigade ägare av alla resource group behållare i Contoso-prenumeration. Alice är också berättigade ägare av alla resurser (t.ex. virtuella datorer) i varje resursgrupp för prenumerationen.

Vi antar att det finns tre resursgrupper i Contoso-prenumerationen: Fabrikam Test, Fabrikam Dev och Fabrikam Prod. Var och en av de här resursgrupperna innehåller en enda virtuell dator.

PIM-inställningarna har konfigurerats för varje roll för en resurs. Till skillnad från tilldelningar, dessa inställningar ärvs inte och gäller enbart för resursrollen.

Fortsättning på exemplet: Bob använder PIM vill kräva att alla medlemmar i rollen ägare av Contoso-prenumerationsgodkännande för begäran aktiveras. För att skydda resurserna i resursgruppen Fabrikam Prod, kräver Bob även godkännande för medlemmar i rollen ägare för den här resursen. Rollerna ägare i Fabrikam Test- och Fabrikam Dev kräver inte godkännande för aktivering.

När Alice begär aktivering av sin ägarrollen för Contoso-prenumeration, måste en godkännare Godkänn eller neka hennes begäran innan hon blir aktiv i rollen. Om Alice beslutar att [omfång hennes aktivering](pim-resource-roles-activate-your-roles.md#apply-just-enough-administration-practices) till Fabrikam Prod resursgrupp, en godkännare måste godkänna eller neka denna begäran för. Men om Alice bestämmer sig att definiera omfattningen av sin aktivering till ena eller båda Fabrikam Test- eller Fabrikam Dev godkännande krävs inte.

Arbetsflöde för godkännande kanske inte är nödvändigt för alla medlemmar i en roll. Tänk dig ett scenario där din organisation nyanställda flera kontrakt associates som underlättar utvecklingen av ett program som körs i en Azure-prenumeration. Som administratör av en resurs kan du vilja att anställda ska ha berättigad åtkomst utan godkännande krävs dock kontrakt associates måste begära godkännande. Om du vill konfigurera arbetsflöde för godkännande för endast kontrakt associates, kan du skapa en anpassad roll med samma behörigheter som rolltilldelningen för anställda. Du kan kräva godkännande för att aktivera den anpassa rollen. [Mer information om anpassade roller](pim-resource-roles-custom-role-policy.md).

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure-resursroller i PIM](pim-resource-roles-assign-roles.md)
