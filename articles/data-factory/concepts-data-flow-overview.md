---
title: Azure Data Factory mappning översikt över flödet av Data
description: En översikt över förklaring av mappning av Data som flödar i Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 5064f47444b4ca6d9194ed66144938e6e3d51a4e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61261990"
---
# <a name="what-are-mapping-data-flows-in-azure-data-factory"></a>Vad är mappning av Data som flödar i Azure Data Factory?

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Mappningen Data flödar kan datatekniker att utveckla grafiska dataomvandlingslogik utan att skriva kod. De resulterande dataflöden utförs som aktiviteter i Azure Data Factory-Pipelines med utskalade Azure Databricks-kluster.

Syftet med Azure Data Factory-dataflöde är att tillhandahålla en fullständigt visuell upplevelse med ingen kodning krävs. Dina Data som flödar körs på ett eget kluster för körning för att bearbeta skalats ut. Azure Data Factory hanterar alla översättning, sökväg optimering och körning av dina data flow-jobb.

Börja med att skapa dataflöden i felsökningsläge så att du kan validera omvandling logik interaktivt. Lägg sedan till aktiviteten dataflödet din pipeline för att köra och testa dina data för flow i pipeline-felsökning eller använda ”Utlös nu” i pipelinen för att testa ditt dataflöde från en pipeline-aktivitet.

Du kommer sedan schemalägga och övervaka dina flödesaktiviteter för data med hjälp av Azure Data Factory-pipelines som kör aktiviteten dataflöde.

Växlingsreglaget felsökningsläge på designytan dataflöde kan interaktiva bygga Datatransformationer. Felsökningsläge innehåller en data prep miljö för utveckling för flödet av data.
