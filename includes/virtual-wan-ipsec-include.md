---
title: inkludera fil
description: inkludera fil
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/07/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 02ced43f8c3fc7c83359b78362e8ad0feeab3070
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "72168387"
---
>[!NOTE]
>När du arbetar med standard principer kan Azure fungera både som initierare och svarare under en konfiguration av en IPsec-tunnel. Det finns inget stöd för Azure som en responder.
>

### <a name="initiator"></a>Initierare

I följande avsnitt listas de kombinationer av principer som stöds när Azure är initieraren för tunneln.

**Fas-1**

* AES_256, SHA1, DH_GROUP_2
* AES_256 SHA_256 DH_GROUP_2
* AES_128, SHA1, DH_GROUP_2
* AES_128 SHA_256 DH_GROUP_2

**Fas-2**

* GCM_AES_256 GCM_AES_256 PFS_NONE
* AES_256 SHA_1 PFS_NONE
* AES_256 SHA_256 PFS_NONE
* AES_128 SHA_1 PFS_NONE

### <a name="responder"></a>Onlinesvarare

I följande avsnitt listas de kombinationer av principer som stöds när Azure är den som svarar på tunneln.

**Fas-1**

* AES_256, SHA1, DH_GROUP_2
* AES_256 SHA_256 DH_GROUP_2
* AES_128, SHA1, DH_GROUP_2
* AES_128 SHA_256 DH_GROUP_2

**Fas-2**

* GCM_AES_256 GCM_AES_256 PFS_NONE
* AES_256 SHA_1 PFS_NONE
* AES_256 SHA_256 PFS_NONE
* AES_128 SHA_1 PFS_NONE
* AES_256 SHA_1 PFS_1
* AES_256 SHA_1 PFS_2
* AES_256 SHA_1 PFS_14
* AES_128 SHA_1 PFS_1
* AES_128 SHA_1 PFS_2
* AES_128 SHA_1 PFS_14
* AES_256 SHA_256 PFS_1
* AES_256 SHA_256 PFS_2
* AES_256 SHA_256 PFS_14
* AES_256 SHA_1 PFS_24
* AES_256 SHA_256 PFS_24
* AES_128 SHA_256 PFS_NONE
* AES_128 SHA_256 PFS_1
* AES_128 SHA_256 PFS_2
* AES_128 SHA_256 PFS_14