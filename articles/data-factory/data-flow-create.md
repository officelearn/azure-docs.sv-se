---
title: Skapa ett data flöde för mappning
description: Så här skapar du ett Azure Data Factory mappnings data flöde
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/12/2019
ms.openlocfilehash: cd1342ae5912a9a91f1bd8d1bdacecdb5a2bbdb0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "81416512"
---
# <a name="create-azure-data-factory-data-flow"></a>Skapa Azure Data Factory-dataflöde

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Att mappa data flöden i ADF är ett sätt att omvandla data i skala utan att behöva koda. Du kan utforma ett datatransformerings jobb i data flödes designern genom att skapa en serie transformeringar. Börja med ett valfritt antal käll omvandlingar följt av data omvandlings steg. Fyll sedan i ditt data flöde med mottagare för att få dina resultat i ett mål.

Kom igång genom att först skapa en ny v2-Data Factory från Azure Portal. När du har skapat din nya fabrik klickar du på panelen "skapa & övervakare" för att starta Data Factory användar gränssnittet.

![Data flödes alternativ](media/data-flow/v2portal.png "skapa data flöde")

När du är i Data Factory användar gränssnittet kan du använda exempel data flöden. Exemplen är tillgängliga från galleriet för ADM-mallar. I ADF skapar du "pipeline från mall" och väljer kategorin data flöde från mallgalleriet.

![Data flödes alternativ](media/data-flow/template.png "skapa data flöde")

Du uppmanas att ange din Azure Blob Storage konto information.

![Data flödes alternativ](media/data-flow/template2.png "data flöde-skapa 2")

[De data som används för dessa exempel hittar du här](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata). Hämta exempel data och lagra filerna i dina Azure Blob Storage-konton så att du kan köra exemplen.

## <a name="create-new-data-flow"></a>Skapa nytt data flöde

Använd knappen Skapa resurs "plus tecken" i ADF-gränssnittet för att skapa data flöden.

![Data flödes alternativ](media/data-flow/newresource.png "Ny resurs")

## <a name="next-steps"></a>Nästa steg

Börja skapa din data omvandling med en [käll omvandling](data-flow-source.md).
