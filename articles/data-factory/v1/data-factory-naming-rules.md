---
title: Regler för namngivning av Azure Data Factory-entiteter
description: Beskriver namngivningsregler för datafabrikentiteter.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: bc5e801d-0b3b-48ec-9501-bb4146ea17f1
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 8fa1340b586434bf98d51437d4dc6b08594f0afa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931893"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory – namngivningsregler
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [namngivningsregler i Data Factory](../naming-rules.md).

I följande tabell finns namngivningsregler för datafabriksartefakter.

| Namn | Namn Unikhet | Valideringskontroller |
|:--- |:--- |:--- |
| Data Factory |Unik i Hela Microsoft Azure. Namn är skiftlägesokänsliga, det `MyDF` villa och `mydf` referera till samma datafabrik. |<ul><li>Varje datafabrik är knuten till exakt en Azure-prenumeration.</li><li>Objektnamn måste börja med en bokstav eller ett tal och kan bara innehålla bokstäver, siffror och strecktecknet (-).</li><li>Varje streck (-) tecken måste omedelbart föregås och följas av en bokstav eller ett tal. På varandra följande streck är inte tillåtna i behållarnamn.</li><li>Namnet kan vara 3-63 tecken långt.</li></ul> |
| Länkade tjänster/tabeller/pipelines |Unik med i en datafabrik. Namnen är skiftlägesokänsliga. |<ul><li>Maximalt antal tecken i ett tabellnamn: 260.</li><li>Objektnamn måste börja med en bokstav, ett nummer eller ett understreck (_).</li><li>Följande tecken är inte tillåtna: ".", "+", "?", "/", "<", ">","*","%","&",":","\\"</li></ul> |
| Resursgrupp |Unik i Hela Microsoft Azure. Namnen är skiftlägesokänsliga. |<ul><li>Maximalt antal tecken: 1000.</li><li>Namnet kan innehålla bokstäver, siffror och följande tecken: "-", "_", "" och ""</li></ul> |

