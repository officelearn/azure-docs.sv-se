---
title: Konfigurera slutpunkter på en klassisk Linux VM | Microsoft Docs
description: Lär dig att konfigurera slutpunkter för en Linux-VM i Azure-portalen för att tillåta kommunikation med en virtuell Linux-dator i Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: timlt
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
ms.openlocfilehash: a37c70a0a304ecfa0c3ce205451f456948593ebe
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="how-to-set-up-endpoints-on-a-linux-classic-virtual-machine-in-azure"></a>Hur du ställer in slutpunkter på en Linux klassiska virtuell dator i Azure
Alla Linux virtuella datorer som du skapar i Azure med hjälp av den klassiska distributionsmodellen kan automatiskt kommunicera via en kanal för privat nätverk med andra virtuella datorer i samma molntjänst eller virtuella nätverk. Datorer på Internet eller andra virtuella nätverk måste dock slutpunkter för att dirigera inkommande nätverkstrafik till en virtuell dator. Den här artikeln är också tillgängligt för [virtuella Windows-datorer](../../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

I den **Resource Manager** distributionsmodell, slutpunkter konfigureras med hjälp av **Nätverkssäkerhetsgrupper (NSG: er)**. Mer information finns i [öppna portar och slutpunkter](../nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

När du skapar en virtuell Linux-dator i Azure-portalen kan skapas en slutpunkt för SSH (Secure Shell) vanligtvis automatiskt. Du kan konfigurera ytterligare slutpunkter när du skapar den virtuella datorn eller senare efter behov.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Nästa steg
* Du kan också skapa en VM-slutpunkt med hjälp av den [Azure-kommandoradsgränssnittet](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2). Kör den **azure vm endpoint skapa** kommando.
* Om du har skapat en virtuell dator i Resource Manager-distributionsmodellen, du kan använda Azure CLI i Resource Manager-läge till [skapa nätverk säkerhetsgrupper](../../../virtual-network/tutorial-filter-network-traffic-cli.md) för trafiken till den virtuella datorn.
