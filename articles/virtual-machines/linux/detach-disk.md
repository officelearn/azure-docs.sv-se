---
title: Koppla från en datadisk från en Linux VM - Azure | Microsoft Docs
description: Lär dig att koppla från en datadisk från en virtuell dator i Azure med hjälp av CLI 2.0 eller Azure-portalen.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: 572fe5bd4d6d79bb9dd94353732e273282e2a0af
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
ms.locfileid: "30903693"
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>Hur du koppla från en datadisk från en virtuell Linux-dator

När du inte längre behöver en datadisk som är ansluten till en virtuell dator kan du enkelt koppla bort den. Detta tar bort disken från den virtuella datorn, men ta bort inte den från lagring. 

> [!WARNING]
> Om du koppla från en disk som den inte tas bort automatiskt. Om du har prenumererat på Premium-lagring, fortsätter du lagring avgifter för disken. Mer information finns i [priser och fakturering när du använder Premiumlagring](../windows/premium-storage.md#pricing-and-billing). 
> 
> 

Om du vill använda befintliga data på disken igen kan du ansluta den igen till samma virtuella dator, eller till en annan.  

## <a name="detach-a-data-disk-using-cli-20"></a>Koppla från en datadisk med hjälp av CLI 2.0

```azurecli
az vm disk detach \
    -g myResourceGroup \
    --vm-name myVm \
    -n myDataDisk
```

Disken finns kvar i lagringsutrymmet men är inte längre kopplad till en virtuell dator.


## <a name="detach-a-data-disk-using-the-portal"></a>Koppla ifrån en datadisk med hjälp av portalen
1. Välj i den vänstra menyn **virtuella datorer**.
2. Välj den virtuella dator som har datadisk som du vill koppla från och klickar på **stoppa** att frigöra den virtuella datorn.
3. Välj i rutan virtuella **diskar**.
4. Längst upp i den **diskar** väljer **redigera**.
5. I den **diskar** rutan längst till höger på disken för data som du vill koppla från, klicka på den ![Detach bilden](./media/detach-disk/detach.png) frånkoppling knappen.
5. När disken har tagits bort, klicka på Spara överst i fönstret.
6. Klicka i den virtuella dator i rutan **översikt** och klicka sedan på den **starta** längst upp för att starta om den virtuella datorn.

Disken finns kvar i lagringsutrymmet men är inte längre kopplad till en virtuell dator.


## <a name="next-steps"></a>Nästa steg
Om du vill återanvända datadisk du just [koppla den till en annan virtuell dator](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

