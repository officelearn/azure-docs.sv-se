---
title: Skapa Azure Data Factory mappning av dataflöde
description: Skapa Azure Data Factory mappning av dataflöde
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: b706e229bed48c821d5ca772450df320fd7e0b7f
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271991"
---
# <a name="create-azure-data-factory-data-flow"></a>Skapa Azure Data Factory-dataflöde

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Mappning av Data som flödar i ADF är ett sätt att omvandla data i stor skala utan kodning krävs. Du kan utforma ett omvandlingsjobb i flödesdesignern data genom att skapa en serie transformationer. Börja med valfritt antal källa transformationer följt av datatransformationssteg. Slutför ditt dataflöde med mottagare att få dina resultat i en destination.

Kom igång genom att först skapa en ny V2 Data Factory från Azure Portal. När du har skapat din nya datafabrik klickar du på ikonen ”författare och Övervakare” för att starta Användargränssnittet för Data Factory.

![Alternativ för Flow](media/data-flow/v2dataflowportal.png "dataflödet skapa")

När du använder Användargränssnittet för Data Factory kan använda du exemplet Data flödar. Exemplen är tillgängliga från ADF mallgalleriet. Skapa ”Pipeline från mall” i ADF, och Välj kategorin Data flöda från mallgalleriet.

![Alternativ för Flow](media/data-flow/template.png "dataflödet skapa")

Du uppmanas att ange dina uppgifter för Azure Blob Storage-konto.

![Alternativ för Flow](media/data-flow/template2.png "dataflödet skapa 2")

[Data som används för de här exemplen finns här](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata). Ladda ned exempeldata och lagra filer i din Azure Blob storage-konton så att du kan använda exemplen.

Använd skapa resursen ”plus-tecknet”-knappen i ADF UI att skapa Data flödar

![Alternativ för Flow](media/data-flow/newresource.png "ny resurs")

