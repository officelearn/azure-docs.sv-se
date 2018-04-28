---
title: Azure Resource Manager-exempelmallar för virtuella nätverk | Microsoft Docs
description: Lär dig om olika Azure Resource Manager-mallar som du kan distribuera virtuella Azure-nätverk med.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/13/2018
ms.author: jdial
ms.openlocfilehash: af0affc549afd8b63fe0d566fac198de054554c1
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="azure-resource-manager-template-samples-for-virtual-network"></a>Azure Resource Manager-exempelmallar för virtuella nätverk

Följande tabell innehåller länkar till Azure Resource Manager-exempelmallar. Du kan distribuera mallar med Azure [Portal](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json), Azure [CLI](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller Azure [PowerShell](../azure-resource-manager/resource-group-template-deploy.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Information om hur du skapar egna mallar finns i [Skapa din första mall](../azure-resource-manager/resource-manager-create-first-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och [Förstå strukturen och syntaxen för Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-network%2ftoc.json).


| | |
|----|----|
|[Skapa ett virtuellt nätverk med två undernät](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets)| Skapar ett virtuellt nätverk med två undernät.|
|[Dirigera trafik via en virtuell nätverksinstallation](https://github.com/Azure/azure-quickstart-templates/tree/master/201-userdefined-routes-appliance)| Skapar ett virtuellt nätverk med tre undernät. Distribuerar en virtuell dator i varje undernät. Skapar en routingtabell som innehåller vägar för att dirigera trafik från ett undernät till ett annat genom den virtuella datorn i det tredje undernätet. Associerar routningstabellen med ett av undernäten.|
|[ Skapa en tjänstslutpunkt för virtuellt nätverk för Azure Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vnet-2subnets-service-endpoints-storage-integration)|Skapar ett nytt virtuellt nätverk med två undernät och ett nätverksgränssnitt i varje undernät. Aktiverar en tjänstslutpunkt till Azure Storage för ett av undernäten och kopplar ett nytt lagringskonto till det undernätet.|
|[Ansluta två virtuella nätverk](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vnet-to-vnet-peering)| Skapar två virtuella nätverk och peering mellan dem.|
|[Skapa en virtuell dator med flera IP-adresser](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-multiple-ipconfig)| Skapar en virtuell Windows- eller Linux-dator med flera IP-adresser.|
