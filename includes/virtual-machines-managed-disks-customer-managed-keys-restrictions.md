---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/24/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b660d3a0d49de80ed85cfbdcdf8e28b9828cbf26
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2020
ms.locfileid: "91545204"
---
- Endast [program varu-och HSM RSA-nycklar](../articles/key-vault/keys/about-keys.md) med storlekar 2 048-bitar, 3 072-bitars och 4 096-bit stöds, inga andra nycklar eller storlekar.
    - [HSM](../articles/key-vault/keys/hsm-protected-keys.md) -nycklar kräver **Premium** -nivån i Azure Key Vaults.
- Diskar som har skapats från anpassade avbildningar som krypteras med kryptering på Server sidan och Kundhanterade nycklar måste krypteras med samma Kundhanterade nycklar och måste finnas i samma prenumeration.
- Ögonblicks bilder som har skapats från diskar som är krypterade med kryptering på Server sidan och Kundhanterade nycklar måste vara krypterade med samma Kundhanterade nycklar.
- Alla resurser som är relaterade till dina Kundhanterade nycklar (Azure Key Vaults, disk krypterings uppsättningar, virtuella datorer, diskar och ögonblicks bilder) måste finnas i samma prenumeration och region.
- Diskar, ögonblicks bilder och bilder som är krypterade med Kundhanterade nycklar kan inte flyttas till en annan resurs grupp och prenumeration.
- Hanterade diskar som för närvarande eller tidigare krypteras med Azure Disk Encryption kan inte krypteras med Kundhanterade nycklar.
- Det går bara att skapa upp till 50 disk krypterings uppsättningar per region per prenumeration.
- Information om hur du använder Kundhanterade nycklar med delade avbildnings gallerier finns i för [hands version: Använd Kundhanterade nycklar för att kryptera avbildningar](../articles/virtual-machines/image-version-encryption.md).
