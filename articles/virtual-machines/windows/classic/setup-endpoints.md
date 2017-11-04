---
title: "Konfigurera slutpunkter på en klassiska Windows VM | Microsoft Docs"
description: "Lär dig att konfigurera slutpunkter för en virtuell Windows-dator i den klassiska Azure-portalen för att tillåta kommunikation med en Windows-dator i Azure."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 8afc21c2-d3fb-43a3-acce-aa06be448bb6
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: cynthn
ms.openlocfilehash: 60861819a7e437bb715b14c0e8eaf74f13b33ebf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-set-up-endpoints-on-a-classic-windows-virtual-machine-in-azure"></a>Hur man konfigurerar slutpunkter för klassiska Windows-dator i Azure
Alla Windows automatiskt virtuella datorer som du skapar i Azure med hjälp av den klassiska distributionsmodellen kan kommunicera över en privat nätverkskanalen med andra virtuella datorer i samma molntjänst eller virtuella nätverk. Datorer på Internet eller andra virtuella nätverk måste dock slutpunkter för att dirigera inkommande nätverkstrafik till en virtuell dator. Den här artikeln är också tillgängligt för [virtuella Linux-datorer](../../linux/classic/setup-endpoints.md).

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

I den **Resource Manager** distributionsmodell, slutpunkter konfigureras med hjälp av **Nätverkssäkerhetsgrupper (NSG: er)**. Mer information finns i [ge extern åtkomst till den virtuella datorn med Azure-portalen](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

När du skapar en virtuell Windows-dator i Azure portal skapas vanliga slutpunkter som de för fjärrskrivbord och Windows PowerShell-fjärrkommunikation vanligtvis automatiskt. Du kan konfigurera ytterligare slutpunkter när du skapar den virtuella datorn eller senare efter behov.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Nästa steg
* Om du vill använda Azure PowerShell-cmdlet för att ställa in en VM-slutpunkt, se [Lägg till AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).
* Om du vill använda Azure PowerShell-cmdlet för att hantera en ACL för en slutpunkt, se [hantera åtkomstkontrollistor (ACL) för slutpunkter med hjälp av PowerShell](../../../virtual-network/virtual-networks-acl-powershell.md).
* Om du har skapat en virtuell dator i Resource Manager-distributionsmodellen kan du använda Azure PowerShell för att [skapa nätverk säkerhetsgrupper](../../../virtual-network/virtual-networks-create-nsg-arm-ps.md) för trafiken till den virtuella datorn.
