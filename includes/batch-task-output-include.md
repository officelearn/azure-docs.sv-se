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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187696"
---
En uppgift som körs i Azure Batch kan producera utdata när den körs. Aktivitetsdata måste ofta lagras för hämtning av andra aktiviteter i jobbet, klientprogrammet som utförde jobbet eller båda. Uppgifter skriver utdata till filsystemet för en batch-beräkningsnod, men alla data på noden går förlorade när den görs om eller när noden lämnar poolen. Uppgifter kan också ha en filkvarhållningsperiod, varefter filer som skapas av uppgiften tas bort. Av dessa skäl är det viktigt att spara aktivitetsutdata som du behöver senare i ett datalager som [Azure Storage](https://docs.microsoft.com/azure/storage/).

För olika alternativ för lagringskonton, se [Översikt över Batch-funktionen](../articles/batch/batch-api-basics.md#azure-storage-account).
