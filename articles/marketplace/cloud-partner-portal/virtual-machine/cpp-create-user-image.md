---
title: Skapa en användare VM-avbildning för Azure Marketplace
description: Visar en lista över stegen och referenser som krävs för att skapa en VM-avbildning för användaren.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: 0005ab517d38903b87889b67449569495e396265
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64938320"
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
