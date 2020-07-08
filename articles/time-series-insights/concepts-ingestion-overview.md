---
title: Översikt över intag – Azure Time Series Insights | Microsoft Docs
description: Lär dig mer om data inmatning i Azure Time Series Insights.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: ecee6817b58639d1b60264c818ab9517e5d133e0
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050112"
---
# <a name="azure-time-series-insights-data-ingestion-overview"></a>Översikt över Azure Time Series Insights data inmatning

Din Azure Time Series Insightss miljö innehåller en inmatnings *motor* för att samla in, bearbeta och lagra data för strömnings tids serier. När data anländer till din händelse källa (er) kommer Azure Time Series Insights att förbruka och lagra dina data i nära real tid.

[![Översikt över intag](media/concepts-ingress-overview/ingress-overview.png)](media/concepts-ingress-overview/ingress-overview.png#lightbox)

## <a name="ingestion-topics"></a>Avsnitt om inmatning

Följande artiklar behandlar data bearbetning i djupet, inklusive bästa praxis att följa:

* Läs om [händelse källor](concepts-streaming-ingestion-event-sources.md) och vägledning om hur du väljer en tids stämpling för händelse källan.

* Granska de [data typer](concepts-supported-data-types.md) som stöds

* Förstå hur inmatnings motorn använder en uppsättning [regler](./concepts-json-flattening-escaping-rules.md) för dina JSON-egenskaper för att skapa dina lagrings konto kolumner.

* Granska begränsningarna för din miljö [genom strömning](concepts-streaming-throughput-limitations.md) för att planera för dina skalnings behov.

## <a name="next-steps"></a>Nästa steg

* Fortsätt till om du vill veta mer om [händelse källor](concepts-streaming-ingestion-event-sources.md) för din Azure Time Series Insightss miljö. 
