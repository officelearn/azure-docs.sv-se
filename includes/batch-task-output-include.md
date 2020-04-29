---
title: ta med fil
description: ta med fil
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 7ba4c90811bd8051ed9c307d9f9fa33e08e69dc7
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2020
ms.locfileid: "67187696"
---
En aktivitet som körs i Azure Batch kan producera utdata när den körs. Uppgifter om utdata för uppgifter behöver ofta lagras för hämtning av andra uppgifter i jobbet, klient programmet som körde jobbet eller båda. Aktiviteter skriver utdata till fil systemet i en batch-datornod, men alla data på noden förloras när de är avbildade eller när noden lämnar poolen. Aktiviteter kan också ha en kvarhållning av filer, efter vilken filer som skapats av uppgiften tas bort. Av dessa skäl är det viktigt att spara Uppgiftsutdata som du behöver senare till ett data lager, till exempel [Azure Storage](https://docs.microsoft.com/azure/storage/).

För olika alternativ för lagringskonton, se [Översikt över Batch-funktionen](../articles/batch/batch-api-basics.md#azure-storage-account).
