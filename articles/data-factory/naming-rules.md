---
title: Regler för namngivning av Azure Data Factory entiteter
description: Beskriver namngivnings regler för Data Factory entiteter.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 21adf26c2dbaca4507a4c925e3dae3b99c9d53ba
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96497531"
---
# <a name="azure-data-factory---naming-rules"></a>Namngivnings regler för Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Följande tabell innehåller namngivnings regler för Data Factory artefakter.

| Name | Namn unikhet | Verifierings kontroller |
|:--- |:--- |:--- |
| Data fabrik | Unikt mellan Microsoft Azure. Namn är inte Skift läges känsliga, det `MyDF` vill säga och `mydf` referera till samma data fabrik. |<ul><li>Varje data fabrik är knuten till exakt en Azure-prenumeration.</li><li>Objekt namn måste börja med en bokstav eller en siffra och får bara innehålla bokstäver, siffror och bindestreck (-).</li><li>Varje bindestreck (-) måste föregås omedelbart och följas av en bokstav eller en siffra. Efterföljande bindestreck tillåts inte i behållar namn.</li><li>Namnet kan vara 3-63 tecken långt.</li></ul> |
| Länkade tjänster/data uppsättningar/pipelines/data flöden | Unikt med i en data fabrik. Namn är inte Skift läges känsliga. |<ul><li>Objekt namn måste börja med en bokstav.</li><li>Följande tecken är inte tillåtna: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", " \\ "</li><li>Bindestreck ("-") tillåts inte i namnen på länkade tjänster, data flöden och data uppsättningar.</li></ul>  |
| Integration Runtime |Unikt med i en data fabrik. Namn är inte Skift läges känsliga. |<ul><li>Namnet på integration runtime får bara innehålla bokstäver, siffror och bindestreck (-).</li><li>Det första och sista tecknet måste vara en bokstav eller en siffra. Varje bindestreck (-) måste föregås omedelbart och följas av en bokstav eller en siffra.</li><li>Efterföljande bindestreck tillåts inte i integration runtime-namn. </li></ul> |
| Dataflödesomvandlingar | Unikt inom ett data flöde. Namn är inte Skift läges känsliga | <ul><li>Transformerings namn för data flöde får bara innehålla bokstäver och siffror</li><li>Det första tecknet måste vara en bokstav. </li></ul> |
| Resursgrupp |Unikt mellan Microsoft Azure. Namn är inte Skift läges känsliga. | Mer information finns i [namngivnings regler och begränsningar för Azure](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming). |

## <a name="next-steps"></a>Nästa steg
Lär dig hur du skapar data fabriker genom att följa steg-för-steg-instruktioner i [snabb start: skapa en data fabriks](quickstart-create-data-factory-powershell.md) artikel. 
