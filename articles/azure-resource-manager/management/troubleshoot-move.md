---
title: Felsöka flyttfel
description: Använd Azure Resource Manager för att flytta resurser till en ny resursgrupp eller prenumeration.
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: 5a65f7daa0f5e3b1c8c6ddfdbecc0ff7d53e5afd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75891263"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Felsöka flytt av Azure-resurser till en ny resursgrupp eller prenumeration

Den här artikeln innehåller förslag som hjälper dig att lösa problem när du flyttar resurser.

## <a name="upgrade-a-subscription"></a>Uppgradera en prenumeration

Om du verkligen vill uppgradera din Azure-prenumeration (till exempel byta från gratis till betala per användning) måste du konvertera din prenumeration.

* Information om hur du uppgraderar en kostnadsfri utvärderingsversion finns i [Uppgradera din kostnadsfria utvärderingsversion eller Microsoft Imagine Azure-prenumeration till Pay-As-You-Go](../../billing/billing-upgrade-azure-subscription.md).
* Information om hur du ändrar ett pay-as-you-go-konto finns i [Ändra din Azure Pay-As-You-Go-prenumeration till ett annat erbjudande](../../billing/billing-how-to-switch-azure-offer.md).

Om du inte kan konvertera prenumerationen [skapar du en Azure-supportbegäran](../../azure-portal/supportability/how-to-create-azure-support-request.md). Välj **Prenumerationshantering** för ärendetypen.

## <a name="service-limitations"></a>Begränsningar av tjänsten

Vissa tjänster kräver ytterligare överväganden när resurser flyttas. Om du flyttar följande tjänster kontrollerar du riktlinjerna och begränsningarna.

* [App Services](./move-limitations/app-service-move-limitations.md)
* [Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [Klassisk distributionsmodell](./move-limitations/classic-model-move-limitations.md)
* [Nätverk](./move-limitations/networking-move-limitations.md)
* [Recovery Services](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Virtuella datorer](./move-limitations/virtual-machines-move-limitations.md)

## <a name="large-requests"></a>Stora förfrågningar

När det är möjligt, bryta stora drag i separata flyttoperationer. Resource Manager returnerar omedelbart ett fel när det finns mer än 800 resurser i en enda åtgärd. Men flytta mindre än 800 resurser kan också misslyckas genom att time out.

## <a name="resource-not-in-succeeded-state"></a>Resursen lyckades inte

När du får ett felmeddelande som anger att en resurs inte kan flyttas eftersom den inte är i ett efterföljande tillstånd, kan det faktiskt vara en beroende resurs som blockerar flytten. Felkoden är vanligtvis **MoveCannotProceedWithResourcesNotInSucceededState**.

Om käll- eller målresursgruppen innehåller ett virtuellt nätverk kontrolleras tillstånden för alla beroende resurser för det virtuella nätverket under flytten. Kontrollen omfattar dessa resurser direkt och indirekt beroende av det virtuella nätverket. Om någon av dessa resurser är i ett misslyckat tillstånd blockeras flytten. Om till exempel en virtuell dator som använder det virtuella nätverket har misslyckats blockeras flytten. Flytten blockeras även när den virtuella datorn inte är en av de resurser som flyttas och inte finns i en av resursgrupperna för flytten.

När du får det här felet har du två alternativ. Flytta antingen resurserna till en resursgrupp som inte har ett virtuellt nätverk eller [kontakta supporten](../../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="next-steps"></a>Nästa steg

Kommandon som ska flyttas finns i [Flytta resurser till ny resursgrupp eller prenumeration](move-resource-group-and-subscription.md).
