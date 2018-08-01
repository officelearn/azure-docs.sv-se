---
title: Utdata fel principer i Azure Stream Analytics
description: Läs mer om principer som är tillgängliga i Azure Stream Analytics utdata felhantering.
services: stream-analytics
author: sidram
ms.author: sidram
manager: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/27/2018
ms.openlocfilehash: f854e8ce35ac9d0a99b4a7dc1cdc66724114a5c4
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391741"
---
# <a name="output-error-policy"></a>Felprincip för utdata
Den här artikeln beskriver den utgående data felhantering principer som kan konfigureras i Azure Stream Analytics.

Utdata felhantering principerna gäller endast för datakonverteringsfel som uppstår när händelsen utdata som genereras av ett Stream Analytics-jobb följer inte schemat för mål-mottagare. Du kan konfigurera den här principen genom att välja antingen **försök** eller **släppa**. I Azure-portalen när den är i ett Stream Analytics-jobb **konfigurera**väljer **Felprincip** att göra ditt val.

![Felprincip - plats](./media/stream-analytics-error-policy/stream-analytics-error-policy-locate.PNG)


## <a name="retry"></a>Försök igen
När ett fel inträffar, Azure Stream Analytics återförsök skriva händelsen på obestämd tid tills skrivningen lyckas. Det finns ingen tidsgräns för återförsök. Så småningom blockeras alla efterföljande händelser från bearbetningen av den händelse som försöker på nytt. Det här alternativet är utdata standardfelet hantering av principen.

## <a name="drop"></a>Ta bort
Azure Stream Analytics kommer att släppa alla utdatahändelse som resulterar i ett datakonverteringsfel. Avbrutna händelser kan inte återställas för att bearbeta om senare.


Alla tillfälliga fel (till exempel nätverksfel) görs oavsett utdata-konfiguration för felhantering.


## <a name="next-steps"></a>Nästa steg
[Felsökningsguide för Azure Stream Analytics](stream-analytics-troubleshooting-guide.md)
