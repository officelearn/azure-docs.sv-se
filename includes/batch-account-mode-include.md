---
title: ta med fil
description: ta med fil
services: batch
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 05/04/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 62bb91a2e51c39caf31719f72d68a6edab9205bc
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
> [!NOTE]
> När du skapar ett Batch-konto kan du välja mellan två *poolallokeringslägen*: **användarprenumeration** och **Batch-tjänst**. I de flesta fall bör du använda standardläget Batch-tjänst. I det här läget allokeras pooler i bakgrunden i Azure-hanterade prenumerationer. I det alternativa användarprenumerationsläget skapas virtuella Batch-datorer och andra resurser direkt i din prenumeration när en pool skapas. Användarprenumerationsläge krävs om du vill skapa Batch-pooler med [Azure Reserved VM Instances](https://azure.microsoft.com/pricing/reserved-vm-instances/). Du måste registrera din prenumeration med Azure Batch och koppla kontot till ett Azure Key Vault för att kunna skapa ett Batch-konto i läget Användarprenumeration.