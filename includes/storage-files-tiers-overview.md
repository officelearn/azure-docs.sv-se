---
title: inkludera fil
description: inkludera fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e75cb7d13fb74d32191ab7f076d73ad66976503d
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/16/2020
ms.locfileid: "90606178"
---
Azure Files erbjuder fyra olika nivåer av lagring, Premium, transaktion optimerad, frekvent och låg frekvent så att du kan skräddarsy dina resurser efter prestanda-och pris krav för ditt scenario:

- **Premium**: Premium-filresurser backas upp av solid state-hårddiskar (SSD) och distribueras i **FileStorage-lagrings konto** typen. Premium-filresurser ger konsekvent höga prestanda och låg latens i ensiffriga millisekunder för de flesta i/o-åtgärder för i/o-intensiva arbets belastningar. Premium-filresurser lämpar sig för en mängd olika arbets belastningar, t. ex. databaser, webbplatser som är värdar för webbplatser och utvecklings miljöer. Premium fil resurser kan användas med både SMB-(Server Message Block) och NFS-protokoll (Network File System).
- **Transaktion optimerad**: transaktions optimerade fil resurser möjliggör transaktions intensiva arbets belastningar som inte behöver svars tiden som erbjuds av Premium-filresurser. Transaktions optimerade fil resurser erbjuds på den standard lagrings maskin vara som backas upp av hård diskar (HDD) och distribueras i **GPv2-lagrings konto typen (General Purpose version 2)** . Transaktions optimeringen har historiskt sett "standard", men detta refererar till lagrings medie typen i stället för själva nivån (frekvent och låg frekvent är också "standard"-nivåer, eftersom de är på standard lagrings maskin vara).
- Frekvent **: Hot**fil resurser erbjuder lagring som är optimerad för generella fil delnings scenarier, till exempel team resurser och Azure File Sync. Frekventa fil resurser erbjuds den standard lagrings maskin vara som backas upp av hård diskar och distribueras i **GPv2-lagrings konto typen (General Purpose version 2)** .
- Låg **frekvent: låg**frekventa fil resurser erbjuder kostnads effektiv lagring som är optimerad för online-scenarier för Arkiv lag ring. Azure File Sync kan också vara en bra anpassning för mindre omsättnings arbets belastningar. Häftiga fil resurser erbjuds på den standard lagrings maskin vara som backas upp av hård diskar och distribueras i **GPv2-lagrings konto typen (General Purpose version 2)** .

Premium-filresurser är bara tillgängliga i en etablerad fakturerings modell. Mer information om den etablerade fakturerings modellen för Premium-filresurser finns i [förstå etablering för Premium-filresurser](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares). Standard fil resurser, inklusive transaktions optimerings-, frekventa och häftiga fil resurser, är tillgängliga via betala per användning.

Frekventa och häftiga fil resurser är tillgängliga i alla offentliga Azure-och Azure Government regioner. Transaktions optimerade fil resurser är tillgängliga i alla Azure-regioner, inklusive Azure Kina och Azure Germany-regioner.

Information om hur du distribuerar en frekvent eller svag fil resurs finns i [skapa en frekvent eller låg frekvent fil resurs](../articles/storage/files/storage-how-to-create-file-share.md#create-a-hot-or-cool-file-share). 