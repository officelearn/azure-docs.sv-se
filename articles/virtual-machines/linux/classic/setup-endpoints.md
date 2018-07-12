---
title: Konfigurerar du slutpunkter på en klassisk Linux VM | Microsoft Docs
description: Lär dig att konfigurera slutpunkter för en Linux-VM i Azure-portalen för att tillåta kommunikation med en Linux-dator i Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: f3749738-1109-4a1d-8635-40e4bd220e91
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: cynthn
ms.openlocfilehash: 03cb90dcdcc7a7407898ddd8cbc30f1af0833676
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38295695"
---
# <a name="how-to-set-up-endpoints-on-a-linux-classic-virtual-machine-in-azure"></a>Så här konfigurerar du slutpunkter på en Linux klassisk virtuell dator i Azure
Alla Linux-datorer som du skapar i Azure med hjälp av den klassiska distributionsmodellen kan kommunicera automatiskt via en kanal för privata nätverk med andra virtuella datorer i samma molntjänst eller virtuella nätverk. Datorer på Internet eller andra virtuella nätverk kräver dock slutpunkter dirigera inkommande trafik till en virtuell dator. Den här artikeln är också tillgängligt för [Windows virtuella datorer](../../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

I den **Resource Manager** distributionsmodellen slutpunkter konfigureras med hjälp av **Nätverkssäkerhetsgrupper (NSG)**. Mer information finns i [att öppna portar och slutpunkter](../nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

När du skapar en Linux-dator i Azure-portalen skapas en slutpunkt för Secure Shell (SSH) vanligtvis åt dig automatiskt. Du kan konfigurera ytterligare slutpunkter när du skapar den virtuella datorn eller efteråt vid behov.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Nästa steg
* Du kan också skapa en VM-slutpunkt med hjälp av den [Azure-kommandoradsgränssnittet](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2). Kör den **azure virtuell datorslutpunkt skapa** kommando.
* Om du har skapat en virtuell dator i distributionsmodellen för Resource Manager kan du använda Azure CLI i Resource Manager-läge till [skapa nätverkssäkerhetsgrupper](../../../virtual-network/tutorial-filter-network-traffic-cli.md) för trafiken till den virtuella datorn.
