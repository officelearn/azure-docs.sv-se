---
title: Skala kvoter och gränser i ditt labb i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du skala ett labb i Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: ae624155-9181-45fa-bd2b-1983339b7e0e
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 5a179c0c6b777ee6b2afdd0f2e9267d247665d8d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787481"
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>Skala kvoter och gränser i DevTest Labs
När du arbetar i DevTest Labs märker du att det inte finns några Azure-resurser, vilket kan påverka tjänsten DevTest Labs vissa standardgränser. Dessa gränser kallas **kvoter**.

> [!NOTE]
> Tjänsten DevTest Labs införa inte kvoter. Kvoter som kan uppstå är standardbegränsningar av övergripande Azure-prenumerationen.

Du kan använda varje Azure-resurs tills du når sin kvot. Varje prenumeration har separata kvoter och användning spåras per prenumeration.

Varje prenumeration har till exempel en standardkvot på 20 kärnor. Så om du skapar virtuella datorer i labbet med fyra kärnor, kan du bara skapa fem virtuella datorer. 

[Azure-prenumeration och Tjänstbegränsningarna](https://docs.microsoft.com/azure/azure-subscription-service-limits) listar några av de vanligaste kvoterna för Azure-resurser. Resurserna som används mest i ett labb och för vilket du kan stöta på kvoter, innehåller VM kärnor, offentliga IP-adresser, gränssnitt, hanterade diskar, RBAC rolltilldelning och ExpressRoute-kretsar.

## <a name="view-your-usage-and-quotas"></a>Visa användnings- och kvoter
Dessa steg visar hur att visa de aktuella kvoterna i din prenumeration för specifika Azure-resurser och se hur många procent av varje kvot som du har använt.

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Välj **fler tjänster**, och välj sedan **fakturering** från listan.
1. Välj en prenumeration i bladet fakturering.
4. Välj **användning + kvoter**.

   ![Knappen användning och kvoter](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas.png)

   Användning + kvoter bladet visas, lista över olika resurser som är tillgängliga i den prenumerationen och procentandelen kvoten som används per resurs.

   ![Kvoter och användning](./media/devtest-lab-scale-lab/devtestlab-view-quotas.png)

## <a name="requesting-more-resources-in-your-subscription"></a>Begär fler resurser i din prenumeration
Om du når ett kvoten tak Standardgränsen för en resurs i en prenumeration kan du öka upp till en övre gräns, enligt beskrivningen i [Azure-prenumeration och Tjänstbegränsningarna](https://docs.microsoft.com/azure/azure-subscription-service-limits).

Dessa steg visar hur du begära en ökad kvot via den [Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Välj **fler tjänster**väljer **fakturering**, och välj sedan **användning + kvoter**.
1. I användning + kvoter bladet väljer den **begära öka** knappen.

   ![Knappen för ökning av begäran](./media/devtest-lab-scale-lab/devtestlab-request-increase.png)

1. För att slutföra och skicka begäran fyller i informationen som krävs på de tre flikarna i den **ny supportbegäran** formuläret.

   ![Öka formulär](./media/devtest-lab-scale-lab/devtestlab-support-form.png)

[Förstå Azure gränser och ökar](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/) innehåller mer information om att kontakta Azure-supporten om du vill begära en ökad kvot.



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>Nästa steg
* Utforska den [DevTest Labs Azure Resource Manager QuickStart mallgalleriet](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
