---
title: Hantera tillgängligheten för virtuella Linux-datorer i Azure | Microsoft Docs
description: Lär dig hur du använder flera virtuella datorer för att säkerställa hög tillgänglighet för ditt Linux-program i Azure
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
ms.topic: article
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ab269bcd56a5e60fdc8434a58b61163cb8769763
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70082909"
---
# <a name="manage-the-availability-of-linux-virtual-machines"></a>Hantera tillgängligheten för virtuella Linux-datorer

Lär dig hur du konfigurerar och hanterar flera virtuella datorer för att säkerställa hög tillgänglighet för ditt Linux-program i Azure. Du kan också [Hantera tillgängligheten för virtuella Windows-datorer](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Instruktioner för hur du skapar en tillgänglighets uppsättning med CLI i distributions modellen för Resource Manager finns i [Azure availset: kommandon för att hantera dina tillgänglighets uppsättningar](../azure-cli-arm-commands.md#azure-availset-commands-to-manage-your-availability-sets).

[!INCLUDE [virtual-machines-common-manage-availability](../../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>Nästa steg
Mer information om belastnings utjämning för virtuella datorer finns i [belastnings utjämning för virtuella datorer](../virtual-machines-linux-load-balance.md).

