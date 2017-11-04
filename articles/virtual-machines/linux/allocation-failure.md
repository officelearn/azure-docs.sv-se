---
title: "Felsökning av Linux VM Allokeringsfel | Microsoft Docs"
description: "Felsök tilldelningsproblem när du skapar, startar om eller ändra storlek på en Linux VM i Azure"
services: virtual-machines-linux, azure-resource-manager
documentationcenter: 
author: JiangChen79
manager: felixwu
editor: 
tags: top-support-issue,azure-resourece-manager,azure-service-management
ms.assetid: 1ef41144-6dd6-4a56-b180-9d8b3d05eae7
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2016
ms.author: cjiang
ms.openlocfilehash: 8cf421afe9b41af050152dc965d42e7809b5f2d9
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-linux-vms-in-azure"></a>Felsök tilldelningsproblem när du skapar, startar om eller ändra storlek på virtuella Linux-datorer i Azure
När du skapar en virtuell dator, starta om stoppats (frigjorts) virtuella datorer eller ändra storlek på en virtuell dator allokerar beräkningsresurser till din prenumeration på Microsoft Azure. Ibland kan du få felmeddelanden när du utför dessa åtgärder--även innan du når Azure-prenumerationsbegränsningar. Den här artikeln förklarar orsakerna till några vanliga Allokeringsfel och föreslår möjliga reparation. Informationen kan också vara användbart när du planerar distributionen av dina tjänster. Du kan också [Felsök tilldelningsproblem när du skapar, startar om eller ändra storlek på virtuella Windows-datorer i Azure](../windows/allocation-failure.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [virtual-machines-common-allocation-failure](../../../includes/virtual-machines-common-allocation-failure.md)]

