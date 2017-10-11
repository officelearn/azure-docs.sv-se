---
title: "Skapa en klassisk virtuell Linux-dator med hjälp av Azure CLI 1.0 | Microsoft Docs"
description: "Lär dig hur du skapar en virtuell Linux-dator med Azure CLI-1.0 med hjälp av den klassiska distributionsmodellen"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: f8071a2e-ed91-4f77-87d9-519a37e5364f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
ms.openlocfilehash: 8ddbacbbb70c0cf1a2537fab4d981a316610a4d7
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-create-a-classic-linux-vm-with-the-azure-cli-10"></a>Så här skapar du en klassisk virtuell Linux-dator med Azure CLI 1.0
> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Resource Manager-version finns [här](../create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Det här avsnittet beskriver hur du skapar en Linux-dator (VM) med Azure CLI-1.0 med hjälp av den klassiska distributionsmodellen. Vi använder en Linux-avbildning från de tillgängliga **bilder** på Azure. Azure CLI 1.0-kommandona ger följande konfigurationsalternativ, bland annat:

* Ansluta den virtuella datorn till ett virtuellt nätverk
* Att lägga till den virtuella datorn till en befintlig molntjänst
* Att lägga till den virtuella datorn till ett befintligt lagringskonto
* Lägger till den virtuella datorn till en tillgänglighetsuppsättning eller plats

> [!IMPORTANT]
> Om du vill att den virtuella datorn använder ett virtuellt nätverk så att du kan ansluta till den direkt av värdnamn eller konfigurera anslutningar mellan platser, kontrollera att du anger det virtuella nätverket när du skapar den virtuella datorn. En virtuell dator kan konfigureras för att ansluta ett virtuellt nätverk endast när du skapar den virtuella datorn. Mer information om virtuella nätverk finns [Azure översikt över virtuella nätverk](http://go.microsoft.com/fwlink/p/?LinkID=294063).
> 
> 

## <a name="how-to-create-a-linux-vm-using-the-classic-deployment-model"></a>Så här skapar du en Linux VM som använder den klassiska distributionsmodellen
[!INCLUDE [virtual-machines-create-LinuxVM](../../../../includes/virtual-machines-create-linuxvm.md)]

