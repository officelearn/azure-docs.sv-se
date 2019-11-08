---
title: Skapa en användar avbildning av en virtuell dator för Azure Marketplace
description: Visar de steg och referenser som krävs för att skapa en användar avbildning av en virtuell dator.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: e63f09dc538c5e66b244826cf3b5f92ac388b6a9
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818702"
---
# <a name="create-a-user-vm-image"></a>Skapa en användaravbildning av en virtuell dator

Den här artikeln beskriver de två allmänna stegen som krävs för att skapa en ohanterad avbildning från en generaliserad virtuell hård disk.  Det finns referenser som vägleder dig genom varje steg: avbilda avbildningen och generalisera avbildningen.


## <a name="capture-the-vm-image"></a>Avbilda avbildningen av den virtuella datorn

Följ anvisningarna i följande artikel om hur du fångar den virtuella datorn som motsvarar din åtkomst metod:

-  PowerShell: [så här skapar du en ohanterad virtuell dator avbildning från en virtuell Azure-dator](../../../virtual-machines/windows/capture-image-resource.md)
-  Azure CLI: [så här skapar du en avbildning av en virtuell dator eller virtuell hård disk](../../../virtual-machines/linux/capture-image.md)
-  API: [Virtual Machines-Capture](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)


## <a name="generalize-the-vm-image"></a>Generalisera VM-avbildningen

Eftersom du har genererat användar avbildningen från en tidigare generaliserad virtuell hård disk bör den också generaliseras.  Välj följande artikel som motsvarar din åtkomst metod.  (Du kanske redan har generaliserat disken när du spelade in den.)

-  PowerShell: [generalisera den virtuella datorn](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Azure CLI: [steg 2: skapa en VM-avbildning](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API: [Virtual Machines-generalize](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="next-steps"></a>Nästa steg

Härnäst ska du [skapa ett certifikat](cpp-create-key-vault-cert.md) och lagra det i en ny Azure Key Vault.  Detta certifikat krävs för att upprätta en säker WinRM-anslutning till den virtuella datorn.
