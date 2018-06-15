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
ms.openlocfilehash: 0dcb608553d4455403c073e34e83bccb81cc64db
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2018
ms.locfileid: "31532506"
---
En aktivitet som körs i Azure Batch kan gav inga utdata när den körs. Uppgift utgående data behöver ofta lagras för hämtning av andra uppgifter i jobbet, klientprogram som körs jobbet eller båda. Uppgifter skriva utdata till filsystemet på en Batch-beräkningsnod, men alla data på noden går förlorad när avbildade eller när noden lämnar poolen. Uppgifter kan också ha en kvarhållningsperiod för filen, efter vilken filer som skapas av aktiviteten tas bort. Därför är det viktigt att bevara uppgiftens utdata som du behöver senare till ett dataarkiv som [Azure Storage](https://docs.microsoft.com/azure/storage/).

Kontot lagringsalternativ i Batch, finns det [Batch funktionsöversikt](../articles/batch/batch-api-basics.md#azure-storage-account).
