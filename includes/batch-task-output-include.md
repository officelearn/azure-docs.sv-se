---
title: ta med fil
description: ta med fil
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: eaa76153fe96b5fd41166b20770e0a969aa9260d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "83797133"
---
En aktivitet som körs i Azure Batch kan producera utdata när den körs. Uppgifter om utdata för uppgifter behöver ofta lagras för hämtning av andra uppgifter i jobbet, klient programmet som körde jobbet eller båda. Aktiviteter skriver utdata till fil systemet i en batch-datornod, men alla data på noden förloras när de är avbildade eller när noden lämnar poolen. Aktiviteter kan också ha en kvarhållning av filer, efter vilken filer som skapats av uppgiften tas bort. Av dessa skäl är det viktigt att spara Uppgiftsutdata som du behöver senare till ett data lager, till exempel [Azure Storage](https://docs.microsoft.com/azure/storage/).

För lagrings konto alternativ i batch, se [batch-konton och Azure Storage konton](../articles/batch/accounts.md#azure-storage-accounts).
