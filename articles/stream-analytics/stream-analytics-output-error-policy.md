---
title: Utdata fel principer i Azure Stream Analytics
description: Läs mer om principer som är tillgängliga i Azure Stream Analytics utdata felhantering.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: b31530966d2c5ca9a3f82f3e74ba349e66053a83
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478933"
---
# <a name="azure-stream-analytics-output-error-policy"></a>Felprincip för Azure Stream Analytics-utdata
Den här artikeln beskriver den utgående data felhantering principer som kan konfigureras i Azure Stream Analytics.

Utdata felhantering principerna gäller endast för datakonverteringsfel som uppstår när händelsen utdata som genereras av ett Stream Analytics-jobb följer inte schemat för mål-mottagare. Du kan konfigurera den här principen genom att välja antingen **försök** eller **släppa**. I Azure-portalen när den är i ett Stream Analytics-jobb **konfigurera**väljer **Felprincip** att göra ditt val.

![Azure Stream Analytics fel Principplatsen](./media/stream-analytics-output-error-policy/stream-analytics-error-policy-locate.png)


## <a name="retry"></a>Försök igen
När ett fel inträffar, Azure Stream Analytics återförsök skriva händelsen på obestämd tid tills skrivningen lyckas. Det finns ingen tidsgräns för återförsök. Så småningom blockeras alla efterföljande händelser från bearbetningen av den händelse som försöker på nytt. Det här alternativet är utdata standardfelet hantering av principen.

## <a name="drop"></a>Ta bort
Azure Stream Analytics kommer att släppa alla utdatahändelse som resulterar i ett datakonverteringsfel. Avbrutna händelser kan inte återställas för att bearbeta om senare.


Alla tillfälliga fel (till exempel nätverksfel) görs oavsett utdata-konfiguration för felhantering.


## <a name="next-steps"></a>Nästa steg
[Felsökningsguide för Azure Stream Analytics](stream-analytics-troubleshooting-guide.md)
