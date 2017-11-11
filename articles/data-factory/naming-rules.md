---
title: "Regler för namngivning av enheter i Azure Data Factory | Microsoft Docs"
description: "Beskriver namnregler för enheter som Data Factory."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: bc5e801d-0b3b-48ec-9501-bb4146ea17f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: shlo
ms.openlocfilehash: 085328a9bbe304004f25f46ba5c366e911ac3836
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2017
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - namngivningsregler
Följande tabell innehåller namngivningsregler för Data Factory-artefakter.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [namngivningsregler i Data Factory version1](v1/data-factory-naming-rules.md).

| Namn | Unika namn | Kontroller |
|:--- |:--- |:--- |
| Data Factory |Unikt över Microsoft Azure. Namn är inte skiftlägeskänsliga, det vill säga `MyDF` och `mydf` referera till samma data factory. |<ul><li>Varje datafabriken är kopplad till en Azure-prenumeration.</li><li>Objektnamn måste börja med en bokstav eller en siffra och får innehålla endast bokstäver, siffror och streck (-).</li><li>Varje streck (-) måste föregås och följas av en bokstav eller en siffra. Streck i följd är inte tillåtna i behållarnamn.</li><li>Namnet kan innehålla 3-63 tecken.</li></ul> |
| Länkade tjänster/tabeller/Pipelines |Unikt med i en data factory. Namn är inte skiftlägeskänsliga. |<ul><li>Maximalt antal tecken i ett tabellnamn: 260.</li><li>Objektnamn måste börja med en bokstav, siffra eller ett understreck (_).</li><li>Följande tecken är inte tillåtna ”:”., ”+” ”,”?, ”/” ”, <” ”, >” ”, *”, ”%”, ”&” ”,:” ”,\\”</li></ul> |
| Resursgrupp |Unikt över Microsoft Azure. Namn är inte skiftlägeskänsliga. |<ul><li>Maximalt antal tecken: 1000.</li><li>Namnet får innehålla bokstäver, siffror och följande tecken ”:-” ”, _” ”,,” och ””.</li></ul> |

## <a name="next-steps"></a>Nästa steg
Lär dig hur du skapar datafabriker genom att följa instruktionerna i [Snabbstart: skapa en datafabrik](quickstart-create-data-factory-powershell.md) artikel. 