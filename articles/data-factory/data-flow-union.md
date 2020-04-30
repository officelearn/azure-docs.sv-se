---
title: Mappa data Flow union-transformering
description: Azure Data Factory mappa data flöde ny gren omvandling
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 04/27/2020
ms.openlocfilehash: 1874e3536a3dda123c7fff9726c02e5d92d88804
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204346"
---
# <a name="azure-data-factory-mapping-data-flow-union-transformation"></a>Transformering av Azure Data Factory mappning av data Flow union

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Union kombinerar flera data strömmar till en, med SQL-unionen av dessa strömmar som nya utdata från Union-omvandlingen. Alla scheman från varje indataströmmen kommer att kombineras i ditt data flöde, utan att ha en kopplings nyckel.

Du kan kombinera n-antal strömmar i inställnings tabellen genom att välja ikonen "+" bredvid varje konfigurerad rad, inklusive både käll data och strömmar från befintliga transformationer i ditt data flöde.

Här är en kort video genom gång av union-omvandlingen i ADF: s mappnings data flöde:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vngz]

![Union-transformering](media/data-flow/union.png "Union")

I det här fallet kan du kombinera olika metadata från flera källor (i det här exemplet tre olika källfiler) och kombinera dem till en enda ström:

![Översikt över union-omvandling](media/data-flow/union111.png "Union 1")

För att åstadkomma detta lägger du till ytterligare rader i unions inställningarna genom att inkludera alla källor som du vill lägga till. Det finns inget behov av en gemensam Sök-eller kopplings nyckel:

![Omvandlings inställningar för union](media/data-flow/unionsettings.png "Unions inställningar")

Om du ställer in en Välj omvandling efter din union, kan du byta namn på överlappande fält eller fält som inte har namngetts från huvudets huvud källor. Klicka på "inspektera" om du vill se kombinera metadata med 132 totala kolumner i det här exemplet från tre olika källor:

![Unions transformering slut](media/data-flow/union333.png "Union 3")

## <a name="name-and-position"></a>Namn och position

När du väljer "union efter namn", kommer varje kolumn värde att släppas i motsvarande kolumn från varje källa med ett nytt sammanfogat metadata-schema.

Om du väljer "union by position" kommer varje kolumn värde att släppas till den ursprungliga positionen från varje motsvarande källa, vilket resulterar i en ny kombinerad data ström där data från varje källa läggs till i samma ström:

![Union-utdata](media/data-flow/unionoutput.png "Union-utdata")

## <a name="next-steps"></a>Nästa steg

Utforska liknande omvandlingar inklusive [Join](data-flow-join.md) och [exists](data-flow-exists.md).
