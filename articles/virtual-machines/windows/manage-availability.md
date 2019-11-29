---
title: Hantera tillgängligheten för virtuella Windows-datorer i Azure
description: Lär dig hur du använder flera virtuella datorer för att säkerställa hög tillgänglighet för ditt Windows-program i Azure
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 02351953-7b6a-4657-b9e1-de2ea8f6aa05
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/27/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1f9914b84b63f271c7dd7d1b8f7dbc3b69511605
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561195"
---
# <a name="manage-the-availability-of-windows-virtual-machines-in-azure"></a>Hantera tillgängligheten för virtuella Windows-datorer i Azure 

Lär dig hur du konfigurerar och hanterar flera virtuella datorer för att säkerställa hög tillgänglighet för ditt Windows-program i Azure. Du kan också [Hantera tillgängligheten för virtuella Linux-datorer](../linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Anvisningar om hur du skapar och använder tillgänglighets uppsättningar när du använder den klassiska distributions modellen finns i [så här konfigurerar du en tillgänglighets uppsättning](classic/configure-availability-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

[!INCLUDE [virtual-machines-common-manage-availability](../../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>Nästa steg
Mer information om belastnings utjämning för virtuella datorer finns i [belastnings utjämning för virtuella datorer](tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Visa referens arkitekturer för att köra program på N-nivå på SQL Server i IaaS

* [Windows N-Tier-program på Azure med SQL Server](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
* [Köra ett program på N-nivå i flera Azure-regioner för hög tillgänglighet](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)
