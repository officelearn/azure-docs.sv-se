---
title: Utdata fel principer i Azure Stream Analytics
description: Läs mer om principer som är tillgängliga i Azure Stream Analytics utdata felhantering.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 22112272bb302769e5969cf6995d486438deb41f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431614"
---
# <a name="azure-stream-analytics-output-error-policy"></a>Felprincip för Azure Stream Analytics-utdata
Den här artikeln beskriver den utgående data felhantering principer som kan konfigureras i Azure Stream Analytics.

Utdata felhantering principerna gäller endast för datakonverteringsfel som uppstår när händelsen utdata som genereras av ett Stream Analytics-jobb följer inte schemat för mål-mottagare. Du kan konfigurera den här principen genom att välja antingen **försök** eller **släppa**. I Azure-portalen när den är i ett Stream Analytics-jobb **konfigurera**väljer **Felprincip** att göra ditt val.

![Azure Stream Analytics fel Principplatsen](./media/stream-analytics-output-error-policy/stream-analytics-error-policy-locate.png)


## <a name="retry"></a>Försök igen
När ett fel inträffar, Azure Stream Analytics återförsök skriva händelsen på obestämd tid tills skrivningen lyckas. Det finns ingen tidsgräns för återförsök. Så småningom blockeras alla efterföljande händelser från bearbetningen av den händelse som försöker på nytt. Det här alternativet är utdata standardfelet hantering av principen.

## <a name="drop"></a>Ta bort
Azure Stream Analytics utelämnar eventuella utdatahändelser som resulterar i datakonverteringsfel. Utelämnade händelser kan inte återställas för bearbetning vid ett senare tillfälle.


Alla tillfälliga fel (till exempel nätverksfel) görs oavsett utdata-konfiguration för felhantering.


## <a name="next-steps"></a>Nästa steg
[Felsökningsguide för Azure Stream Analytics](stream-analytics-troubleshooting-guide.md)
