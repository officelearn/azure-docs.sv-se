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
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187696"
---
En aktivitet som körs i Azure Batch kan producera utdata när den körs. Uppgift utdata data behöver ofta lagras för hämtning av andra aktiviteter i jobbet, det klientprogram som körs jobbet eller båda. Uppgifter skriva utdata till filsystemet för en Batch-beräkningsnod, men alla data på noden förloras när den återställs eller när nod lämnar poolen. Uppgifter kan också ha en kvarhållningsperiod för filen, varefter filer som skapas av aktiviteten tas bort. Därför är det viktigt att bevara uppgiftsutdata som du behöver senare till ett datalager som [Azure Storage](https://docs.microsoft.com/azure/storage/).

För olika alternativ för lagringskonton, se [Översikt över Batch-funktionen](../articles/batch/batch-api-basics.md#azure-storage-account).
