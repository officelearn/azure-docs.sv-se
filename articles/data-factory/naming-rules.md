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
ms.date: 01/16/2018
ms.author: shlo
ms.openlocfilehash: c02a9393de72b827b7e38b52d06589f042d581b0
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54024781"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory – namnregler
Följande tabell innehåller namnregler för Data Factory-artefakter.

| Namn | Namn på unikhet | Verifieringskontroller |
|:--- |:--- |:--- |
| Data Factory |Unikt över Microsoft Azure. Är skiftlägeskänsliga, det vill säga `MyDF` och `mydf` referera till samma data factory. |<ul><li>Varje data factory är kopplad till exakt en Azure-prenumeration.</li><li>Objektnamn måste börja med en bokstav eller en siffra och får innehålla endast bokstäver, siffror och bindestreck (-).</li><li>Varje bindestreck (-) måste föregås och följas av en bokstav eller ett tal. Streck i följd är inte tillåtna i behållarnamn.</li><li>Namnet kan vara mellan 3-63 tecken långt.</li></ul> |
| Länkade tjänster/datauppsättningar/Pipelines |Unikt med i en data factory. Är skiftlägeskänsliga. |<ul><li>Objektnamn måste börja med en bokstav, siffra eller ett understreck (_).</li><li>Följande tecken är inte tillåtna ”:”., ”+” ”,”?, ”/” ”, <” ”, >” ”, *”, ”%”, ”&” ”,:” ”,\\”</li><li>Streck (”-”) är inte tillåtna i namnen på länkade tjänster och datauppsättningar endast.</li></ul>  |
| Resursgrupp |Unikt över Microsoft Azure. Är skiftlägeskänsliga. | Mer information finns i [Azure namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions). |

## <a name="next-steps"></a>Nästa steg
Lär dig hur du skapar datafabriker med stegvisa instruktioner i [Snabbstart: skapa en datafabrik](quickstart-create-data-factory-powershell.md) artikeln. 
