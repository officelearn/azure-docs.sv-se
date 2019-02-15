---
title: Azure Data Factory mappning Flow Surrogate viktiga Dataomvandling
description: Azure Data Factory mappning Flow Surrogate viktiga Dataomvandling
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 62e9879f6bc75f042669ecb931ca7459d1317cc7
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56272088"
---
# <a name="azure-data-factory-mapping-data-flow-surrogate-key-transformation"></a>Azure Data Factory mappning Flow Surrogate viktiga Dataomvandling

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Använd Surrogate nyckel omvandlingen att lägga till en stegvis ökande-business godtyckliga nyckelvärdet i dina data flow raduppsättningen. Detta är användbart när du utformar dimensionstabeller i en star-schema analysdata modell där varje medlem i din dimensionstabeller måste ha en unik nyckel som tillhör en annan nyckel, Kimball DW-metod.

![Surrogat viktiga transformeringen](media/data-flow/surrogate.png "surrogat omvandling")

”Nyckelkolumn” är det namn som du ska ge din nya surrogate nyckelkolumn.

”Start värdet” är startpunkten för inkrementell värdet.
