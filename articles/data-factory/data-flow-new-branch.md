---
title: Azure Data dataflöde Factory mappning ny gren omvandling
description: Azure Data dataflöde Factory mappning ny gren omvandling
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: c48c1500a31ce8f22bcef7207c0ea67caca24231
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56272111"
---
# <a name="azure-data-factory-mapping-data-flow-new-branch-transformation"></a>Azure Data dataflöde Factory mappning ny gren omvandling

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Branch-alternativ](media/data-flow/menu.png "menyn")

Branchning tar aktuella dataströmmen i ditt dataflöde och replikera den till en annan stream. Använd ny gren att utföra flera uppsättningar med åtgärder och transformationer mot samma dataström.

Exempel: Ditt dataflöde har en källa transformera med en vald uppsättning kolumner och konverteringsmöjligheter för datatyper. Du kan sedan placera en härledd kolumn direkt efter den här källan. I den härledda kolumnen har du skapa ett nytt fält som kombinerar förnamn och efternamn för att göra ett nytt ”efternamn”-fält.

Du kan behandla nya dataströmmen med en uppsättning transformationer och en mottagare på en rad och Använd ny gren för att skapa en kopia av dataströmmen där kan du omvandla samma data med en annan uppsättning transformationer. Genom att omvandla den kopierade data i en separat gren, kan du därefter mottagare dessa data till en annan plats.

> [!NOTE]
> ”Ny gren” visas endast som en åtgärd på den + omvandling menyn när det finns en efterföljande omvandling efter den aktuella platsen där du försöker grenen. dvs. visas inte en ”ny gren” alternativ i slutet här tills du lägger till en annan transformering efter Välj

![Branch](media/data-flow/branch2.png "Branch 2")
