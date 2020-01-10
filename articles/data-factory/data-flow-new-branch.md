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
ms.openlocfilehash: 71fb9f1ba9952be0e6b3910dd1079aa6d3c0482d
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834522"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Skapa en ny gren i mappnings data flödet

Lägg till en ny gren för att utföra flera uppsättningar åtgärder och omvandlingar mot samma data ström. Att lägga till en ny gren är användbart när du vill använda samma källa för flera handfat eller för att koppla samman data till varandra.

Du kan lägga till en ny gren från omvandlings listan som liknar andra transformeringar. **Ny gren** är bara tillgänglig som en åtgärd när det finns en befintlig omvandling efter den omvandling som du försöker förgrena.

![Lägga till en ny gren](media/data-flow/new-branch2.png "Lägga till en ny gren")

I exemplet nedan läser data flödet taxi rese data. Utdata som sammanställs av både dag och leverantör krävs. I stället för att skapa två separata data flöden som läser från samma källa kan du lägga till en ny gren. På så sätt kan båda agg regeringar köras som en del av samma data flöde. 

![Lägga till en ny gren](media/data-flow/new-branch.png "Lägga till en ny gren")
