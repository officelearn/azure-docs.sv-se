---
title: Överföra Azure-prenumerationer från en partner till en annan (förhandsversion)
description: I den här artikeln får du lära dig det du behöver veta före och efter överföring av faktureringsägarskap för din Azure-prenumeration.
author: bandersmsft
ms.reviewer: mcville
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 09/07/2020
ms.author: banders
ms.openlocfilehash: 246128ec3d26e4bf3f2a5a8f0660aab7d9daca3d
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/08/2020
ms.locfileid: "89554218"
---
# <a name="transfer-azure-subscriptions-from-one-partner-to-another-preview"></a>Överföra Azure-prenumerationer från en partner till en annan (förhandsversion)

I den här artikeln får du lära dig det du behöver veta före och efter överföring av faktureringsägarskap för din Azure-prenumeration. För att starta en överföring av en Azure-prenumeration som finns under en Azure-plan från en Microsoft-partner till en annan måste du kontakta din partner. Partnern skickar instruktioner för hur du börjar. När överföringen har slutförts ändras faktureringsägarskapet för din prenumeration.

## <a name="user-access"></a>Användaråtkomst

Åtkomst till befintliga användare, grupper eller tjänsthuvudnamn som tilldelades med hjälp av Azure RBAC (rollbaserad åtkomstkontroll) påverkas inte under övergången. [Azure RBAC](../../role-based-access-control/overview.md) hjälper till vid hantering av vem som ska ha åtkomst till Azure-resurser, vad de ska kunna göra med resurserna och till vilka områden de ska ha åtkomst. Din nya partner får ingen RBAC-åtkomst till dina resurser i och med prenumerationsöverföringen. Din tidigare partner behåller sin RBAC-åtkomst.

Det är därför viktigt att du tar bort Azure RBAC-åtkomst för den gamla partnern och lägger till åtkomst för den nya partnern. Mer information om hur du ger din nya partner åtkomst finns i [Vad är rollbaserad åtkomstkontroll i Azure (Azure RBAC)?](../../role-based-access-control/overview.md) Mer information om hur du tar bort din tidigare partners RBAC-åtkomst finns i [Ta bort en rolltilldelning](../../role-based-access-control/role-assignments-portal.md#remove-a-role-assignment).

Din nya partner får inte heller automatiskt [AOBO-åtkomst](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) (Admin on Behalf Of, administratör på uppdrag av) till dina prenumerationer. AOBO krävs för att din partner ska kunna hantera Azure-prenumerationer åt dig. Mer information om hur du ger din nya partner AOBO-åtkomst finns i artikeln om att [överföra faktureringsägarskapet för ett Azure-prenumerationskonto till ett annat konto](billing-subscription-transfer.md).

## <a name="stop-a-transfer"></a>Stoppa en överföring

När du får en bekräftelse på att en överföringsbegäran har skickats kan du använda något av följande alternativ **om du inte vill att överföringen ska fortsätta**.

- Om begäran inte har godkänts av din nuvarande partner än kan du be din nya partner att avbryta den överföringsbegäran som de har startat (när statusen är väntande).
- Be den nuvarande partnern att inte vidta någon åtgärd när de tar emot överföringsbegäran. Överföringen kan inte fortsätta utan den nuvarande partnerns medgivande. Begäran går ut.
- Du kan _ta bort återförsäljarrelationen_ med den nya partnern. Den här åtgärden tar bort möjligheten att flytta din prenumeration. På det här sättet avbryts begäran.

Du kan också söka hjälp, rapportera felaktigt agerande eller misstänkt aktivitet med något av alternativen på webbplatsen med [Microsofts juridiska information](https://www.microsoft.com/legal/). Du hittar alternativet för att rapportera ett problem under Compliance & ethics (Efterlevnad och etik).

## <a name="next-steps"></a>Nästa steg

- Läs [Vad är rollbaserad åtkomstkontroll i Azure (Azure RBAC)?](../../role-based-access-control/overview.md) om du vill ge din nya partner RBAC-åtkomst.
- Läs artikeln om att [överföra faktureringsägarskapet för ett Azure-prenumerationskonto till ett annat konto](billing-subscription-transfer.md) om du vill ge din nya partner AOBO-åtkomst.