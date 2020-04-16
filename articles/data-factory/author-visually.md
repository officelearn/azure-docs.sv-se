---
title: Visuell redigering
description: Lär dig hur du använder visuell redigering i Azure Data Factory
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
ms.reviewer: ''
manager: anandsub
ms.date: 12/19/2019
ms.openlocfilehash: e7de92878dac72470c0b65d1cf18c1a2d526a0bb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418498"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Visuell redigering i Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Med ux-upplevelsen (Azure Data Factory User Interface Experience) kan du skapa och distribuera resurser för din datafabrik visuellt utan att behöva skriva någon kod. Du kan dra aktiviteter till en pipeline-arbetsyta, utföra testkörningar, felsöka iterativt och distribuera och övervaka dina pipeline-körningar.

För närvarande stöds Azure Data Factory UX endast i Microsoft Edge och Google Chrome.

## <a name="authoring-canvas"></a>Skapa arbetsyta

Om du vill öppna **redigeringsduken**klickar du på pennikonen. 

![Skapa arbetsyta](media/author-visually/authoring-canvas.png)

Här ska du skapa pipelines, aktiviteter, datauppsättningar, länkade tjänster, dataflöden, utlösare och integrationskörningar som utgör din fabrik. Information om hur du kommer igång med att skapa en pipeline med redigeringsarbetsytan finns i [Kopiera data med hjälp av kopieringen Aktivitet](tutorial-copy-data-portal.md). 

Standardupplevelsen för visuell redigering arbetar direkt med datafabrikstjänsten. Azure Repos Git- eller GitHub-integrering stöds också för att tillåta källkontroll och samarbete för arbete med dina datafabrikspipelor. Mer information om skillnaderna mellan dessa redigeringsupplevelser finns [i Källkontroll i Azure Data Factory](source-control.md).

## <a name="expressions-and-functions"></a>Uttryck och funktioner

Uttryck och funktioner kan användas i stället för statiska värden för att ange många egenskaper i Azure Data Factory.

Om du vill ange ett uttryck för ett egenskapsvärde väljer du **Lägg till dynamiskt innehåll** eller klickar på Alt + **P** medan du fokuserar på fältet.

![Lägga till dynamiskt innehåll](media/author-visually/dynamic-content-1.png)

Då öppnas **Data Factory Expression Builder** där du kan skapa uttryck från systemvariabler som stöds, aktivitetsutdata, funktioner och användarspecificerade variabler eller parametrar. 

![Uttrycksverktyget](media/author-visually/dynamic-content-2.png)

Information om uttrycksspråket finns [i Uttryck och funktioner i Azure Data Factory](control-flow-expression-language-functions.md).

## <a name="provide-feedback"></a>Ge feedback

Välj **Feedback** om du vill kommentera funktioner eller meddela Microsoft om problem med verktyget:

![Feedback](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Nästa steg

Mer information om övervakning och hantering av pipelines finns i [Övervaka och hantera pipelines programmässigt.](monitor-programmatically.md)
