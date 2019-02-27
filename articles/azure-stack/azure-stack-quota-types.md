---
title: Kvottyper i Azure Stack | Microsoft Docs
description: Visa och redigera olika kvottyper som är tillgängliga för tjänster och resurser i Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/07/2018
ms.author: sethm
ms.reviewer: xiaofmao
ms.lastreviewed: 12/07/2018
ms.openlocfilehash: def9026fb0422d8868af098f12180617aefe7eff
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56877953"
---
# <a name="quota-types-in-azure-stack"></a>Kvottyper i Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

[Kvoter](azure-stack-plan-offer-quota-overview.md#plans) definiera gränserna för resurser som en användarprenumeration kan etablera eller förbrukar. En kvot kan till exempel tillåta en användare kan skapa upp till fem virtuella datorer. Varje resurs kan ha sin egen typer av kvoter.

## <a name="compute-quota-types"></a>Compute kvottyper 

| **Typ** | **Standardvärde** | **Beskrivning** |
| --- | --- | --- |
| Maximalt antal virtuella datorer | 50 | Det maximala antalet virtuella datorer som en prenumeration kan skapa i den här platsen. |
| Högsta antalet virtuella kärnor | 100 | Det maximala antalet kärnor som en prenumeration kan skapa i den här platsen (till exempel en A3 VM har fyra kärnor). |
| Maximalt antal tillgänglighetsuppsättningar | 10 | Det maximala antalet tillgänglighetsuppsättningar som kan skapas i den här platsen. |
| Anger maximalt antal VM-skalningsuppsättning | 100 | Det maximala antalet VM-skalningsuppsättningar som kan skapas på den här platsen. |
| Maximal kapacitet (i GB) för hanterade standarddiskar | 2048 | Maximal kapacitet för hanterade standarddiskar som kan skapas på den här platsen. |
| Maximal kapacitet (i GB) av premium-hanterad disk | 2048 | Den maximala kapaciteten på premium-hanterade diskar som kan skapas på den här platsen. |

## <a name="storage-quota-types"></a>Kvot lagringstyper 

| **Objekt** | **Standardvärde** | **Beskrivning** |
| --- | --- | --- |
| Maximal kapacitet (GB) |2048 |Total lagringskapacitet som kan användas av en prenumeration på den här platsen. |
| Totalt antal lagringskonton |20 |Det maximala antalet lagringskonton som en prenumeration kan skapa i den här platsen. |

> [!NOTE]  
> Det kan ta upp till två timmar innan en lagringskvoten tillämpas.


## <a name="network-quota-types"></a>Nätverkstyper kvot

| **Objekt** | **Standardvärde** | **Beskrivning** |
| --- | --- | --- |
| Maximal offentliga IP-adresser |50 |Det maximala antalet offentliga IP-adresser som en prenumeration kan skapa i den här platsen. |
| Största virtuella nätverk |50 |Det maximala antalet virtuella nätverk som en prenumeration kan skapa i den här platsen. |
| Största virtuella nätverksgatewayer |1 |Det maximala antalet virtuella nätverksgatewayer (VPN-gatewayer) som en prenumeration kan skapa i den här platsen. |
| Maximal nätverksanslutningar |2 |Det maximala antalet anslutningar (point-to-point eller plats-till-plats) som en prenumeration kan du skapa över alla virtuella nätverksgatewayer på den här platsen. |
| Maximal belastningsutjämnare |50 |Det maximala antalet belastningsutjämnare som en prenumeration kan skapa i den här platsen. |
| Maximal nätverkskort |100 |Det maximala antalet nätverksgränssnitt som en prenumeration kan skapa i den här platsen. |
| Maximal nätverkssäkerhetsgrupper |50 |Det maximala antalet nätverkssäkerhetsgrupper som en prenumeration kan skapa i den här platsen. |

## <a name="view-an-existing-quota"></a>Visa en befintlig kvot

Det finns två olika sätt att visa en befintlig kvot:

### <a name="plans"></a>Planer

1.  I det vänstra navigeringsfönstret i administratörsportalen väljer **planer**.
2.  Välj den plan som du vill visa information om, genom att klicka på dess namn.
3.  I bladet som öppnas väljer du **tjänster och kvoter**.
4.  Välj den kvot som du skulle vilja se genom att klicka på den i den **namn** kolumn.

    [![Kvoter](media/azure-stack-quota-types/quotas1sm.png "visa kvoter")](media/azure-stack-quota-types/quotas1.png#lightbox)

### <a name="resource-providers"></a>Resursproviders

1. På standardinstrumentpanelen för premiumkapacitetshantering hitta den **resursprovidrar** panelen.
2. Välj tjänsten med den kvot som du vill visa som **Compute**, **nätverk**, eller **Storage**.
3. Välj **kvoter**, och välj sedan den kvot som du vill visa.

## <a name="edit-a-quota"></a>Redigera en kvot

Det finns två sätt att redigera en kvot:

### <a name="edit-a-plan"></a>Redigera en plan

1.  I det vänstra navigeringsfönstret i administratörsportalen väljer **planer**.
2.  Välj den plan som du vill redigera en kvot genom att klicka på dess namn.
3.  I bladet som öppnas väljer du **tjänster och kvoter**.
4.  Välj den kvot som du vill redigera genom att klicka på den i den **namn** kolumn.
    [![Kvoter](media/azure-stack-quota-types/quotas1sm.png "visa kvoter")](media/azure-stack-quota-types/quotas1.png#lightbox)

5.  I bladet som öppnas väljer du **redigera i beräkning**, **redigera i nätverket**, eller **redigera i Storage**.
    ![Kvoter](media/azure-stack-quota-types/quotas3.png "visa kvoter")    

Alternativt kan du följa den här proceduren om du vill redigera en kvot:

1. På standardinstrumentpanelen i portalen för administratören att hitta den **resursprovidrar** panelen.
2. Välj tjänsten med den kvot som du vill ändra som **Compute**, **nätverk**, eller **Storage**.
3. Välj sedan **kvoter**, och välj sedan den kvot som du vill ändra.
4. På den **ange lagringskvoter**, **ange Compute kvoter**, eller **ange nätverkskvoter** (beroende på vilken typ av kvot som du har valt att redigera), redigera och välj sedan **Spara**.

### <a name="edit-original-configuration"></a>Redigera originalkonfigurationen
  
Du kan välja att redigera den ursprungliga konfigurationen av en kvot i stället för [med hjälp av en tilläggsplanen](create-add-on-plan.md). När du redigerar en kvot gäller den nya konfigurationen automatiskt globalt för alla planer som använder den kvoten och alla befintliga prenumerationer som använder dessa planer. Redigering av en kvot skiljer sig från när du använder en tilläggsplan för att tillhandahålla en ändrad kvot som en användare väljer att prenumerera på. 

De nya värdena för kvoten tillämpas globalt för alla planer som använder den ändrade kvoten och för alla befintliga prenumerationer som använder dessa planer. 

## <a name="next-steps"></a>Nästa steg

- [Läs mer om planer, erbjudanden och kvoter.](azure-stack-plan-offer-quota-overview.md)
- [Skapa kvoter när du skapar en plan.](azure-stack-create-plan.md)
