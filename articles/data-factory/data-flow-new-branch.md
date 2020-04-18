---
title: Flera grenar i mappningsdataflödet
description: Replikera dataströmmar i mappning av dataflöde med flera grenar
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2020
ms.openlocfilehash: daebd6a4a00d82706ceda68f94736f21b8fe5a84
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606402"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Skapa en ny gren vid mappning av dataflöde

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Lägg till en ny gren för att göra flera uppsättningar åtgärder och omvandlingar mot samma dataström. Det är användbart att lägga till en ny gren när du vill använda samma källa till för flera sänkor eller för att sammanfoga själv.

En ny gren kan läggas till från omvandlingslistan som liknar andra omvandlingar. **Den nya grenen** är bara tillgänglig som en åtgärd när det finns en befintlig omvandling efter omvandlingen som du försöker förgrena.

![Lägga till en ny gren](media/data-flow/new-branch2.png "Lägga till en ny gren")

I exemplet nedan läser dataflödet taxiresadata. Utdata aggregeras av både dag och leverantör krävs. I stället för att skapa två separata dataflöden som läs från samma källa kan en ny gren läggas till. På så sätt kan båda aggregeringarna utföras som en del av samma dataflöde. 

![Lägga till en ny gren](media/data-flow/new-branch.png "Lägga till en ny gren")
