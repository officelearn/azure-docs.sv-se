---
title: Azure Data Factory mappning Flow Surrogate viktiga Dataomvandling
description: Azure Data Factory mappning Flow Surrogate viktiga Dataomvandling
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 6243905857f0450168541f556636d90bb4d855f7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56734956"
---
# <a name="azure-data-factory-mapping-data-flow-surrogate-key-transformation"></a>Azure Data Factory mappning Flow Surrogate viktiga Dataomvandling

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Använd Surrogate nyckel omvandlingen att lägga till en stegvis ökande-business godtyckliga nyckelvärdet i dina data flow raduppsättningen. Detta är användbart när du utformar dimensionstabeller i en star-schema analysdata modell där varje medlem i din dimensionstabeller måste ha en unik nyckel som tillhör en annan nyckel, Kimball DW-metod.

![Surrogat viktiga transformeringen](media/data-flow/surrogate.png "surrogat omvandling")

”Nyckelkolumn” är det namn som du ska ge din nya surrogate nyckelkolumn.

”Start värdet” är startpunkten för inkrementell värdet.
