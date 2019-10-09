---
title: Regler för namngivning av Azure Data Factory entiteter | Microsoft Docs
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
ms.openlocfilehash: c31cffd3c1734e8f71f8971d597eb9e3703ae331
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166430"
---
# <a name="azure-data-factory---naming-rules"></a>Namngivnings regler för Azure Data Factory
Följande tabell innehåller namngivnings regler för Data Factory artefakter.

| Name | Namn unikhet | Verifierings kontroller |
|:--- |:--- |:--- |
| Data Factory |Unikt mellan Microsoft Azure. Namn är Skift läges känsliga, det vill säga `MyDF` och `mydf` refererar till samma data fabrik. |<ul><li>Varje data fabrik är knuten till exakt en Azure-prenumeration.</li><li>Objekt namn måste börja med en bokstav eller en siffra och får bara innehålla bokstäver, siffror och bindestreck (-).</li><li>Varje bindestreck (-) måste föregås omedelbart och följas av en bokstav eller en siffra. Efterföljande bindestreck tillåts inte i behållar namn.</li><li>Namnet kan vara 3-63 tecken långt.</li></ul> |
| Länkade tjänster/data uppsättningar/pipeliner |Unikt med i en data fabrik. Namn är inte Skift läges känsliga. |<ul><li>Objekt namn måste börja med en bokstav, en siffra eller ett under streck (_).</li><li>Följande tecken är inte tillåtna: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", "\\"</li><li>Bindestreck ("-") tillåts inte i namnen på länkade tjänster och data uppsättningar.</li></ul>  |
| Resursgrupp |Unikt mellan Microsoft Azure. Namn är inte Skift läges känsliga. | Mer information finns i [namngivnings regler och begränsningar för Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/ready/considerations/naming-and-tagging#resource-naming). |

## <a name="next-steps"></a>Nästa steg
Lär dig hur du skapar data fabriker genom att följa steg-för-steg-instruktioner i [snabb start: skapa en data fabriks](quickstart-create-data-factory-powershell.md) artikel. 
