---
title: Skydda ditt kluster i Azure Datautforskaren
description: Den här artikeln beskriver hur du skyddar klustret i Azure Datautforskaren inom Azure Portal.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 0f935999b68a7283c032d43c42d688b273d5c450
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2020
ms.locfileid: "75720352"
---
# <a name="secure-your-cluster-in-azure-data-explorer---azure-portal"></a>Skydda ditt kluster i Azure Datautforskaren – Azure Portal

[Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) skyddar och skyddar dina data så att de uppfyller organisationens säkerhets-och efterlevnads åtaganden. Den tillhandahåller volym kryptering för operativ system och data diskar för virtuella datorer i klustret. Den integreras också med [Azure Key Vault](/azure/key-vault/), vilket gör att vi kan styra och hantera disk krypterings nycklar och hemligheter och se till att alla data på de virtuella dator diskarna krypteras. 
  
## <a name="enable-encryption-at-rest-in-the-azure-portal"></a>Aktivera kryptering i vila i Azure Portal
  
Med dina kluster säkerhets inställningar kan du aktivera disk kryptering i klustret. Att aktivera [kryptering i vila](/azure/security/fundamentals/encryption-atrest) i klustret ger data skydd för lagrade data (i vila). 

1. I Azure Portal går du till Azure Datautforskaren kluster resursen. Under rubriken **Inställningar** väljer du **säkerhet**. 

    ![Aktivera kryptering i vila](media/manage-cluster-security/security-encryption-at-rest.png)

1. I fönstret **säkerhet** **väljer du för säkerhets** inställningen **disk kryptering** . 

1. Välj **Spara**.
 
> [!NOTE]
> Välj **av** om du vill inaktivera krypteringen när den har Aktiver ATS.

## <a name="next-steps"></a>Nästa steg

[Kontrol lera kluster hälsa](/azure/data-explorer/check-cluster-health)
