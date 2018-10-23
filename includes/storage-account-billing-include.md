---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/19/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: dfb76a14f7e177211e5e8891005544e20f19d3f3
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638451"
---
Du debiteras för Azure Storage baserat på din användning för storage-konto. Alla objekt i ett lagringskonto faktureras tillsammans som en grupp. 

Lagringskostnaderna beräknas enligt följande faktorer: region/plats, kontotyp, åtkomstnivå, lagringskapacitet, replikeringsschema, lagringstransaktioner och utgående data.

* **Region** refererar till den geografiska region där ditt konto är baserat.
* **Kontotyp** refererar till typ av storage-konto som du använder. 
* **Åtkomstnivå** refererar till det användningsmönstret för data som du har angett för gpv2 eller Blob storage-konto.
* Storage **kapacitet** refererar till hur mycket av din lagringskontots tilldelade utrymme som använder för att lagra data.
* **Replikering** avgör hur många kopior av dina data bevaras i taget och på vilka platser.
* **Transaktioner** avser alla Läs- och skrivåtgärder till Azure Storage.
* **Utgående data** refererar till alla data som överförs från en Azure-region. När data på ditt lagringskonto används av ett program som inte är i samma region så debiteras du för utgående data. Information om hur du använder resursgrupper för att gruppera dina data och tjänster i samma region för att begränsa kostnader för utgående trafik finns i [vad är en Azure-resursgrupp?](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/azure-resource-access#what-is-an-azure-resource-group). 

Sidan [Pris för Azure Storage](https://azure.microsoft.com/pricing/details/storage/) innehåller detaljerad prisinformation baserat på kontotyp, lagringskapacitet, replikering och transaktioner. Sidan [Prisinformation för dataöverföringar](https://azure.microsoft.com/pricing/details/data-transfers/) innehåller detaljerad prisinformation för utgående datatrafik. Du kan använda [priskalkylatorn för Azure Storage](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) för att hjälpa att uppskatta dina kostnader.

