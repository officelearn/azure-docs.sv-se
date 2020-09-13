---
title: Hantera resurser som skapas under flyttningen av den virtuella datorn i Azure Resource superprocessen
description: Lär dig hur du hanterar resurser som skapas under flyttnings processen för virtuella datorer i Azure Resource-arbetskraften
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 4ff1905eb164d004af69ce5b0df3278bf3a46884
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2020
ms.locfileid: "89670771"
---
# <a name="manage-resources-created-for-the-vm-move"></a>Hantera resurser som har skapats för flytt av virtuella datorer

Den här artikeln beskriver hur du hanterar resurser som skapas direkt av [Azure Resource-arbetskraften](overview.md) för att under lätta migreringen av den virtuella datorn. 

När du har flyttat virtuella datorer över flera regioner finns det ett antal resurser som har skapats av resurs förflyttning som bör rensas manuellt.

## <a name="delete-resources-created-for-vm-move"></a>Ta bort resurser som har skapats för flytt av virtuella datorer

Ta bort flyttnings samlingen manuellt och Site Recovery resurser som skapats för flyttning av den virtuella datorn.

1. I käll regionen som du flyttade virtuella datorer från granskar du resurserna i resurs gruppen ```RegionMoveRG-<sourceregion>-<target-region>``` .
2. Kontrol lera att den virtuella datorn och alla andra käll resurser i flyttnings samlingen har flyttats/tagits bort. Detta säkerställer att det inte finns några väntande resurser som använder dem.
2. Ta bort dessa resurser.

    - Namnet på flyttnings samlingen är ```movecollection-<sourceregion>-<target-region>``` .
    - Namnet på cachens lagrings konto är ```resmovecache<guid>```
    - Valv namnet är ```ResourceMove-<sourceregion>-<target-region>-GUID``` .

## <a name="next-steps"></a>Nästa steg

Försök att [flytta en virtuell dator](tutorial-move-region-virtual-machines.md) till en annan region med resurs förflyttning.