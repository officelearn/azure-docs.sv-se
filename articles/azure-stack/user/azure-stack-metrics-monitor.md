---
title: Använda övervakningsdata från Azure Stack | Microsoft Docs
description: Läs mer om alternativ för att använda övervakningsdata från Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2018
ms.author: mabrigg
ms.openlocfilehash: 949dee06089ed381010a03ac2fae73aaf4a00c54
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42889821"
---
# <a name="how-to-consume-monitoring-data-from-azure-stack"></a>Hur du använder övervakningsdata från Azure Stack

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Du kan hitta övervakningsdata i en enda plats med Azure Monitor-pipeline, precis som Azure Monitor i globala Azure. Men inte alla övervakningsdata som finns i globala Azure är tillgängligt i Azure Stack. I den här artikeln hittar du en sammanfattning av de olika sätt att du programmässigt kan mata in övervakningsdata från tjänsten.
 
## <a name="options-for-data-consumption"></a>Alternativ för dataförbrukning

| Datatyp | Kategori | Tjänster som stöds | Åtkomstmetoder |
|-------------------------------------------------------------|----------|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Azure Monitor på plattformsnivå-mått | Mått | [Mått som stöds med Azure Monitor på Azure Stack](azure-stack-metrics-supported.md) | REST-API |
| Compute gästmått OS (till exempel prestandaantal) | Mått | Windows och Linux-datorer | Storage-tabell eller blob:<br>Windows eller Linux Azure-diagnostik <br>Händelsehubb:<br>Windows Azure Diagnostics |
| Storage-mått | Mått | Azure Storage | Storage-tabell:<br>Lagringsanalys |
| Aktivitetslogg | Händelser | Alla Azure-tjänster | REST-API:<br>Azure Monitor händelse-API |
| Compute-loggarna för gästoperativsystemet (till exempel IIS, ETW, Syslog-poster) | Händelser | Windows och Linux-datorer | Storage-tabell eller blob:<br>Windows eller Linux Azure-diagnostik <br>Händelsehubb:<br>Windows Azure Diagnostics |
| Storage-loggar | Händelser | Azure Storage | Storage-tabell:<br>Lagringsanalys<br>`Vita: how about hybrid OMS/AppInsights, shall we mention?` |

## <a name="next-steps"></a>Nästa steg

Läs mer om [Azure övervaka på Azure Stack](azure-stack-metrics-azure-data.md).