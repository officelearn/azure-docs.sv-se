---
title: ta med fil
description: ta med fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7d0286b63703c165dda6cd12bb625fc64272aac1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011781"
---
Azure Files erbjuder fyra olika nivåer av lagring, Premium, transaktion optimerad, frekvent och låg frekvent så att du kan skräddarsy dina resurser efter prestanda-och pris krav för ditt scenario:

- **Premium**: Premium-filresurser backas upp av solid state-hårddiskar (SSD) och distribueras i **FileStorage-lagrings konto** typen. Premium-filresurser ger konsekvent höga prestanda och låg latens i ensiffriga millisekunder för de flesta i/o-åtgärder för i/o-intensiva arbets belastningar. Premium-filresurser lämpar sig för en mängd olika arbets belastningar, t. ex. databaser, webbplatser som är värdar för webbplatser och utvecklings miljöer. Premium fil resurser kan användas med både SMB-(Server Message Block) och NFS-protokoll (Network File System).
- **Transaktion optimerad**: transaktions optimerade fil resurser möjliggör transaktions intensiva arbets belastningar som inte behöver svars tiden som erbjuds av Premium-filresurser. Transaktions optimerade fil resurser erbjuds på den standard lagrings maskin vara som backas upp av hård diskar (HDD) och distribueras i **GPv2-lagrings konto typen (General Purpose version 2)** . Transaktions optimeringen har historiskt sett "standard", men detta refererar till lagrings medie typen i stället för själva nivån (frekvent och låg frekvent är också "standard"-nivåer, eftersom de är på standard lagrings maskin vara).
- Frekvent **: Hot** fil resurser erbjuder lagring som är optimerad för generella fil delnings scenarier, till exempel team resurser och Azure File Sync. Frekventa fil resurser erbjuds den standard lagrings maskin vara som backas upp av hård diskar och distribueras i **GPv2-lagrings konto typen (General Purpose version 2)** .
- Låg **frekvent: låg** frekventa fil resurser erbjuder kostnads effektiv lagring som är optimerad för online-scenarier för Arkiv lag ring. Azure File Sync kan också vara en bra anpassning för mindre omsättnings arbets belastningar. Häftiga fil resurser erbjuds på den standard lagrings maskin vara som backas upp av hård diskar och distribueras i **GPv2-lagrings konto typen (General Purpose version 2)** .

Premium-filresurser är bara tillgängliga i en etablerad fakturerings modell. Mer information om den etablerade fakturerings modellen för Premium-filresurser finns i [förstå etablering för Premium-filresurser](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares). Standard fil resurser, inklusive transaktions optimerings-, frekventa och häftiga fil resurser, är tillgängliga via betala per användning.

Frekventa och häftiga fil resurser är tillgängliga i alla offentliga Azure-och Azure Government regioner. Transaktions optimerade fil resurser är tillgängliga i alla Azure-regioner, inklusive Azure Kina och Azure Germany-regioner.

> [!Important]  
> Du kan flytta fil resurser mellan nivåer inom GPv2 lagrings konto typer (transaktion optimerad, frekvent och låg frekvent). Delnings åtgärder mellan nivåer medför transaktioner: om du flyttar från en Hotter-nivå till en låg frekvent nivå kommer den avbilds nivå som är låg för varje fil i resursen, medan en flytt från en låg frekvent nivå till en Hotter-nivå kommer att medföra den låg frekventa transaktions avgiften för varje fil resursen.