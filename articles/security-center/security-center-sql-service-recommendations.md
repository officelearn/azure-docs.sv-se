---
title: Rekommendationer & lagring – Azure Security Center
description: Det här dokumentet innehåller rekommendationer i Azure Security Center som hjälper dig att skydda dina data och Azure SQL-tjänst och hålla sig i enlighet med säkerhetsprinciper.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2019
ms.author: memildin
ms.openlocfilehash: 74ed55e1d460495bfa8d3d4c00bd37bb7f05260e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552873"
---
# <a name="protect-azure-data-and-storage-services"></a>Skydda Azure-data- och lagringstjänster
När Azure Security Center identifierar potentiella säkerhetsproblem skapas rekommendationer som hjälper dig att konfigurera de nödvändiga kontrollerna för att härda och skydda dina resurser.

I den här artikeln **beskrivs sidan Datasäkerhet** i avsnittet resurssäkerhet i Security Center.

En fullständig lista över de rekommendationer som kan visas på den här sidan finns i [rekommendationer för data och lagring](recommendations-reference.md#recs-datastorage).


## <a name="view-your-data-security-information"></a>Visa din datasäkerhetsinformation

1. Klicka på Data och **lagringsresurser**i avsnittet **Resurssäkerhetshygien** .

    ![Data & lagringsresurser](./media/security-center-monitoring/click-data.png)

    Sidan **Datasäkerhet** öppnas med rekommendationer för dataresurser.

    [![Dataresurser](./media/security-center-monitoring/sql-overview.png)](./media/security-center-monitoring/sql-overview.png#lightbox)

    På sidan kan du:

    * Klicka på fliken **Översikt** visar alla dataresursrekommendationer som ska åtgärdas. 
    * Klicka på varje flik och visa rekommendationerna efter resurstyp.

    > [!NOTE]
    > Mer information om lagringskryptering finns i [Azure Storage-kryptering för data i vila](../storage/common/storage-service-encryption.md).


## <a name="remediate-a-recommendation-on-a-data-resource"></a>Åtgärda en rekommendation för en dataresurs

1. Klicka på en resurs på någon av resursflikarna. Informationssidan öppnas med en lista över de rekommendationer som ska åtgärdas.

    ![Resursinformation](./media/security-center-monitoring/sql-recommendations.png)

2. Klicka på en rekommendation. Sidan Rekommendation öppnas och visar **stegen Reparation för** att implementera rekommendationen.

   ![Åtgärder för reparation](./media/security-center-monitoring/remediate1.png)

3. Klicka på **Vidta åtgärd**. Sidan resursinställningar visas.

    ![Aktivera rekommendation](./media/security-center-monitoring/remediate2.png)

4. Följ **stegen För reparation** och klicka på **Spara**.


## <a name="next-steps"></a>Nästa steg

Mer information om rekommendationer som gäller för andra Azure-resurstyper finns i följande avsnitt:

* [Fullständig referenslista över Säkerhetsrekommendationer för Azure Security Center](recommendations-reference.md)
* [Skydda datorer och program i Azure Security Center](security-center-virtual-machine-protection.md)
* [Skydda nätverket i Azure Security Center](security-center-network-recommendations.md)