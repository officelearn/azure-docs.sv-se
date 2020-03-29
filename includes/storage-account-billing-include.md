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
ms.openlocfilehash: cdcbe993bd1100b2060a1f8d38eb82ac97121c0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74851644"
---
Du debiteras för Azure Storage baserat på användning av ditt lagringskonto. Alla objekt i ett lagringskonto faktureras tillsammans som en grupp. 

Lagringskostnaderna beräknas enligt följande faktorer: 

* **Region** refererar till det geografiska område där ditt konto är baserat.
* **Kontotyp** avser vilken typ av lagringskonto du använder. 
* **Åtkomstnivån** refererar till det dataanvändningsmönster som du har angett för ditt allmänna v2- eller Blob-lagringskonto.
* **Lagringskapacitet** avser hur mycket av tilldelningen av lagringskonto du använder för att lagra data.
* **Replikeringen** avgör hur många kopior av dina data som underhålls samtidigt och på vilka platser.
* **Transaktioner** refererar till alla läs- och skrivåtgärder till Azure Storage.
* **Data utgående** avser alla data som överförs från en Azure-region. När data i ditt lagringskonto används av ett program som inte körs i samma region debiteras du för datautgående. Information om hur du använder resursgrupper för att gruppera data och tjänster i samma region för att begränsa utgående avgifter finns i [Vad är en Azure-resursgrupp?](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group). 

Sidan [Pris för Azure Storage](https://azure.microsoft.com/pricing/details/storage/) innehåller detaljerad prisinformation baserat på kontotyp, lagringskapacitet, replikering och transaktioner. Sidan [Prisinformation för dataöverföringar](https://azure.microsoft.com/pricing/details/data-transfers/) innehåller detaljerad prisinformation för utgående datatrafik. Du kan använda [priskalkylatorn för Azure Storage](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) för att hjälpa att uppskatta dina kostnader.

