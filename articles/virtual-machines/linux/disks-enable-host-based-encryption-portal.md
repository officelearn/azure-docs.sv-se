---
title: Aktivera kryptering från slut punkt till slut punkt med kryptering på värd Azure Portal-hanterade diskar
description: Använd kryptering på värden för att aktivera kryptering från slut punkt till slut punkt på dina Azure Managed disks.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 07/23/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 96506e0ca09e9491135faa69d0b690b91ccc8177
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2020
ms.locfileid: "87136345"
---
# <a name="enable-end-to-end-encryption-using-encryption-at-host---azure-portal"></a>Aktivera kryptering från slut punkt till slut punkt med kryptering på värd-Azure Portal

När du aktiverar kryptering på värden krypteras data som lagras på den virtuella dator värden i vila och flöden krypteras till lagrings tjänsten. För konceptuell information om kryptering på värden och andra typer av hanterade disk krypterings typer, se [kryptering på värd-till-slutpunkt-kryptering för dina VM-data](disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

[!INCLUDE [virtual-machines-disks-encryption-at-host-portal](../../../includes/virtual-machines-disks-encryption-at-host-portal.md)]

## <a name="next-steps"></a>Nästa steg

[Azure Resource Manager-mallexempel](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)