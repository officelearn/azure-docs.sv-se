---
title: Skapa en vm-avbildning för användare för Azure Marketplace
description: Visar de steg och referenser som krävs för att skapa en avbildning av virtuella användare.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 6bbee7f53cb9a61b72bdbbd941a3a0401f5b913b
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273961"
---
# <a name="create-a-user-vm-image"></a>Skapa en användaravbildning av en virtuell dator

> [!IMPORTANT]
> Från och med den 13 april 2020 börjar vi den rörliga hanteringen av dina Azure Virtual Machine-erbjudanden till Partner Center. Efter migreringen skapar och hanterar du dina erbjudanden i Partner center. Följ instruktionerna i Skapa dina tekniska resurser för [din virtuella Azure-dator](https://aka.ms/AzureVMTechAsset) för att hantera dina migrerade erbjudanden.

I den här artikeln beskrivs de två allmänna stegen som krävs för att skapa en ohanterad avbildning från en allmän virtuell hårddisk.  Referenser finns som guide dig genom varje steg: fånga bilden och generalisera bilden.


## <a name="capture-the-vm-image"></a>Ta bilden av den virtuella datorn

Använd instruktionerna i följande artikel om hur du hämtar den virtuella datorn som motsvarar din åtkomstmetod:

-  PowerShell: [Så här skapar du en ohanterd VM-avbildning från en virtuell Azure-dator](../../../virtual-machines/windows/capture-image-resource.md)
-  Azure CLI: [Så här skapar du en avbildning av en virtuell dator eller virtuell hårddisk](../../../virtual-machines/linux/capture-image.md)
-  API: [Virtuella datorer - Capture](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)


## <a name="generalize-the-vm-image"></a>Generalisera vm-avbildningen

Eftersom du har genererat användaravbildningen från en tidigare generaliserad virtuell hårddisk bör den också generaliseras.  Återigen väljer du följande artikel som motsvarar din åtkomstmekanism.  (Du kanske redan har generaliserat disken när du hämtade den.)

-  PowerShell: [Generalisera den virtuella datorn](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Azure CLI: [Steg 2: Skapa vm-avbildning](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API: [Virtuella datorer - Generalisera](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="next-steps"></a>Nästa steg

Därefter skapar du [ett certifikat](cpp-create-key-vault-cert.md) och lagrar det i ett nytt Azure Key Vault.  Det här certifikatet krävs för att upprätta en säker WinRM-anslutning till den virtuella datorn.
