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
ms.date: 10/23/2018
ms.author: cynthn
ms.openlocfilehash: 373003eb8be0482ed96d7d11ecd879237f69b47a
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50957173"
---
# <a name="set-up-endpoints-on-a-windows-virtual-machine-by-using-the-classic-deployment-model"></a>Konfigurerar du slutpunkter på en Windows-dator med hjälp av den klassiska distributionsmodellen
Windows-datorer (VM) som du skapar i Azure med hjälp av den klassiska distributionsmodellen kan kommunicera automatiskt via en kanal för privata nätverk med andra virtuella datorer i samma molntjänst eller virtuella nätverk. Datorer på internet eller andra virtuella nätverk kräver dock slutpunkter dirigera inkommande trafik till en virtuell dator. 

Du kan också ställa in slutpunkter på [Linux-datorer](../../linux/classic/setup-endpoints.md).

> [!IMPORTANT]
> Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser: [Resource Manager och den klassiska distributionsmodellen](../../../resource-manager-deployment-model.md). Den här artikeln beskriver hur du gör om du använder den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.  
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

I den **Resource Manager** distributionsmodellen slutpunkter konfigureras med hjälp av **Nätverkssäkerhetsgrupper (NSG)**. Mer information finns i [Tillåt extern åtkomst till den virtuella datorn med hjälp av Azure portal](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

När du skapar en virtuell Windows-dator i Azure-portalen, skapas vanliga slutpunkter, till exempel slutpunkter för fjärrskrivbord och Windows PowerShell-fjärrkommunikation, vanligtvis åt dig automatiskt. Du kan konfigurera ytterligare slutpunkter vid ett senare tillfälle.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Nästa steg
* Om du vill använda en Azure PowerShell-cmdlet för att ställa in en VM-slutpunkt, se [Add-AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).
* Om du vill använda en Azure PowerShell-cmdlet för att hantera en ACL på en slutpunkt, se [hantera åtkomstkontrollistor (ACL) för slutpunkter med hjälp av PowerShell](../../../virtual-network/virtual-networks-acl-powershell.md).
* Om du har skapat en virtuell dator i distributionsmodellen för Resource Manager kan du använda Azure PowerShell för att [skapa nätverkssäkerhetsgrupper](../../../virtual-network/tutorial-filter-network-traffic.md) för trafiken till den virtuella datorn.
