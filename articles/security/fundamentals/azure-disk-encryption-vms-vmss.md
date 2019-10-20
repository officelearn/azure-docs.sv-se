---
title: Azure Disk Encryption för virtuella datorer och skalnings uppsättningar för virtuella datorer
description: Den här artikeln innehåller en översikt över Azure Disk Encryption
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/15/2019
ms.custom: seodec18
ms.openlocfilehash: 239bfb4f5a0e9115c113bb2d5ddbc931f41c0f3d
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2019
ms.locfileid: "72599960"
---
# <a name="azure-disk-encryption-for-virtual-machines-and-virtual-machine-scale-sets"></a>Azure Disk Encryption för virtuella datorer och skalnings uppsättningar för virtuella datorer

Azure Disk Encryption kan tillämpas på både virtuella Linux-och Windows-datorer, samt för skalnings uppsättningar för virtuella datorer. 

## <a name="linux-virtual-machines"></a>Virtuella Linux-datorer

Följande artiklar innehåller vägledning om hur du krypterar virtuella Linux-datorer.

### <a name="current-version-of-azure-disk-encryption"></a>Aktuell version av Azure Disk Encryption

- [Översikt över Azure Disk Encryption för virtuella Linux-datorer](../../virtual-machines/linux/disk-encryption-overview.md)
- [Azure Disk Encryption scenarier på virtuella Linux-datorer](../../virtual-machines/linux/disk-encryption-linux.md)
- [Skapa och kryptera en virtuell Linux-dator med Azure CLI](../../virtual-machines/linux/disk-encryption-cli-quickstart.md)
- [Skapa och kryptera en virtuell Linux-dator med Azure PowerShell](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)
- [Skapa och kryptera en virtuell Linux-dator med Azure Portal](../../virtual-machines/linux/disk-encryption-portal-quickstart.md)
- [Skapa och konfigurera ett nyckel valv för Azure Disk Encryption](../../virtual-machines/linux/disk-encryption-key-vault.md)
- [Azure Disk Encryption exempel skript](../../virtual-machines/linux/disk-encryption-sample-scripts.md)
- [Azure Disk Encryption fel sökning](../../virtual-machines/linux/disk-encryption-troubleshooting.md)
- [Vanliga frågor och svar om Azure Disk Encryption](../../virtual-machines/linux/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure Disk Encryption med Azure AD (tidigare version)

- [Översikt över Azure Disk Encryption med Azure AD för virtuella Linux-datorer](../../virtual-machines/linux/disk-encryption-overview-aad.md)
- [Azure Disk Encryption med Azure AD-scenarier på virtuella Linux-datorer](../../virtual-machines/linux/disk-encryption-linux.md)
- [Skapa och konfigurera ett nyckel valv för Azure Disk Encryption med Azure AD (tidigare version)](../../virtual-machines/linux/disk-encryption-key-vault-aad.md)

## <a name="windows-virtual-machines"></a>Virtuella Windows-datorer

Följande artiklar innehåller vägledning för att kryptera virtuella Windows-datorer.

### <a name="current-version-of-azure-disk-encryption"></a>Aktuell version av Azure Disk Encryption

- [Översikt över Azure Disk Encryption för virtuella Windows-datorer](../../virtual-machines/windows/disk-encryption-overview.md)
- [Azure Disk Encryption scenarier på virtuella Windows-datorer](../../virtual-machines/windows/disk-encryption-windows.md)
- [Skapa och kryptera en virtuell Windows-dator med Azure CLI](../../virtual-machines/windows/disk-encryption-cli-quickstart.md)
- [Skapa och kryptera en virtuell Windows-dator med Azure PowerShell](../../virtual-machines/windows/disk-encryption-powershell-quickstart.md)
- [Skapa och kryptera en virtuell Windows-dator med Azure Portal](../../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- [Skapa och konfigurera ett nyckel valv för Azure Disk Encryption](../../virtual-machines/windows/disk-encryption-key-vault.md)
- [Azure Disk Encryption exempel skript](../../virtual-machines/windows/disk-encryption-sample-scripts.md)
- [Azure Disk Encryption fel sökning](../../virtual-machines/windows/disk-encryption-troubleshooting.md)
- [Vanliga frågor och svar om Azure Disk Encryption](../../virtual-machines/windows/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure Disk Encryption med Azure AD (tidigare version)

- [Översikt över Azure Disk Encryption med Azure AD för virtuella Windows-datorer](../../virtual-machines/windows/disk-encryption-overview-aad.md)
- [Azure Disk Encryption med Azure AD-scenarier på virtuella Windows-datorer](../../virtual-machines/windows/disk-encryption-windows.md)
- [Skapa och konfigurera ett nyckel valv för Azure Disk Encryption med Azure AD (tidigare version)](../../virtual-machines/windows/disk-encryption-key-vault-aad.md)

## <a name="virtual-machine-scale-sets"></a>VM-skalningsuppsättningar

Följande artiklar innehåller vägledning om hur du krypterar skalnings uppsättningar för virtuella datorer.

- [Översikt över Azure Disk Encryption för skalnings uppsättningar för virtuella datorer](../../virtual-machine-scale-sets/disk-encryption-overview.md) 
- [Kryptera en skalnings uppsättning för virtuella datorer med hjälp av Azure CLI](../../virtual-machine-scale-sets/disk-encryption-cli.md) 
- [Kryptera en skalnings uppsättning för virtuella datorer med hjälp av Azure PowerShell](../../virtual-machine-scale-sets/disk-encryption-powershell.md).
- [Kryptera en skalnings uppsättning för virtuella datorer med hjälp av Azure Resource Manager](../../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
- [Skapa och konfigurera ett nyckel valv för Azure Disk Encryption](../../virtual-machine-scale-sets/disk-encryption-key-vault.md)
- [Använd Azure Disk Encryption med sekvensering av tillägg för skalnings uppsättning för virtuell dator](../../virtual-machine-scale-sets/disk-encryption-extension-sequencing.md)

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure-kryptering](encryption-overview.md)
- [Datakryptering i vila](encryption-atrest.md)
- [Metodtips för datasäkerhet och kryptering](data-encryption-best-practices.md)
