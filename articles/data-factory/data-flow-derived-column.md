---
title: Azure Data Factory dataflöde härledda kolumnen omvandling
description: Azure Data Factory dataflöde härledda kolumnen omvandling
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: aee005aed52563e214e24148af2563fd7869de76
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56272016"
---
# <a name="azure-data-factory-data-flow-derived-column-transformation"></a>Azure Data Factory dataflöde härledda kolumnen omvandling

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Använd transformeringen härledd kolumn att generera nya kolumner i ditt dataflöde eller ändra befintliga fält.

![Härled kolumner](media/data-flow/dc1.png "härledd kolumn")

Du kan utföra flera åtgärder för härledd kolumn i en enda härledd kolumn omvandling. Klicka på ”Lägg till kolumn” för att omvandla mer än 1 kolumn i enkel omvandling steg.

Välj en befintlig kolumn att skriva över med ett nytt härledda värde i fältet kolumnen eller klicka på ”Skapa ny kolumn” för att generera en ny kolumn med nyligen härledda värdet.

Textrutan uttryck öppnas Uttrycksverktyget där du kan skapa uttrycket för de härledda kolumner med hjälp av uttryck.
