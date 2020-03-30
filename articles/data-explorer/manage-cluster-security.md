---
title: Skydda klustret i Azure Data Explorer
description: I den här artikeln beskrivs hur du skyddar klustret i Azure Data Explorer i Azure-portalen.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 0f935999b68a7283c032d43c42d688b273d5c450
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75720352"
---
# <a name="secure-your-cluster-in-azure-data-explorer---azure-portal"></a>Skydda klustret i Azure Data Explorer - Azure-portal

[Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) hjälper till att skydda och skydda dina data för att uppfylla dina organisatoriska säkerhets- och efterlevnadsåtaganden. Det ger volymkryptering för operativsystem och datadiskar för dina kluster virtuella datorer. Den integreras också med [Azure Key Vault](/azure/key-vault/), vilket gör att vi kan styra och hantera diskkrypteringsnycklar och hemligheter och se till att alla data på VM-diskarna är krypterade. 
  
## <a name="enable-encryption-at-rest-in-the-azure-portal"></a>Aktivera kryptering i vila i Azure-portalen
  
Med säkerhetsinställningarna för klustret kan du aktivera diskkryptering i klustret. Aktivera [kryptering i vila](/azure/security/fundamentals/encryption-atrest) i klustret ger dataskydd för lagrade data (i vila). 

1. Gå till din Azure Data Explorer-klusterresurs i Azure-portalen. Under rubriken **Inställningar** väljer du **Säkerhet**. 

    ![Aktivera kryptering i vila](media/manage-cluster-security/security-encryption-at-rest.png)

1. Välj **På** för säkerhetsinställningen **Diskkryptering** i **säkerhetsfönstret** Säkerhet. 

1. Välj **Spara**.
 
> [!NOTE]
> Välj **Av** om du vill inaktivera krypteringen när den har aktiverats.

## <a name="next-steps"></a>Nästa steg

[Kontrollera klusterhälsa](/azure/data-explorer/check-cluster-health)
