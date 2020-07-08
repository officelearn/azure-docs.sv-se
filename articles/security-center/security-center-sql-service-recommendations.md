---
title: Rekommendationer för data & lagring – Azure Security Center
description: Det här dokumentet beskriver rekommendationer i Azure Security Center som hjälper dig att skydda dina data och Azure SQL-tjänsten och hålla dig informerad om säkerhets principer.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "75552873"
---
# <a name="protect-azure-data-and-storage-services"></a>Skydda Azure-data och lagrings tjänster
När Azure Security Center identifierar potentiella säkerhets risker skapar den rekommendationer som vägleder dig genom processen att konfigurera de nödvändiga kontrollerna för att skärp och skydda dina resurser.

I den här artikeln förklaras **sidan data säkerhet** i avsnittet resurs säkerhet i Security Center.

En fullständig lista över de rekommendationer som du kan se på den här sidan finns i [rekommendationer för data och lagring](recommendations-reference.md#recs-datastorage).


## <a name="view-your-data-security-information"></a>Visa data säkerhets information

1. I avsnittet **resurs säkerhets hygien** klickar du på **data-och lagrings resurser**.

    ![Data & lagrings resurser](./media/security-center-monitoring/click-data.png)

    Sidan **data säkerhet** öppnas med rekommendationer för data resurser.

    [![Data resurser](./media/security-center-monitoring/sql-overview.png)](./media/security-center-monitoring/sql-overview.png#lightbox)

    På sidan kan du:

    * Klicka på fliken **Översikt** visar en lista över alla rekommendationer för data resurser som ska åtgärdas. 
    * Klicka på varje flik och Visa rekommendationerna efter resurs typ.

    > [!NOTE]
    > Mer information om lagrings kryptering finns i [Azure Storage kryptering för vilande data](../storage/common/storage-service-encryption.md).


## <a name="remediate-a-recommendation-on-a-data-resource"></a>Åtgärda en rekommendation på en data resurs

1. Klicka på en resurs från någon av resurs flikarna. Informations sidan öppnas och visar de rekommendationer som ska åtgärdas.

    ![Resursinformation](./media/security-center-monitoring/sql-recommendations.png)

2. Klicka på en rekommendation. Sidan rekommendation öppnas och visar åtgärds **stegen** för att implementera rekommendationen.

   ![Reparationssteg](./media/security-center-monitoring/remediate1.png)

3. Klicka på **vidta åtgärd**. Sidan resurs inställningar visas.

    ![Aktivera rekommendation](./media/security-center-monitoring/remediate2.png)

4. Följ **stegen för reparation** och klicka på **Spara**.


## <a name="next-steps"></a>Nästa steg

Mer information om rekommendationer som gäller för andra typer av Azure-resurser finns i följande avsnitt:

* [Fullständig referens lista över Azure Security Center säkerhets rekommendationer](recommendations-reference.md)
* [Skydda datorer och program i Azure Security Center](security-center-virtual-machine-protection.md)
* [Skydda nätverket i Azure Security Center](security-center-network-recommendations.md)