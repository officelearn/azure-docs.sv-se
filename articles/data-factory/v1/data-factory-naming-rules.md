---
title: Regler för namngivning av Azure Data Factory-entiteter | Microsoft Docs
description: Beskriver namnregler för Data Factory-entiteter.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: bc5e801d-0b3b-48ec-9501-bb4146ea17f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: daf0b1c12ab10230690a62eb5dc772417d8b92f3
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54024509"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory – namnregler
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [namngivningsregler i Data Factory](../naming-rules.md).

Följande tabell innehåller namnregler för Data Factory-artefakter.

| Namn | Namn på unikhet | Verifieringskontroller |
|:--- |:--- |:--- |
| Data Factory |Unikt över Microsoft Azure. Är skiftlägeskänsliga, det vill säga `MyDF` och `mydf` referera till samma data factory. |<ul><li>Varje data factory är kopplad till exakt en Azure-prenumeration.</li><li>Objektnamn måste börja med en bokstav eller en siffra och får innehålla endast bokstäver, siffror och bindestreck (-).</li><li>Varje bindestreck (-) måste föregås och följas av en bokstav eller ett tal. Streck i följd är inte tillåtna i behållarnamn.</li><li>Namnet kan vara mellan 3-63 tecken långt.</li></ul> |
| Länkade tjänster/tabeller/Pipelines |Unikt med i en data factory. Är skiftlägeskänsliga. |<ul><li>Maximalt antal tecken i ett tabellnamn: 260.</li><li>Objektnamn måste börja med en bokstav, siffra eller ett understreck (_).</li><li>Följande tecken är inte tillåtna ”:”., ”+” ”,”?, ”/” ”, <” ”, >” ”, *”, ”%”, ”&” ”,:” ”,\\”</li></ul> |
| Resursgrupp |Unikt över Microsoft Azure. Är skiftlägeskänsliga. |<ul><li>Maximalt antal tecken: 1000.</li><li>Namnet får innehålla bokstäver, siffror och följande tecken ”:-” ”, _” ”,,” och ””.</li></ul> |

