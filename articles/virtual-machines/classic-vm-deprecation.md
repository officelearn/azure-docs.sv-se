---
title: Vi går i pension med Virtuella Azure Classic-datorer den 1 mars 2023
description: Artikeln ger en översikt över classic VM-pensionering på hög nivå
services: virtual-machines
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 764567bffd2a08ebb5beb17e3063998848b3f110
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127335"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Migrera dina IaaS-resurser till Azure Resource Manager senast den 1 mars 2023 

Under 2014 lanserade vi IaaS på Azure Resource Manager och har förbättrat funktionerna sedan dess. Eftersom [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) nu har fullständiga IaaS-funktioner och andra framsteg, har vi inaktiverat hanteringen av virtuella IaaS-datorer via Azure Service Manager den 28 februari 2020 och den här funktionen kommer att dras tillbaka helt den 1 mars 2023. 

Idag använder cirka 90 % av IaaS virtuella datorer Azure Resource Manager. Om du använder IaaS-resurser via Azure Service Manager (ASM) börjar du planera migreringen nu och slutför den senast den 1 mars 2023 för att dra nytta av [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/).

Klassiska virtuella datorer följer [den moderna livscykelpolicyn](https://support.microsoft.com/help/30881/modern-lifecycle-policy) för pensionering.

## <a name="how-does-this-affect-me"></a>Hur påverkar det här mig? 

1) Från och med den 28 februari 2020 kommer kunder som inte använde virtuella IaaS-datorer via Azure Service Manager (ASM) i februari månad 2020 inte längre att kunna skapa klassiska virtuella datorer. 
2) Den 1 mars 2023 kan kunder inte längre starta virtuella IaaS-datorer med Azure Service Manager och alla som fortfarande körs eller allokeras stoppas och hanteras. 
2) Den 1 mars 2023 informeras prenumerationer som inte har migrerat till Azure Resource Manager om tidslinjer för att ta bort eventuella återstående klassiska virtuella datorer.  

Följande Azure-tjänster och -funktioner påverkas **INTE** av den här pensioneringen: 
- Molntjänster 
- Lagringskonton som **inte** används av klassiska virtuella datorer 
- Virtuella nätverk (VNets) används **inte** av klassiska virtuella datorer. 
- Andra klassiska resurser

## <a name="what-actions-should-i-take"></a>Vilka åtgärder ska jag vidta? 

- Börja planera migreringen till Azure Resource Manager idag. 

- [Läs mer](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview) om hur du migrerar dina klassiska [virtuella Linux-](./linux/migration-classic-resource-manager-plan.md) och [Windows-datorer](./windows/migration-classic-resource-manager-plan.md) till Azure Resource Manager.

- Mer information finns i vanliga [frågor om klassisk till Azure Resource Manager-migrering](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq)

- För tekniska frågor och problem, [kontakta support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

- För andra frågor som inte ingår i FAQ och feedback, kommentera nedan.
