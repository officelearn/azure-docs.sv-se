---
title: Kvottyper i Azure Stack | Microsoft Docs
description: Granska de olika kvottyper som är tillgängliga för tjänster och resurser i Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/27/2018
ms.author: brenduns
ms.reviewer: xiaofmao
ms.openlocfilehash: 172c32c1f3796ec95336543d7d0ea149e63cfb22
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39331146"
---
# <a name="quota-types-in-azure-stack"></a>Kvottyper i Azure Stack

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

[Kvoter](azure-stack-plan-offer-quota-overview.md#plans) definiera gränserna för resurser som en användarprenumeration kan etablera eller förbrukar. En kvot kan till exempel tillåta en användare kan skapa upp till fem virtuella datorer. Varje resurs kan ha sin egen typer av kvoter.

## <a name="compute-quota-types"></a>Compute kvottyper

| **Typ** | **Standardvärde** | **Beskrivning** |
| --- | --- | --- |
| Maxantal virtuella datorer | 20 | Det maximala antalet virtuella datorer som en prenumeration kan skapa i den här platsen. |
| Maxantal virtuella kärnor | 50 | Det maximala antalet kärnor som en prenumeration kan skapa i den här platsen (till exempel en A3 VM har fyra kärnor). |
| Max antal tillgänglighetsuppsättningar | 10 | Det maximala antalet tillgänglighetsuppsättningar som kan skapas i den här platsen. |
| Max antal VM-skalningsuppsättningar | 20 | Det maximala antalet VM-skalningsuppsättningar som kan skapas på den här platsen. |

## <a name="storage-quota-types"></a>Kvot lagringstyper

| **Objekt** | **Standardvärde** | **Beskrivning** |
| --- | --- | --- |
| Maximal kapacitet (GB) |500 |Total lagringskapacitet som kan användas av en prenumeration på den här platsen. |
| Totalt antal lagringskonton |20 |Det maximala antalet lagringskonton som en prenumeration kan skapa i den här platsen. |

> [!NOTE]  
> Det kan ta upp till två timmar innan en lagringskvoten tillämpas.

## <a name="network-quota-types"></a>Nätverkstyper kvot

| **Objekt** | **Standardvärde** | **Beskrivning** |
| --- | --- | --- |
| Maximalt antal offentliga IP-adresser |50 |Det maximala antalet offentliga IP-adresser som en prenumeration kan skapa i den här platsen. |
| Maximalt antal virtuella nätverk |50 |Det maximala antalet virtuella nätverk som en prenumeration kan skapa i den här platsen. |
| Maximalt antal virtuella nätverksgatewayer |1 |Det maximala antalet virtuella nätverksgatewayer (VPN-gatewayer) som en prenumeration kan skapa i den här platsen. |
| Maximalt antal anslutningar |2 |Det maximala antalet anslutningar (point-to-point eller plats-till-plats) som en prenumeration kan du skapa över alla virtuella nätverksgatewayer på den här platsen. |
| Max belastningsutjämnare |50 |Det maximala antalet belastningsutjämnare som en prenumeration kan skapa i den här platsen. |
| Maximalt antal nätverkskort |100 |Det maximala antalet nätverksgränssnitt som en prenumeration kan skapa i den här platsen. |
| Maximalt antal nätverkssäkerhetsgrupper |50 |Det maximala antalet nätverkssäkerhetsgrupper som en prenumeration kan skapa i den här platsen. |

## <a name="view-an-existing-quota"></a>Visa en befintlig kvot

1. På standardinstrumentpanelen för premiumkapacitetshantering hitta den **resursprovidrar** panelen.
2. Välj tjänsten med den kvot som du vill visa som **Compute** eller **Storage**.
3. Välj **kvoter**, och välj sedan den kvot som du vill visa.

## <a name="next-steps"></a>Nästa steg

- [Läs mer om planer, erbjudanden och kvoter.](azure-stack-plan-offer-quota-overview.md)
- [Skapa kvoter när du skapar en plan.](azure-stack-create-plan.md)
