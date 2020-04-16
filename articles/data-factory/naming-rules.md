---
title: Regler för namngivning av Azure Data Factory-entiteter
description: Beskriver namngivningsregler för datafabrikentiteter.
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
ms.openlocfilehash: f922ada663391cf65a61f4e18bba53668f9c4a1a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419416"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory – namngivningsregler

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I följande tabell finns namngivningsregler för datafabriksartefakter.

| Namn | Namn Unikhet | Valideringskontroller |
|:--- |:--- |:--- |
| Data Factory |Unik i Hela Microsoft Azure. Namn är skiftlägesokänsliga, det `MyDF` villa och `mydf` referera till samma datafabrik. |<ul><li>Varje datafabrik är knuten till exakt en Azure-prenumeration.</li><li>Objektnamn måste börja med en bokstav eller ett tal och kan bara innehålla bokstäver, siffror och strecktecknet (-).</li><li>Varje streck (-) tecken måste omedelbart föregås och följas av en bokstav eller ett tal. På varandra följande streck är inte tillåtna i behållarnamn.</li><li>Namnet kan vara 3-63 tecken långt.</li></ul> |
| Länkade tjänster/datauppsättningar/pipelines |Unik med i en datafabrik. Namnen är skiftlägesokänsliga. |<ul><li>Objektnamn måste börja med en bokstav, ett nummer eller ett understreck (_).</li><li>Följande tecken är inte tillåtna: ".", "+", "?", "/", "<", ">","*","%","&",":","\\"</li><li>Streck ("-") är inte tillåtna i namnen på länkade tjänster och endast datauppsättningar.</li></ul>  |
| Resursgrupp |Unik i Hela Microsoft Azure. Namnen är skiftlägesokänsliga. | Mer information finns i [Azures namngivningsregler och begränsningar](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming). |

## <a name="next-steps"></a>Nästa steg
Lär dig hur du skapar datafabriker genom att följa steg-för-steg-instruktioner i [Snabbstart: skapa en datafabriksartikel.](quickstart-create-data-factory-powershell.md) 
