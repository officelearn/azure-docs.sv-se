---
title: Skapa och kryptera en skalningsuppsättning för virtuella datorer med Azure Resource Manager-mallar
description: I den här snabbstarten får du lära dig hur du använder Azure Resource Manager-mallar för att skapa och kryptera en skalauppsättning för virtuell dator
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: quickstart
ms.date: 10/10/2019
ms.openlocfilehash: aeba9aee8dd10b1245fb0a1e1cc98a1951382c72
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "72530002"
---
# <a name="encrypt-virtual-machine-scale-sets-with-azure-resource-manager"></a>Kryptera skalningsuppsättningar för virtuella datorer med Azure Resource Manager

Du kan kryptera eller dekryptera Linux-skalningsuppsättningar för virtuella datorer med Hjälp av Azure Resource Manager-mallar.

## <a name="deploying-templates"></a>Distribuera mallar

Välj först den mall som passar ditt scenario.

- [Aktivera diskkryptering på en virtuell uppsättning virtuell dator som körs på linuxnivå](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)

- [Aktivera diskkryptering på en windows-skalauppsättning för virtuell dator](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)

  - [Distribuera en uppsättning virtuella linux-datorer med en jumpbox och aktivera kryptering på Linux-skalningsuppsättningar för virtuella datorer](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)

  - [Distribuera en skalningsuppsättning för virtuella datorer med en virtuell dator med en jumpbox och aktivera kryptering på Windows-skalningsuppsättningar för virtuella datorer](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)

- [Inaktivera diskkryptering på en virtuell uppsättning virtuell dator som körs på linuxnivå](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

- [Inaktivera diskkryptering på en windows-skalauppsättning för virtuell dator](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

Följ sedan de här stegen:

     1. Click **Deploy to Azure**.
     2. Fill in the required fields then agree to the terms and conditions.
     3. Click **Purchase** to deploy the template.

## <a name="next-steps"></a>Nästa steg

- [Azure Disk Encryption för skalningsuppsättningar för virtuella datorer](disk-encryption-overview.md)
- [Kryptera en skala för virtuella datorer med Azure CLI](disk-encryption-cli.md)
- [Kryptera en skala för virtuella datorer med Azure PowerShell](disk-encryption-powershell.md)
- [Skapa och konfigurera ett nyckelvalv för Azure DiskKryptering](disk-encryption-key-vault.md)
- [Använda Azure Disk Encryption med sekvensering av tillägg för virtuell datoruppsättning](disk-encryption-extension-sequencing.md)