---
title: Konfigurerar du slutpunkter på en klassisk virtuell Windows-dator | Microsoft Docs
description: Lär dig att konfigurera slutpunkter för en klassisk Windows virtuell dator i Azure-portalen för att tillåta kommunikation med en Windows-dator i Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 8afc21c2-d3fb-43a3-acce-aa06be448bb6
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: cynthn
ms.openlocfilehash: cca9adb40557cf7bf9e1d4129fc6bd61cbf0df4f
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38618247"
---
# <a name="how-to-set-up-endpoints-on-a-classic-windows-virtual-machine-in-azure"></a>Så här konfigurerar du slutpunkter på en klassisk Windows-dator i Azure
Alla Windows virtuella datorer som du skapar i Azure med hjälp av den klassiska distributionsmodellen kan automatiskt kommunicerar via ett privat nätverk kanal med andra virtuella datorer i samma molntjänst eller virtuella nätverk. Datorer på Internet eller andra virtuella nätverk kräver dock slutpunkter dirigera inkommande trafik till en virtuell dator. Den här artikeln är också tillgängligt för [Linux-datorer](../../linux/classic/setup-endpoints.md).

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

I den **Resource Manager** distributionsmodellen slutpunkter konfigureras med hjälp av **Nätverkssäkerhetsgrupper (NSG)**. Mer information finns i [Tillåt extern åtkomst till den virtuella datorn med hjälp av Azure portal](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

När du skapar en virtuell Windows-dator i Azure-portalen, är vanliga slutpunkter som de för fjärrskrivbord och Windows PowerShell-fjärrkommunikation vanligtvis skapas automatiskt. Du kan konfigurera ytterligare slutpunkter när du skapar den virtuella datorn eller efteråt vid behov.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Nästa steg
* Om du vill använda en Azure PowerShell-cmdlet för att ställa in en VM-slutpunkt, se [Add-AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).
* Om du vill använda en Azure PowerShell-cmdlet för att hantera en ACL på en slutpunkt, se [hantera åtkomstkontrollistor (ACL) för slutpunkter med hjälp av PowerShell](../../../virtual-network/virtual-networks-acl-powershell.md).
* Om du har skapat en virtuell dator i distributionsmodellen för Resource Manager kan du använda Azure PowerShell för att [skapa nätverkssäkerhetsgrupper](../../../virtual-network/tutorial-filter-network-traffic.md) för trafiken till den virtuella datorn.
