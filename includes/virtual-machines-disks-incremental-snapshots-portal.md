---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/12/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8f2e2081ee31e2ce7b21677bd261ed7eda705334
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79485926"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>Regional tillgänglighet
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>Begränsningar

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>Portalen

Om du vill skapa och konfigurera inkrementella ögonblicksbilder i [https://aka.ms/incrementalsnapshots](https://aka.ms/incrementalsnapshots)den globala Azure-portalen måste du använda följande länk: . Inkrementell ögonblicksbild skapas är ännu inte tillgänglig i den globala Azure-portalen.

1. Logga in på [Azure-portalen](https://aka.ms/incrementalsnapshots) med den medföljande länken och navigera till den disk som du vill ögonblicksbild.
1. Välj **Skapa en ögonblicksbild på** disken

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text="Skärmdump. Diskens blad, med **+Skapa ögonblicksbild** markerat, eftersom det är vad du måste välja.":::

1. Markera den resursgrupp som du vill använda och ange ett namn.
1. Välj **Inkrementell** och välj **Granska + Skapa**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text="Skärmdump. Skapa ett ögonblicksbildblad, fyll i namnet och välj inkrementell och skapa sedan ögonblicksbilden.":::

1. Välj **Skapa**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text="Skärmdump. Valideringssida för ögonblicksbilden, bekräfta dina val och skapa sedan ögonblicksbilden.":::

## <a name="next-steps"></a>Nästa steg

Om du vill se exempelkod som visar differentiella funktioner för inkrementella ögonblicksbilder, med hjälp av .NET, se [Kopiera Azure Managed Disks säkerhetskopior till en annan region med differentiell kapacitet för inkrementella ögonblicksbilder](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).
