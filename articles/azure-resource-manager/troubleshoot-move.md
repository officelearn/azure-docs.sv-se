---
title: Felsöka fel vid flytt av Azure-resurser till ny prenumeration eller resursgrupp grupp
description: Använd Azure Resource Manager för att flytta resurser till en ny resursgrupp eller prenumeration.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: e23d7c571a010e5bfb42e5f15368e0194272ed53
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723472"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Felsöka flytta Azure-resurser till ny resursgrupp eller prenumeration

Den här artikeln innehåller förslag för att lösa problem när du flyttar resurser.

## <a name="upgrade-a-subscription"></a>Uppgradera en prenumeration

Om du verkligen vill uppgradera din Azure-prenumeration (till exempel byta från kostnadsfritt till betala per användning) måste konvertera din prenumeration.

* Om du vill uppgradera en kostnadsfri utvärderingsversion, [uppgradera din kostnadsfria utvärderingsversion eller en Microsoft Imagine Azure-prenumeration till betala per användning](../billing/billing-upgrade-azure-subscription.md).
* Om du vill ändra en betala per användning-konto, se [ändra din betala per användning för Azure-prenumeration till ett annat erbjudande](../billing/billing-how-to-switch-azure-offer.md).

Om du inte kan konvertera prenumerationen, [skapa en Azure-supportbegäran](../azure-supportability/how-to-create-azure-support-request.md). Välj **prenumerationshantering** för typ av ärende.

## <a name="service-limitations"></a>Tjänstens begränsningar

Vissa tjänster kräver ytterligare överväganden när du flyttar resurser. Om du flyttar följande tjänster, kontrollera att du kontrollerar riktlinjer och begränsningar.

* [App Services](./move-limitations/app-service-move-limitations.md)
* [Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [Klassisk distributionsmodell](./move-limitations/classic-model-move-limitations.md)
* [Recovery Services](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Virtual Machines](./move-limitations/virtual-machines-move-limitations.md)
* [virtuella nätverk](./move-limitations/virtual-network-move-limitations.md)

## <a name="large-requests"></a>Stora begäranden

När det är möjligt, flyttar break stora till separata flyttåtgärder. Resource Manager returnerar omedelbart ett fel när det finns fler än 800 resurser i en enda åtgärd. Men kan flyttar resurser som är mindre än 800 också misslyckas av tiden går ut.

## <a name="next-steps"></a>Nästa steg

Kommandon att flytta resurser finns i [flytta resurser till ny resursgrupp eller prenumeration](resource-group-move-resources.md).
