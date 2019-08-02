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
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "67187696"
---
En aktivitet som körs i Azure Batch kan producera utdata när den körs. Uppgifter om utdata för uppgifter behöver ofta lagras för hämtning av andra uppgifter i jobbet, klient programmet som körde jobbet eller båda. Aktiviteter skriver utdata till fil systemet i en batch-datornod, men alla data på noden förloras när de är avbildade eller när noden lämnar poolen. Aktiviteter kan också ha en kvarhållning av filer, efter vilken filer som skapats av uppgiften tas bort. Av dessa skäl är det viktigt att spara Uppgiftsutdata som du behöver senare till ett data lager, till exempel [Azure Storage](https://docs.microsoft.com/azure/storage/).

För olika alternativ för lagringskonton, se [Översikt över Batch-funktionen](../articles/batch/batch-api-basics.md#azure-storage-account).
