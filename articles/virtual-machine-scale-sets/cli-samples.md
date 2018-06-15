---
title: Azure CLI 2.0-exempel | Microsoft Docs
description: Azure CLI 2.0-exempel
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 69fc81d4d0569ee7a66fbda5ab500ef2ee15c694
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
ms.locfileid: "30246397"
---
# <a name="azure-cli-20-samples-for-virtual-machine-scale-sets"></a>Azure CLI 2.0-exempel för VM-skalningsuppsättningar

Följande tabell inkluderar länkar till bash-skript som skapats med Azure CLI 2.0.

| | |
|---|---|
|**Skapa och hantera en skalningsuppsättning**||
| [Skapa en VM-skalningsuppsättning](scripts/cli-sample-create-simple-scale-set.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en VM-skalningsuppsättning med minimal konfiguration. |
| [Skapa en skalningsuppsättning från en anpassad virtuell datoravbildning](scripts/cli-sample-create-scale-set-from-custom-image.md?toc=%2fcli%2fmodule%2ftoc.json) | Skapar en VM-skalningsuppsättning som använder en anpassad virtuell datoravbildning. |
| [Installerar program till en skalningsuppsättning](scripts/cli-sample-install-apps.md?toc=%2fcli%2fmodule%2ftoc.json) | Använd det anpassade Azure-skripttillägget för att installera ett grundläggande webbprogram i en skalningsuppsättning. |
|**Hantera lagring**||
| [Skapa och anslut diskar till en skalningsuppsättning](scripts/cli-sample-attach-disks.md?toc=%2fcli%2fmodule%2ftoc.json) | Skapar en VM-skalningsuppsättning med anslutna datadiskar. |
|**Hantera skala och redundans**||
| [Aktivera värdbaserad automatisk skalning](scripts/cli-sample-enable-autoscale.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en virtuell datorskalning som är konfigurerad att automatiskt skala baserat på CPU-användning. |
| [Skapa en skalningsuppsättning med en zon](scripts/cli-sample-single-availability-zone-scale-set.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en virtuell datorskalning som använder en enda tillgänglighetszon. |
| [Skapa en zonredundant skalningsuppsättning](scripts/cli-sample-zone-redundant-scale-set.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en virtuell datorskala över flera tillgänglighetszoner. |
| | |