---
title: "Felsöka Windows VM Allokeringsfel | Microsoft Docs"
description: "Felsök tilldelningsproblem när du skapar, startar om eller ändra storlek på en Windows-dator i Azure"
services: virtual-machines-windows, azure-resource-manager
documentationcenter: 
author: JiangChen79
manager: felixwu
editor: 
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: bb939e23-77fc-4948-96f7-5037761c30e8
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2016
ms.author: cjiang
ms.openlocfilehash: a27b728dd902f6230a8e0a029ab69dabdd4cc4aa
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-windows-vms-in-azure"></a>Felsök tilldelningsproblem när du skapar, startar om eller ändra storlek på virtuella Windows-datorer i Azure
När du skapar en virtuell dator, starta om stoppats (frigjorts) virtuella datorer eller ändra storlek på en virtuell dator allokerar beräkningsresurser till din prenumeration på Microsoft Azure. Ibland kan du få felmeddelanden när du utför dessa åtgärder--även innan du når Azure-prenumerationsbegränsningar. Den här artikeln förklarar orsakerna till några vanliga Allokeringsfel och föreslår möjliga reparation. Informationen kan också vara användbart när du planerar distributionen av dina tjänster. Du kan också [Felsök tilldelningsproblem när du skapar, startar om eller ändra storlek på virtuella Linux-datorer i Azure](../linux/allocation-failure.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-allocation-failure](../../../includes/virtual-machines-common-allocation-failure.md)]

