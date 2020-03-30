---
title: ta med fil
description: ta med fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/26/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a67ad4c5010cf93ff55123013a35c697ce5971f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597886"
---
Azure-filresurser distribueras till *lagringskonton*, som är objekt på den högsta nivån som representerar en delad lagringspool. Den här lagringspoolen kan användas för att distribuera flera filresurser, samt andra lagringsresurser som blob-behållare, köer eller tabeller. Alla lagringsresurser som distribueras till ett lagringskonto delar de gränser som gäller för det lagringskontot. Information om vilka aktuella begränsningarna för ett lagringskonto finns i [Azure Files skalbarhet och prestandamål](../articles/storage/files/storage-files-scale-targets.md).

Det finns två huvudtyper av lagringskonton som du använder för Azure Files-distributioner: 
- **GPv2-lagringskonton (General Purpose Version 2):** GPv2-lagringskonton gör att du kan distribuera Azure-filresurser på standard-/hårddiskbaserad (HDD-baserad) maskinvara. Förutom att lagra Azure-filresurser kan GPv2-lagringskonton lagra andra lagringsresurser, till exempel blob-behållare, köer eller tabeller. 
- **FileStorage lagringskonton**: FileStorage lagringskonton kan du distribuera Azure-filresurser på premium/solid state disk-baserade (SSD-baserade) hårdvara. FileStorage-konton kan endast användas för att lagra Azure-filresurser. Inga andra lagringsresurser (blob-behållare, köer, tabeller osv.) kan distribueras i ett FileStorage-konto.

Det finns flera andra lagringskontotyper som du kan stöta på i Azure-portalen, PowerShell eller CLI. Två lagringskontotyper, BlockBlobStorage och BlobStorage-lagringskonton, kan inte innehålla Azure-filresurser. De andra två lagringskontotyperna som du kan se är allmänna version 1 (GPv1) och klassiska lagringskonton, som båda kan innehålla Azure-filresurser. Även om GPv1- och klassiska lagringskonton kan innehålla Azure-filresurser är de flesta nya funktioner i Azure Files endast tillgängliga i GPv2- och FileStorage-lagringskonton. Vi rekommenderar därför att du bara använder GPv2- och FileStorage-lagringskonton för nya distributioner och uppgraderar GPv1- och klassiska lagringskonton om de redan finns i din miljö.  