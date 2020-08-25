---
title: inkludera fil
description: inkludera fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/24/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f45ecf9985f17008ce7f751b21a04deefc285461
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/25/2020
ms.locfileid: "88809831"
---
- Endast [program varu-och HSM RSA-nycklar](../articles/key-vault/keys/about-keys.md) med storlekar 2 048-bitar, 3 072-bitars och 4 096-bit stöds, inga andra nycklar eller storlekar.
    - [HSM](../articles/key-vault/keys/hsm-protected-keys.md) -nycklar kräver **Premium** -nivån i Azure Key Vaults.
- Diskar som har skapats från anpassade avbildningar som krypteras med kryptering på Server sidan och Kundhanterade nycklar måste krypteras med samma Kundhanterade nycklar och måste finnas i samma prenumeration.
- Ögonblicks bilder som har skapats från diskar som är krypterade med kryptering på Server sidan och Kundhanterade nycklar måste vara krypterade med samma Kundhanterade nycklar.
- Alla resurser som är relaterade till dina Kundhanterade nycklar (Azure Key Vaults, disk krypterings uppsättningar, virtuella datorer, diskar och ögonblicks bilder) måste finnas i samma prenumeration och region.
- Diskar, ögonblicks bilder och bilder som är krypterade med Kundhanterade nycklar kan inte flyttas till en annan prenumeration.
- Hanterade diskar som för närvarande eller tidigare krypteras med Azure Disk Encryption kan inte krypteras med Kundhanterade nycklar.
- Det går bara att skapa upp till 50 disk krypterings uppsättningar per region per prenumeration.
- Information om hur du använder Kundhanterade nycklar med delade avbildnings gallerier finns i för [hands version: Använd Kundhanterade nycklar för att kryptera avbildningar](../articles/virtual-machines/image-version-encryption.md).
