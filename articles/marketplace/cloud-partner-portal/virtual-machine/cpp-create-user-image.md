---
title: Skapa en användare VM-avbildning för Azure Marketplace | Microsoft Docs
description: Visar en lista över stegen och referenser som krävs för att skapa en VM-avbildning för användaren.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/29/2018
ms.author: pbutlerm
ms.openlocfilehash: bf87856dc28e83fb1308f20613338b9bbfd8f896
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744116"
---
# <a name="create-a-user-vm-image"></a>Skapa en användaravbildning av en virtuell dator

Den här artikeln beskrivs två allmänna steg som krävs för att skapa en ohanterad avbildning från en generaliserad virtuell Hårddisk.  Referenser som leder dig igenom varje steg: infångar avbildningen och generalisera avbildningen.


## <a name="capture-the-vm-image"></a>Skapa VM-avbildning

Följ instruktionerna i följande artikel om hur du fångar den virtuella datorn som motsvarar ditt åtkomst-metoden:

-  PowerShell: [Hur du skapar en ohanterad VM-avbildning från en Azure-dator](../../../virtual-machines/windows/capture-image-resource.md)
-  Azure CLI: [Så här skapar du en avbildning av en virtuell dator eller virtuell Hårddisk](../../../virtual-machines/linux/capture-image.md)
-  API: [VM - avbildning](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)


## <a name="generalize-the-vm-image"></a>Generalisera avbildningen

Eftersom du har genererat användaravbildningen från en tidigare generaliserad virtuell Hårddisk, måste det också vara generaliserat.  Välj igen, i följande artikel som motsvarar ditt åtkomstmekanism.  (Du kanske har redan generaliserats hårddisken när du har hämtat.)

-  PowerShell: [Generalisera den virtuella datorn](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Azure CLI: [Steg 2: Skapa VM-avbildning](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API: [Generalisera virtuella datorer-](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="next-steps"></a>Nästa steg

Sedan kommer du att [skapar du ett certifikat](cpp-create-key-vault-cert.md) och lagra den i en ny Azure Key Vault.  Detta certifikat krävs för att upprätta en säker WinRM-anslutning till den virtuella datorn.
