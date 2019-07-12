---
title: Hantera tillgängligheten för virtuella Linux-datorer i Azure | Microsoft Docs
description: Lär dig hur du använder flera virtuella datorer för att säkerställa hög tillgänglighet för ditt program för Linux i Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 891c852a-84c0-4940-a61e-ada6e185bf37
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 914efbed2d65468cbbbccb3f62f9c21b52c38e42
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667696"
---
# <a name="manage-the-availability-of-linux-virtual-machines"></a>Hantera tillgängligheten för virtuella Linux-datorer

Lär dig hur du konfigurerar och hanterar flera virtuella datorer för att säkerställa hög tillgänglighet för ditt program för Linux i Azure. Du kan också [hantera tillgängligheten för Windows-datorer](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Anvisningar om hur du skapar en tillgänglighetsuppsättning med hjälp av CLI i Resource Manager-distributionsmodellen finns i [azure availset: kommandon för att hantera dina tillgänglighetsuppsättningar](../azure-cli-arm-commands.md#azure-availset-commands-to-manage-your-availability-sets).

[!INCLUDE [virtual-machines-common-manage-availability](../../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>Nästa steg
Mer information om belastningsutjämning för dina virtuella datorer finns [belastningsutjämna virtuella datorer](../virtual-machines-linux-load-balance.md).

