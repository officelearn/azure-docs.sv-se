---
title: Skapa Azure Data Factory mappning av dataflöde
description: Så här skapar du en Azure Data Factory mappning av dataflöde
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 366ed60534544ebbf889e2f72fe703f9b821f871
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65235665"
---
# <a name="create-azure-data-factory-data-flow"></a>Skapa Azure Data Factory-dataflöde

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Mappning av Data som flödar i ADF är ett sätt att omvandla data i stor skala utan kodning krävs. Du kan utforma ett omvandlingsjobb i flödesdesignern data genom att skapa en serie transformationer. Börja med valfritt antal källa transformationer följt av datatransformationssteg. Slutför ditt dataflöde med mottagare att få dina resultat i en destination.

Kom igång genom att först skapa en ny V2 Data Factory från Azure-portalen. När du har skapat din nya datafabrik klickar du på ikonen ”författare och Övervakare” för att starta Användargränssnittet för Data Factory.

![Alternativ för Flow](media/data-flow/v2portal.png "dataflödet skapa")

När du använder Användargränssnittet för Data Factory kan använda du exemplet Data flödar. Exemplen är tillgängliga från ADF mallgalleriet. Skapa ”Pipeline från mall” i ADF, och Välj kategorin Data flöda från mallgalleriet.

![Alternativ för Flow](media/data-flow/template.png "dataflödet skapa")

Du uppmanas att ange dina uppgifter för Azure Blob Storage-konto.

![Alternativ för Flow](media/data-flow/template2.png "dataflödet skapa 2")

[Data som används för de här exemplen finns här](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata). Ladda ned exempeldata och lagra filer i din Azure Blob storage-konton så att du kan använda exemplen.

## <a name="create-new-data-flow"></a>Skapa nytt dataflöde

Använd skapa resursen ”plus-tecknet”-knappen i ADF UI att skapa Data flödar.

![Alternativ för Flow](media/data-flow/newresource.png "ny resurs")

## <a name="next-steps"></a>Nästa steg

Börja skapa din Dataomvandling med en [source omvandling](data-flow-source.md).
