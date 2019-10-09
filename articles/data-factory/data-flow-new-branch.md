---
title: Azure Data Factory mappa data flöde ny gren omvandling
description: Azure Data Factory mappa data flöde ny gren omvandling
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 3f443396627a4bbaba2a3eeb0a3ac05cc1597c85
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029285"
---
# <a name="azure-data-factory-mapping-data-flow-new-branch-transformation"></a>Azure Data Factory mappa data flöde ny gren omvandling



(media/data-flow/menu.png "Menyn") ![förgrenings alternativ]

Branchning tar den aktuella data strömmen i ditt data flöde och replikerar den till en annan data ström. Använd ny gren för att utföra flera uppsättningar av åtgärder och omvandlingar mot samma data ström.

Exempel: Ditt data flöde har en käll omvandling med en vald uppsättning kolumner och data typs konverteringar. Sedan placerar du en härledd kolumn direkt efter den källan. I den härledda kolumnen har du skapat ett nytt fält som kombinerar förnamn och efter namn för att skapa ett nytt "fullständigt namn"-fält.

Du kan behandla den nya strömmen med en uppsättning transformationer och en mottagare på en rad och använda ny gren för att skapa en kopia av data strömmen där du kan omvandla samma data med en annan uppsättning transformeringar. Genom att omvandla de kopierade data i en separat gren kan du därefter mata in dessa data till en annan plats.

> [!NOTE]
> "Ny gren" visas endast som en åtgärd på menyn + omvandling när det finns en efterföljande omvandling efter den aktuella plats där du försöker förgrena. Det innebär att du inte ser alternativet "ny gren" i slutet här förrän du lägger till en annan omvandling efter Select

![Gren](media/data-flow/branch2.png "gren 2")
