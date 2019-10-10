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
ms.openlocfilehash: 6ad5bafe66e6e39503d3987aa6ddd9159c532fa0
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72237472"
---
Du debiteras för Azure Storage baserat på lagrings kontots användning. Alla objekt i ett lagringskonto faktureras tillsammans som en grupp. 

Lagrings kostnaderna beräknas enligt följande faktorer: region/plats, Kontotyp, åtkomst nivå, lagrings kapacitet, replikeringsschema, lagrings transaktioner och utgående data.

* **Region** syftar på den geografiska region där ditt konto är baserat.
* **Konto typ** avser den typ av lagrings konto som du använder. 
* **Åtkomst nivån** syftar på det data användnings mönster som du har angett för ditt General-Purpose v2-eller Blob Storage-konto.
* Lagrings **kapaciteten** avser hur mycket av lagrings kontots avrop du använder för att lagra data.
* **Replikeringen** avgör hur många kopior av dina data som bevaras samtidigt och på vilka platser.
* **Transaktioner** avser alla Läs-och skriv åtgärder till Azure Storage.
* **Utgående data** syftar på data som överförs från en Azure-region. När data på ditt lagringskonto används av ett program som inte är i samma region så debiteras du för utgående data. Information om hur du använder resurs grupper för att gruppera dina data och tjänster i samma region för att begränsa utgående kostnader finns i [Vad är en Azure-resurs grupp?](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group). 

Sidan [Pris för Azure Storage](https://azure.microsoft.com/pricing/details/storage/) innehåller detaljerad prisinformation baserat på kontotyp, lagringskapacitet, replikering och transaktioner. Sidan [Prisinformation för dataöverföringar](https://azure.microsoft.com/pricing/details/data-transfers/) innehåller detaljerad prisinformation för utgående datatrafik. Du kan använda [priskalkylatorn för Azure Storage](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) för att hjälpa att uppskatta dina kostnader.

