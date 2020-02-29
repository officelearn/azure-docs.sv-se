---
title: Vi drar tillbaka de klassiska virtuella Azure-datorerna den 1 mars 2023
description: Artikeln innehåller en översikt över den klassiska VM-indragningen
services: virtual-machines
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 9f3fd59fa040ab46a5fc4ef8272a17cba4c631b8
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925779"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Migrera dina IaaS-resurser till Azure Resource Manager den 1 mars 2023 

I 2014 lanserade vi IaaS på Azure Resource Manager och har ökat funktionerna någonsin sedan. Eftersom [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) nu har fullständiga IaaS-funktioner och andra framsteg förbrukade vi hanteringen av IaaS-datorer via Azure Service Manager den 28 februari 2020 och den här funktionen kommer att dras tillbaka den 1 mars 2023. 

Idag, cirka 90% av de virtuella IaaS-datorerna använder Azure Resource Manager. Om du använder IaaS-resurser via Azure Service Manager (ASM) börjar du planera migreringen nu och slutför den 1 mars 2023 för att dra nytta av [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/).

Klassiska virtuella datorer kommer att följa den [moderna livs cykel principen](https://support.microsoft.com/help/30881/modern-lifecycle-policy) för utfasning.

## <a name="how-does-this-affect-me"></a>Hur påverkar detta mig? 

1) Från och med den 28 februari 2020 kommer kunder som inte utnyttjade IaaS-datorer via Azure Service Manager (ASM) under månaden februari 2020 inte längre att kunna skapa klassiska virtuella datorer. 
2) Den 1 mars 2023 kommer kunder inte längre att kunna starta IaaS virtuella datorer med Azure Service Manager och alla som fortfarande körs eller allokeras stoppas och frigörs. 
2) Den 1 mars 2023 kommer prenumerationer som inte har migrerats till Azure Resource Manager att informeras om tids linjer för borttagning av eventuella återstående klassiska virtuella datorer.  

Följande Azure-tjänster och-funktioner påverkas **inte** av den här indragningen: 
- Cloud Services 
- Lagrings konton som **inte** används av klassiska virtuella datorer 
- Virtuella nätverk (virtuella nätverk) som **inte** används av klassiska virtuella datorer. 
- Andra klassiska resurser

## <a name="what-actions-should-i-take"></a>Vilka åtgärder ska jag göra? 

- Börja planera migreringen till Azure Resource Manager, idag. 

- [Läs mer](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview) om hur du migrerar dina klassiska [Linux](./linux/migration-classic-resource-manager-plan.md) -och [Windows](./windows/migration-classic-resource-manager-plan.md) -datorer till Azure Resource Manager.

- Mer information finns i [vanliga frågor och svar om klassisk Azure Resource Manager migrering](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq)

- [Kontakta supporten](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)för tekniska frågor och problem.

- För andra frågor som inte ingår i vanliga frågor och svar och kommentarer nedan.
