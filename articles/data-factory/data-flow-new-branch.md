---
title: Flera grenar i mappnings data flödet
description: Replikera data strömmar i mappa data flöde med flera grenar
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2020
ms.openlocfilehash: a1dd4baecd0e1f817c93652fbc0766069ccf5583
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93040138"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Skapa en ny gren i mappnings data flödet

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Lägg till en ny gren för att utföra flera uppsättningar åtgärder och omvandlingar mot samma data ström. Att lägga till en ny gren är användbart när du vill använda samma källa för flera handfat eller för att koppla samman data till varandra.

Du kan lägga till en ny gren från omvandlings listan som liknar andra transformeringar. **Ny gren** är bara tillgänglig som en åtgärd när det finns en befintlig omvandling efter den omvandling som du försöker förgrena.

![Skärm bild som visar alternativet ny gren på menyn för flera indata/utdata/utdata.](media/data-flow/new-branch2.png "Lägga till en ny gren")

I exemplet nedan läser data flödet taxi rese data. Utdata som sammanställs av både dag och leverantör krävs. I stället för att skapa två separata data flöden som läser från samma källa kan du lägga till en ny gren. På så sätt kan båda agg regeringar köras som en del av samma data flöde. 

![Skärm bild visar data flödet med två grenar från källan.](media/data-flow/new-branch.png "Lägga till en ny gren")
