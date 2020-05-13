---
title: Skapa och kryptera en skalnings uppsättning för virtuella datorer med Azure Resource Manager mallar
description: I den här snabb starten får du lära dig hur du använder Azure Resource Manager mallar för att skapa och kryptera en skalnings uppsättning för virtuella datorer
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: c2b49a7e7e14bfbefcca64133ff23fdfabe53e7b
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198428"
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
