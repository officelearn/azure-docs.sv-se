---
title: ta med fil
description: ta med fil
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 11/06/2019
ms.openlocfilehash: 754c9799ed4c2fd90cbcf1e9717b0be21edd54cb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75659888"
---
>[!IMPORTANT]
>Du kan använda de resurser som du har skapat som förutsättningar för andra Azure Machine Learning-självstudier och instruktionsartiklar.

### <a name="delete-everything"></a>Ta bort allt

Om du inte planerar att använda något som du har skapat tar du bort hela resursgruppen så att du inte ådrar dig några avgifter.

1. I Azure-portalen väljer du **Resursgrupper** till vänster i fönstret.
 
   ![Ta bort resursgrupp i Azure-portalen](./media/aml-ui-cleanup/delete-resources.png)

1. Välj den resursgrupp som du skapade i listan.

1. Välj **Ta bort resursgrupp**.

Om du tar bort resursgruppen tas också alla resurser som du har skapat i designern bort. 

### <a name="delete-individual-assets"></a>Ta bort enskilda tillgångar

I designern där du skapade experimentet tar du bort enskilda resurser genom att markera dem och sedan välja knappen **Ta bort.**

Beräkningsmålet som du skapade här *automatiskt skalas automatiskt* till noll noder när det inte används. Den här åtgärden vidtas för att minimera avgifter.Om du vill ta bort beräkningsmålet gör du så här:

![Ta bort tillgångar](./media/aml-ui-cleanup/delete-asset.png)

Du kan avregistrera datauppsättningar från arbetsytan genom att välja varje datauppsättning och välja **Avregistrera**.

![Avregistrera datauppsättning](./media/aml-ui-cleanup/unregister-dataset1225.png)

Om du vill ta bort en datauppsättning går du till lagringskontot med hjälp av Azure-portalen eller Azure Storage Explorer och tar bort dessa resurser manuellt.


