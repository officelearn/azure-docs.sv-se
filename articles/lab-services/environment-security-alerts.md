---
title: Säkerhets aviseringar för miljöer i Azure DevTest Labs
description: Den här artikeln visar hur du visar säkerhets aviseringar för en miljö i DevTest Labs och vidtar lämpliga åtgärder.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2020
ms.author: spelluru
ms.openlocfilehash: 4ca17bece33107de756eb221e14eaab851660a99
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76992240"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Säkerhets aviseringar för miljöer i Azure DevTest Labs
Den här artikeln visar hur du visar säkerhets aviseringar för miljöer i Azure DevTest Labs. 

## <a name="prerequisites"></a>Krav
För närvarande kan du bara Visa säkerhets aviseringar för miljöer som distribuerats i labbet. Om du vill testa eller använda den här funktionen distribuerar du en miljö i labbet. 

## <a name="view-security-alerts-for-an-environment"></a>Visa säkerhets aviseringar för en miljö

1. På Start sidan för ditt labb väljer du **säkerhets aviseringar** på den vänstra menyn. Du bör se antalet säkerhets aviseringar (hög, medel och låg).

    ![Säkerhets aviseringar – översikt](./media/environment-security-alerts/security-alerts-overview-page.png)
2. Högerklicka på tre punkter (...) i den sista kolumnen och välj **Visa säkerhets aviseringar**. 

    ![Visa säkerhetsaviseringar](./media/environment-security-alerts/view-security-alerts-menu.png)
3. Du hittar mer information om rekommendationerna för aviseringar och rådgivare. 

    ![Visa säkerhetsaviseringar](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>Nästa steg
Mer information om miljöer finns i följande artiklar:

- [Skapa miljöer med flera virtuella datorer och PaaS-resurser med Azure Resource Manager mallar](devtest-lab-create-environment-from-arm.md)
- [Konfigurera och använda offentliga miljöer](devtest-lab-configure-use-public-environments.md)
