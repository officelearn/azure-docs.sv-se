---
title: ta med fil
description: ta med fil
services: batch
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 7cee3f534e4ab4973a87a9c373a5b83aa2ba9ce2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60549953"
---
En aktivitet som körs i Azure Batch kan producera utdata när den körs. Uppgift utdata data behöver ofta lagras för hämtning av andra aktiviteter i jobbet, det klientprogram som körs jobbet eller båda. Uppgifter skriva utdata till filsystemet för en Batch-beräkningsnod, men alla data på noden förloras när den återställs eller när nod lämnar poolen. Uppgifter kan också ha en kvarhållningsperiod för filen, varefter filer som skapas av aktiviteten tas bort. Därför är det viktigt att bevara uppgiftsutdata som du behöver senare till ett datalager som [Azure Storage](https://docs.microsoft.com/azure/storage/).

För olika alternativ för lagringskonton, se [Översikt över Batch-funktionen](../articles/batch/batch-api-basics.md#azure-storage-account).
