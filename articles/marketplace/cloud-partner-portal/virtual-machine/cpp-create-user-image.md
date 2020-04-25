---
title: Skapa en användar avbildning av en virtuell dator för Azure Marketplace
description: Visar de steg och referenser som krävs för att skapa en användar avbildning av en virtuell dator.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 9d82d50769925480d461c122096c3919d7e8940d
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82146573"
---
# <a name="create-a-user-vm-image"></a>Skapa en användaravbildning av en virtuell dator

> [!IMPORTANT]
> Från och med 13 april 2020 börjar vi flytta hanteringen av din virtuella Azure-dator till Partner Center. Efter migreringen kommer du att skapa och hantera dina erbjudanden i Partner Center. Följ anvisningarna i [skapa tekniska till gång till Azure-datorer](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) för att hantera dina migrerade erbjudanden.

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
