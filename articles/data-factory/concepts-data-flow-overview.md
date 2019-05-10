---
title: Azure Data Factory mappning översikt över flödet av Data
description: En översikt över förklaring av mappning av Data som flödar i Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 051886f98d6d35594336291bbb2defb2a4acdfc5
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233062"
---
# <a name="what-are-mapping-data-flows"></a>Vad är mappning Data flödar?

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Mappningen Data flödar är visuellt utformad dataomvandlingen i Azure Data Factory. Dataflöden kan datatekniker att utveckla grafiska dataomvandlingslogik utan att skriva kod. De resulterande dataflöden utförs som aktiviteter i Azure Data Factory-Pipelines med utskalade Azure Databricks-kluster.

Syftet med Azure Data Factory-dataflöde är att tillhandahålla en fullständigt visuell upplevelse med ingen kodning krävs. Dina Data som flödar körs på ett eget kluster för körning för att bearbeta skalats ut. Azure Data Factory hanterar alla översättning, sökväg optimering och körning av dina data flow-jobb.

Börja med att skapa dataflöden i felsökningsläge så att du kan validera omvandling logik interaktivt. Lägg sedan till aktiviteten dataflödet din pipeline för att köra och testa dina data för flow i pipeline-felsökning eller använda ”Utlös nu” i pipelinen för att testa ditt dataflöde från en pipeline-aktivitet.

Du kommer sedan schemalägga och övervaka dina flödesaktiviteter för data med hjälp av Azure Data Factory-pipelines som kör aktiviteten dataflöde.

Växlingsreglaget felsökningsläge på designytan dataflöde kan interaktiva bygga Datatransformationer. Felsökningsläge innehåller en data prep miljö för utveckling för flödet av data.
