---
title: Aktivera dubbel kryptering på rest-Azure Portal-hanterade diskar
description: Aktivera dubbel kryptering i vila för dina hanterade disk data med hjälp av Azure Portal.
author: roygara
ms.date: 07/23/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 7fefaa3b13b73db119da696738af66e84f8bdd63
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2020
ms.locfileid: "87136300"
---
# <a name="azure-portal---enable-double-encryption-at-rest-on-your-managed-disks"></a>Azure Portal – aktivera dubbel kryptering i vila på dina hanterade diskar

Azure-disklagring stöder dubbel kryptering i vila för hanterade diskar. Grundläggande information om dubbel kryptering i vila och andra typer av hanterade disk krypterings typer finns i avsnittet [dubbel kryptering i vilo](disk-encryption.md#double-encryption-at-rest) läge i vår disk krypterings artikel.

[!INCLUDE [virtual-machines-double-encryption-at-rest-portal](../../../includes/virtual-machines-disks-double-encryption-at-rest-portal.md)]

## <a name="next-steps"></a>Nästa steg

- [Azure Resource Manager-mallexempel](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)
- [Aktivera Kundhanterade nycklar med kryptering på Server sidan – exempel](disks-enable-customer-managed-keys-cli.md#examples)