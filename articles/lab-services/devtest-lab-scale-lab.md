---
title: Skala kvoter och begränsningar i ditt labb i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du skalar ett labb i Azure DevTest Labs
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
ms.openlocfilehash: ec79f6a9b255d44e66b901a0aae263c8dbbf2863
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149868"
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>Skala kvoter och begränsningar i DevTest Labs
När du arbetar i labb kan kanske du märker att det finns vissa standardgränser till vissa Azure-resurser, vilket kan påverka tjänsten DevTest Labs. Dessa gränser kallas **kvoter**.

> [!NOTE]
> DevTest Labs-tjänsten införa inte kvoter. Kvoter som kan uppstå är standardbegränsningar för övergripande Azure-prenumerationen.

Du kan använda varje Azure-resurs tills du når sin kvot. Varje prenumeration har separata kvoter och användning spåras per prenumeration.

Till exempel har varje prenumeration en standardkvot på 20 kärnor. Så om du skapar virtuella datorer i labbet med fyra kärnor, kan du bara skapa fem virtuella datorer.

[Azure-prenumeration och tjänstbegränsningar](https://docs.microsoft.com/azure/azure-subscription-service-limits) listas några av de vanligaste kvoter för Azure-resurser. Resurserna som används oftast i ett labb och för vilket du kan stöta på kvoter, är VM-kärnor, offentliga IP-adresser, nätverksgränssnitt, hanterade diskar, RBAC rolltilldelning och ExpressRoute-kretsar.

## <a name="view-your-usage-and-quotas"></a>Visa användning och kvoter
De här stegen visar hur du visar de aktuella kvoterna i din prenumeration för specifika Azure-resurser och se vilken procentandel av varje kvot som du har använt.

1. Logga in på [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Välj **fler tjänster**, och välj sedan **fakturering** i listan.
1. Välj en prenumeration på bladet fakturering.
4. Välj **användning + kvoter**.

   ![Knappen för användning och kvoter](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas.png)

   Användning + kvoter bladet visas, lista över olika resurser som är tillgängliga i den prenumerationen och procentandelen kvoten som används per resurs.

   ![Kvoter och användning](./media/devtest-lab-scale-lab/devtestlab-view-quotas.png)

## <a name="requesting-more-resources-in-your-subscription"></a>Begär fler resurser i din prenumeration
Om du uppnår ett kvottak Standardgränsen för en resurs i en prenumeration kan utökas upp till en övre gräns, enligt beskrivningen i [Azure-prenumeration och tjänstbegränsningar](https://docs.microsoft.com/azure/azure-subscription-service-limits).

De här stegen visar hur du begära en kvot via den [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Välj **fler tjänster**väljer **fakturering**, och välj sedan **användning + kvoter**.
1. I användning + kvoter bladet och välja den **begär öka** knappen.

   ![Begär ökning knappen](./media/devtest-lab-scale-lab/devtestlab-request-increase.png)

1. Slutför och skicka din begäran genom att fylla i informationen som krävs på de tre flikarna i den **ny supportbegäran** formuläret.

   ![Formulär för ökning](./media/devtest-lab-scale-lab/devtestlab-support-form.png)

[Förstå Azure-gränser och ökar](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/) innehåller mer information om att kontakta Azure-supporten om du vill öka kvoten.



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>Nästa steg
* Utforska den [DevTest Labs Azure Resource Manager QuickStart mallgalleriet](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
