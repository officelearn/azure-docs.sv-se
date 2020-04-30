---
title: Skapa och kryptera en skalnings uppsättning för virtuella datorer med Azure Resource Manager mallar
description: I den här snabb starten får du lära dig hur du använder Azure Resource Manager mallar för att skapa och kryptera en skalnings uppsättning för virtuella datorer
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: quickstart
ms.date: 10/10/2019
ms.openlocfilehash: aeba9aee8dd10b1245fb0a1e1cc98a1951382c72
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "72530002"
---
# <a name="encrypt-virtual-machine-scale-sets-with-azure-resource-manager"></a>Kryptera skalnings uppsättningar för virtuella datorer med Azure Resource Manager

Du kan kryptera eller dekryptera skalnings uppsättningar för virtuella Linux-datorer med hjälp av Azure Resource Manager mallar.

## <a name="deploying-templates"></a>Distribuera mallar

Först väljer du den mall som passar ditt scenario.

- [Aktivera disk kryptering på en virtuell dators skalnings uppsättning med virtuella Linux-datorer](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)

- [Aktivera disk kryptering på en virtuell Windows-dator med skal uppsättning](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)

  - [Distribuera en virtuell dators skalnings uppsättning med virtuella Linux-datorer med en hopp och möjliggör kryptering på virtuella Linux-datorers skalnings uppsättningar](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)

  - [Distribuera en virtuell dators skalnings uppsättning med virtuella Windows-datorer med en hopp och möjliggör kryptering på Windows Virtual Machine Scale Sets](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)

- [Inaktivera disk kryptering på en virtuell dators skalnings uppsättning med virtuella Linux-datorer](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

- [Inaktivera disk kryptering på en virtuell Windows-dator med skal uppsättning](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

Följ sedan de här stegen:

     1. Click **Deploy to Azure**.
     2. Fill in the required fields then agree to the terms and conditions.
     3. Click **Purchase** to deploy the template.

## <a name="next-steps"></a>Nästa steg

- [Azure Disk Encryption för skalnings uppsättningar för virtuella datorer](disk-encryption-overview.md)
- [Kryptera en skalnings uppsättning för virtuella datorer med hjälp av Azure CLI](disk-encryption-cli.md)
- [Kryptera en skalnings uppsättning för virtuella datorer med hjälp av Azure PowerShell](disk-encryption-powershell.md)
- [Skapa och konfigurera ett nyckel valv för Azure Disk Encryption](disk-encryption-key-vault.md)
- [Använd Azure Disk Encryption med sekvensering av tillägg för skalnings uppsättning för virtuell dator](disk-encryption-extension-sequencing.md)