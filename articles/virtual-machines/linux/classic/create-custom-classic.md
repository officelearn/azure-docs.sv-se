---
title: Skapa en klassisk virtuell Linux-dator med hjälp av Azure CLI för klassiska | Microsoft Docs
description: Lär dig hur du skapar en Linux-dator med klassiska Azure CLI med hjälp av den klassiska distributionsmodellen
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: f8071a2e-ed91-4f77-87d9-519a37e5364f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: cynthn
ms.openlocfilehash: 507d9e12a37d7bf187a3e56b04cb47ac0104773d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982057"
---
# <a name="how-to-create-a-classic-linux-vm-with-the-azure-classic-cli"></a>Så här skapar du en klassisk virtuell Linux-dator med den klassiska Azure CLI
> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Resource Manager-version finns [här](../create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Det här avsnittet beskriver hur du skapar en Linux-dator (VM) med klassiska Azure CLI med hjälp av den klassiska distributionsmodellen. Vi använder en Linux-avbildning från de tillgängliga **AVBILDNINGAR** på Azure. Azure klassiska CLI-kommandon ger följande konfigurationsalternativ, bland annat:

* Ansluta den virtuella datorn till ett virtuellt nätverk
* Att lägga till den virtuella datorn till en befintlig molntjänst
* Att lägga till den virtuella datorn till ett befintligt lagringskonto
* Lägga till den virtuella datorn till en tillgänglighetsuppsättning eller plats

> [!IMPORTANT]
> Om du vill att den virtuella datorn ska använda ett virtuellt nätverk så att du kan ansluta till den direkt av värdnamn eller konfigurera anslutningar mellan platser, kontrollera att du anger det virtuella nätverket när du skapar den virtuella datorn. En virtuell dator kan konfigureras för att ansluta till ett virtuellt nätverk endast när du skapar den virtuella datorn. Mer information om virtuella nätverk finns [Azure översikt över Virtual Network](http://go.microsoft.com/fwlink/p/?LinkID=294063).
> 
> 

## <a name="how-to-create-a-linux-vm-using-the-classic-deployment-model"></a>Så här skapar du en Linux VM med hjälp av den klassiska distributionsmodellen
[!INCLUDE [virtual-machines-create-LinuxVM](../../../../includes/virtual-machines-create-linuxvm.md)]

