---
title: Rendera manager support – Azure Batch
description: Använda Azure för rendering med hjälp av Azure Batch rendering manager-integrering
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 4eeece4946b4f957d9f864da7c46d77d119863b5
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53539930"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>Med Azure Batch med rendering gruppen Chefer

Om du använder en befintlig återge lokal servergrupp, så är det mycket troligt att en hanterare för rendering styr rendering servergruppen kapacitet och rendera jobb.

Azure ger inbyggt stöd eller tillägg till populära rendering chefer. Du kan lägga till och ta bort Azure virtuella datorer, inklusive virtuella datorer med betala för det du använder programmet licensiering och lågprioriterade virtuella datorer.

Följande rendering chefer stöds:

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Royal rendering](http://www.royalrender.de/)
* [Thinkbox tidsgräns](https://deadline.thinkboxsoftware.com/)

## <a name="using-azure-with-pipelinefx-qube"></a>Med hjälp av Azure med PipelineFX Qube

Skript och instruktioner för att aktivera Azure Batch-poolen virtuella datorer som ska användas som Qube arbetare är i [GitHub-lagringsplatsen](https://github.com/Azure/azure-qube).

## <a name="using-azure-with-royal-render"></a>Med hjälp av Azure med Royal rendering

Royal rendering har Azure och Azure Batch-integrering inbyggda, så att du kan utöka en renderingsgrupp med Azure-baserade virtuella datorer. En sammanfattning finns [hjälpfilerna](http://www.royalrender.de/help8/index.html?Cloudrendering.html).

Ett exempel på Royal rendera kund som använder Azure-integrering finns i den [Jellyfish bilder kundberättelsen](https://customers.microsoft.com/story/jellyfishpictures).

## <a name="using-azure-with-thinkbox-deadline"></a>Med hjälp av Azure med Thinkbox tidsgräns

Skript och instruktioner för att aktivera Azure Batch-poolen virtuella datorer som ska användas eftersom tidsgränsen slaves [GitHub-lagringsplatsen](https://github.com/Azure/azure-deadline).

## <a name="next-steps"></a>Nästa steg

Prova att använda Azure Batch-integrering för chefen rendering med hjälp av lämplig plugin-programmet och anvisningar om GitHub, om tillämpligt.