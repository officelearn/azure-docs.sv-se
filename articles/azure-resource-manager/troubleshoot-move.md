---
title: Felsöka fel när du flyttar Azure-resurser till en ny prenumeration eller resurs grupp
description: Använd Azure Resource Manager för att flytta resurser till en ny resursgrupp eller prenumeration.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: b688218b871a5f652e7f4de172d23f1b1fb0aa5c
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035509"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Felsöka flytt av Azure-resurser till en ny resurs grupp eller prenumeration

Den här artikeln innehåller förslag på hur du kan lösa problem när du flyttar resurser.

## <a name="upgrade-a-subscription"></a>Uppgradera en prenumeration

Om du faktiskt vill uppgradera din Azure-prenumeration (t. ex. genom att byta från kostnads fritt till betala per användning) måste du konvertera din prenumeration.

* Om du vill uppgradera en kostnadsfri utvärderingsversion, [uppgradera din kostnadsfria utvärderingsversion eller en Microsoft Imagine Azure-prenumeration till betala per användning](../billing/billing-upgrade-azure-subscription.md).
* Om du vill ändra en betala per användning-konto, se [ändra din betala per användning för Azure-prenumeration till ett annat erbjudande](../billing/billing-how-to-switch-azure-offer.md).

Om du inte kan konvertera prenumerationen, [skapa en Azure-supportbegäran](../azure-supportability/how-to-create-azure-support-request.md). Välj **prenumerationshantering** för typ av ärende.

## <a name="service-limitations"></a>Tjänst begränsningar

Vissa tjänster kräver ytterligare överväganden när du flyttar resurser. Om du flyttar följande tjänster, se till att du har kontroll över vägledning och begränsningar.

* [App Services](./move-limitations/app-service-move-limitations.md)
* [Azure DevOps-tjänster](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [Klassisk distributionsmodell](./move-limitations/classic-model-move-limitations.md)
* [Nätverk](./move-limitations/networking-move-limitations.md)
* [Recovery Services](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Virtual Machines](./move-limitations/virtual-machines-move-limitations.md)

## <a name="large-requests"></a>Stora begär Anden

När det är möjligt, flyttar break stora till separata flyttåtgärder. Resource Manager returnerar omedelbart ett fel när det finns fler än 800 resurser i en enda åtgärd. Men kan flyttar resurser som är mindre än 800 också misslyckas av tiden går ut.

## <a name="resource-not-in-succeeded-state"></a>Resursen har inte statusen slutförd

När du får ett fel meddelande som anger att en resurs inte kan flyttas eftersom den inte är i ett framgångs tillstånd, kan det hända att den faktiskt är en beroende resurs som blockerar flytten.

Om käll-eller mål resurs gruppen innehåller ett virtuellt nätverk, kontrol leras tillståndet för alla beroende resurser för det virtuella nätverket under flytten. Om någon av dessa resurser är i ett felaktigt tillstånd blockeras flyttningen. Om till exempel en virtuell dator som använder det virtuella nätverket har misslyckats blockeras flyttningen. Flyttningen blockeras även om den virtuella datorn inte är en av de resurser som flyttas och inte finns i någon av resurs grupperna för flytten. Undvik det här problemet genom att flytta dina resurser till en resurs grupp som inte har ett virtuellt nätverk.

## <a name="next-steps"></a>Nästa steg

För kommandon för att flytta resurser, se [Flytta resurser till ny resurs grupp eller prenumeration](resource-group-move-resources.md).
