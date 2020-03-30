---
title: Säkerhetsaviseringar för miljöer i Azure DevTest Labs
description: Den här artikeln visar hur du visar säkerhetsaviseringar för en miljö i DevTest Labs och vidtar en lämplig åtgärd.
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
ms.openlocfilehash: fbac5a2fab91cdac8ebf626e324f12f209cfade5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588713"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Säkerhetsaviseringar för miljöer i Azure DevTest Labs
Som labbanvändare kan du nu visa Azure Security Center-aviseringar för dina labbmiljöer. Security Center samlar automatiskt in, analyserar och integrerar loggdata från Azure-resurser, nätverket och anslutna partnerlösningar som brandväggs- och slutpunktsskyddslösningar för att identifiera verkliga hot och minimera antalet falska positiva identifieringar. En lista över prioriterade säkerhetsvarningar visas i Security Center tillsammans med den information som du behöver för att snabbt undersöka problemet, samt rekommendationer för hur du åtgärdar ett angrepp. [Läs mer om säkerhetsaviseringar i Azure Security Center](../security-center//security-center-alerts-overview.md).  


## <a name="prerequisites"></a>Krav
För närvarande kan du visa säkerhetsaviseringar endast för plattform som en tjänst (PaaS) miljöer som distribueras i ditt labb. Om du vill testa eller använda den här funktionen [distribuerar du en miljö till labbet](devtest-lab-create-environment-from-arm.md). 

## <a name="view-security-alerts-for-an-environment"></a>Visa säkerhetsaviseringar för en miljö

1. På startsidan för ditt labb väljer du **Säkerhetsvarningar** på den vänstra menyn. Du bör se antalet säkerhetsaviseringar (hög, medel och låg). Läs mer om [hur aviseringar klassificeras](../security-center/security-center-alerts-overview.md#how-are-alerts-classified).

    ![Säkerhetsaviseringar - översikt](./media/environment-security-alerts/security-alerts-overview-page.png)
2. Högerklicka på tre punkter (...) i den sista kolumnen och välj **Visa säkerhetsvarningar**. 

    ![Visa säkerhetsaviseringar](./media/environment-security-alerts/view-security-alerts-menu.png)
    
3. Du ser mer information om aviseringar och rådgivande rekommendationer. Läs mer om [hur du hanterar och svarar på säkerhetsaviseringar i Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md).

    ![Visa säkerhetsaviseringar](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>Nästa steg
Mer information om miljöer finns i följande artiklar:

- [Skapa miljöer med flera virtuella datorer och PaaS-resurser med Azure Resource Manager-mallar](devtest-lab-create-environment-from-arm.md)
- [Konfigurera och använda offentliga miljöer](devtest-lab-configure-use-public-environments.md)
