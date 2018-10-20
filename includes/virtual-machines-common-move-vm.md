---
title: ta med fil
description: ta med fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/19/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 61990e785741799fcbcd4e6df965953bd9944f4d
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49476329"
---
## <a name="use-the-azure-portal-to-move-a-vm-to-a-different-subscription"></a>Använd Azure portal för att flytta en virtuell dator till en annan prenumeration
Du kan flytta en virtuell dator och dess kopplade resurser till en annan prenumeration med hjälp av Azure portal.

1. Öppna [Azure-portalen](https://portal.azure.com).
2. Klicka på **Bläddra** > **resursgrupper** och välj den resursgrupp som innehåller den virtuella datorn som du vill flytta.
3. Överst på sidan för resursgruppen väljer **flytta** och välj sedan **flytta till en annan prenumeration**. Den **flytta resurser** öppnas.
4. Välj de resurser att flytta. I de flesta fall bör du flytta alla relaterade resurser som listas.
5. Välj den **prenumeration** där du vill att den virtuella datorn flyttas.
6. Välj en befintlig **resursgrupp**, eller ange ett namn som har en ny resursgrupp skapas.
7. När du är klar, väljer du att du förstår att nya resurs-ID kommer att skapas och att de nya ID: N måste användas med den virtuella datorn när den har flyttats och sedan väljer **OK**.

## <a name="use-the-azure-portal-to-move-a-vm-to-another-resource-group"></a>Använd Azure portal för att flytta en virtuell dator till en annan resursgrupp
Du kan flytta en virtuell dator och dess kopplade resurser till en annan resursgrupp med hjälp av Azure portal.

1. Öppna [Azure-portalen](https://portal.azure.com).
2. Klicka på **Bläddra** > **virtuella datorer** och välj den virtuella datorn som du vill flytta från listan.
3. På sidan för den virtuella datorn bredvid etiketten för resursgrupp, Välj **ändra**. Den **flytta resurser** öppnas.
4. Välj de resurser att flytta. I de flesta fall bör du flytta alla relaterade resurser som listas.
5. Välj en befintlig **resursgrupp**, eller ange ett namn som har en ny resursgrupp skapas.
6. När du är klar, väljer du att du förstår att nya resurs-ID kommer att skapas och att de nya ID: N måste användas med den virtuella datorn när den har flyttats och sedan väljer **OK**.

