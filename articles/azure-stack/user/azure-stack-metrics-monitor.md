---
title: Använda övervakningsdata från Azure Stack | Microsoft Docs
description: Läs mer om alternativ för användning av övervakningsdata från Azure Stack.
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
ms.date: 12/01/2018
ms.author: mabrigg
ms.lastreviewed: 12/01/2018
ms.openlocfilehash: 54d12cc709c9579fcd056bef22bdf767c81f8e61
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55246957"
---
# <a name="how-to-consume-monitoring-data-from-azure-stack"></a>Hur du använder övervakningsdata från Azure Stack

*Gäller för: Integrerade Azure Stack-system*

Du kan hitta övervakningsdata i en enda plats med Azure Monitor-pipeline, precis som Azure Monitor i globala Azure. Men inte alla övervakningsdata som finns i globala Azure är tillgängligt i Azure Stack. I den här artikeln hittar du en sammanfattning av de olika sätt att du programmässigt kan mata in övervakningsdata från tjänsten.
 
## <a name="options-for-data-consumption"></a>Alternativ för dataförbrukning

| Datatyp | Kategori | Tjänster som stöds | Åtkomstmetoder |
|-------------------------------------------------------------|----------|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Azure Monitor på plattformsnivå-mått | Mått | [Mått som stöds med Azure Monitor på Azure Stack](azure-stack-metrics-supported.md) | REST-API |
| Compute gästmått OS (till exempel prestandaantal) | Mått | Windows och Linux-datorer | Storage-tabell eller blob:<br>Windows eller Linux Azure-diagnostik <br>Event Hub:<br>Windows Azure Diagnostics |
| Storage-mått | Mått | Azure Storage | Storage-tabell:<br>Lagringsanalys |
| Aktivitetslogg | Händelser | Alla Azure-tjänster | REST-API:<br>Azure Monitor händelse-API |
| Compute-loggarna för gästoperativsystemet (till exempel IIS, ETW, Syslog-poster) | Händelser | Windows och Linux-datorer | Storage-tabell eller blob:<br>Windows eller Linux Azure-diagnostik <br>Event Hub:<br>Windows Azure Diagnostics |
| Storage-loggar | Händelser | Azure Storage | Storage-tabell:<br>Lagringsanalys |

## <a name="next-steps"></a>Nästa steg

Läs mer om [Azure övervaka på Azure Stack](azure-stack-metrics-azure-data.md).
