---
title: Aktivera kryptering från slut punkt till slut punkt med kryptering på värd Azure Portal-hanterade diskar
description: Använd kryptering på värden för att aktivera kryptering från slut punkt till slut punkt på dina Azure Managed disks-Azure Portal.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 07/23/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: e969fe34e7e7723218586f24807ad70944ba5716
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2020
ms.locfileid: "87171369"
---
# <a name="enable-end-to-end-encryption-using-encryption-at-host---azure-portal"></a>Aktivera kryptering från slut punkt till slut punkt med kryptering på värd-Azure Portal

När du aktiverar kryptering på värden krypteras data som lagras på den virtuella dator värden i vila och flöden krypteras till lagrings tjänsten. För konceptuell information om kryptering på värden och andra typer av hanterade disk krypterings typer, se [kryptering på värd-till-slutpunkt-kryptering för dina VM-data](disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

[!INCLUDE [virtual-machines-disks-encryption-at-host-portal](../../../includes/virtual-machines-disks-encryption-at-host-portal.md)]

## <a name="next-steps"></a>Nästa steg

[Azure Resource Manager-mallexempel](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)