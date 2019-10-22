---
title: ta med fil
description: ta med fil
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 10/16/2019
ms.openlocfilehash: 0071b0df2c2e173eced1722372f88b1de2708afa
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692389"
---
>[!IMPORTANT]
>Du kan använda de resurser som du har skapat som krav för andra Azure Machine Learning tjänst självstudier och instruktions artiklar.

### <a name="delete-everything"></a>Ta bort allt

Om du inte planerar att använda något som du har skapat tar du bort hela resurs gruppen så att du inte debiteras några avgifter:

1. I Azure Portal väljer du **resurs grupper** på vänster sida av fönstret.
 
   ![Ta bort resursgrupp i Azure-portalen](./media/aml-ui-cleanup/delete-resources.png)

1. I listan väljer du den resurs grupp som du skapade.

1. Välj knappen med tre punkter ( **...** ) på höger sida av fönstret.

1. Välj **Ta bort resursgrupp**.

Om du tar bort resurs gruppen raderas även alla resurser som du skapade i det visuella gränssnittet.  

### <a name="delete-only-the-compute-target"></a>Ta endast bort beräknings målet

Beräknings målet som du skapade här *automatiskt skalar automatiskt* till noll noder när det inte används. Detta är att minimera kostnaderna. Gör så här om du vill ta bort beräknings målet:

1. Öppna arbets ytan i [Azure Portal](https://portal.azure.com).

    ![Ta bort beräknings målet](./media/aml-ui-cleanup/delete-compute-target.png)

1. I avsnittet **Compute** i arbets ytan väljer du resursen.

1. Välj **Ta bort**.

### <a name="delete-individual-assets"></a>Ta bort enskilda till gångar

Ta bort enskilda till gångar i det visuella gränssnittet där du skapade experimentet genom att markera dem och sedan klicka på knappen **ta bort** . Data uppsättningar kan avregistreras från din arbets yta genom att markera varje data uppsättning och välja **avregistrera**.

![Ta bort till gångar](./media/aml-ui-cleanup/delete-asset.png)
