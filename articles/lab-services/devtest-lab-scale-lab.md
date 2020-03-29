---
title: Skala kvoter och gränser i labbet i Azure DevTest Labs | Microsoft-dokument
description: I den här artikeln beskrivs hur du kan skala ditt labb i Azure DevTest Labs. Visa dina användningskvoter och begränsningar och begär en ökning.
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
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: f3299c24bc751263ccd284a4fddb492fba5dd1e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76761193"
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>Skala kvoter och gränser i DevTest Labs
När du arbetar i DevTest Labs kanske du märker att det finns vissa standardgränser för vissa Azure-resurser, vilket kan påverka DevTest Labs-tjänsten. Dessa gränser kallas **kvoter**.

> [!NOTE]
> DevTest Labs-tjänsten medför inga kvoter. Alla kvoter som du kan stöta på är standardbegränsningar för den övergripande Azure-prenumerationen.

Du kan använda varje Azure-resurs tills du når dess kvot. Varje prenumeration har separata kvoter och användningen spåras per prenumeration.

Varje prenumeration har till exempel en standardkvot på 20 kärnor. Så om du skapar virtuella datorer i labbet med fyra kärnor vardera, kan du bara skapa fem virtuella datorer.

[Azure-prenumerations- och tjänstgränser](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) listar några av de vanligaste kvoterna för Azure-resurser. De resurser som oftast används i ett labb och för vilka du kan stöta på kvoter, inkluderar VM-kärnor, offentliga IP-adresser, nätverksgränssnitt, hanterade diskar, RBAC-rolltilldelning och ExpressRoute-kretsar.

## <a name="view-your-usage-and-quotas"></a>Visa din användning och dina kvoter
De här stegen visar hur du visar de aktuella kvoterna i din prenumeration för specifika Azure-resurser och för att se hur stor procentandel av varje kvot du har använt.

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Välj **Fler tjänster**och välj sedan **Fakturering** i listan.
1. Välj en prenumeration i bladet Fakturering.
4. Välj **Användning + kvoter**.

   ![Knappen Användning och kvoter](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas.png)

   Bladet Användning + kvoter visas, med en lista över olika resurser som är tillgängliga i prenumerationen och procentandelen av kvoten som används per resurs.

   ![Kvoter och användning](./media/devtest-lab-scale-lab/devtestlab-view-quotas.png)

## <a name="requesting-more-resources-in-your-subscription"></a>Begära mer resurser i prenumerationen
Om du når ett kvottak kan standardgränsen för en resurs i en prenumeration ökas upp till en maximal gräns, enligt beskrivningen i [Azure-prenumerations- och tjänstgränser](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).

De här stegen visar hur du begär en kvotökning via [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Välj **Fler tjänster,** välj **Fakturering**och välj sedan Användning **+ kvoter**.
1. Välj knappen **Begär ökning i** bladet Användning + kvoter.

   ![Knappen Begär ökning](./media/devtest-lab-scale-lab/devtestlab-request-increase.png)

1. Fyll i och skicka begäran genom att fylla i den information som krävs på alla tre flikarna i formuläret **Ny supportbegäran.**

   ![Formulär för ökning av begäran](./media/devtest-lab-scale-lab/devtestlab-support-form.png)

[Förstå Azure Gränser och ökar](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/) ger mer information om hur du kontaktar Azure-support för att begära en kvotökning.



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>Nästa steg
* Utforska [snabbstartsgalleriet DevTest Labs Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
