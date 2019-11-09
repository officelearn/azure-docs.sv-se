---
title: Regler för namngivning av Azure Data Factory entiteter
description: Beskriver namngivnings regler för Data Factory entiteter.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2018
ms.openlocfilehash: 59cddf04493333b441dcf130d1d99d4fa946748c
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837835"
---
# <a name="azure-data-factory---naming-rules"></a>Namngivnings regler för Azure Data Factory
Följande tabell innehåller namngivnings regler för Data Factory artefakter.

| Namn | Namn unikhet | Verifierings kontroller |
|:--- |:--- |:--- |
| Data Factory |Unikt mellan Microsoft Azure. Namn är Skift läges känsliga, det vill säga `MyDF` och `mydf` refererar till samma data fabrik. |<ul><li>Varje data fabrik är knuten till exakt en Azure-prenumeration.</li><li>Objekt namn måste börja med en bokstav eller en siffra och får bara innehålla bokstäver, siffror och bindestreck (-).</li><li>Varje bindestreck (-) måste föregås omedelbart och följas av en bokstav eller en siffra. Efterföljande bindestreck tillåts inte i behållar namn.</li><li>Namnet kan vara 3-63 tecken långt.</li></ul> |
| Länkade tjänster/data uppsättningar/pipeliner |Unikt med i en data fabrik. Namn är inte Skift läges känsliga. |<ul><li>Objekt namn måste börja med en bokstav, en siffra eller ett under streck (_).</li><li>Följande tecken är inte tillåtna: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", "\\"</li><li>Bindestreck ("-") tillåts inte i namnen på länkade tjänster och data uppsättningar.</li></ul>  |
| Resursgrupp |Unikt mellan Microsoft Azure. Namn är inte Skift läges känsliga. | Mer information finns i [namngivnings regler och begränsningar för Azure](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming). |

## <a name="next-steps"></a>Nästa steg
Lär dig hur du skapar data fabriker genom att följa steg-för-steg-instruktioner i [snabb start: skapa en data fabriks](quickstart-create-data-factory-powershell.md) artikel. 
