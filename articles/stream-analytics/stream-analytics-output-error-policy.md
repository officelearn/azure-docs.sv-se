---
title: Principer för utdatafel i Azure Stream Analytics
description: Lär dig mer om principerna för hantering av utdatafel som är tillgängliga i Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 22112272bb302769e5969cf6995d486438deb41f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431614"
---
# <a name="azure-stream-analytics-output-error-policy"></a>Sekretesspolicy för utdatafel i Azure Stream Analytics
I den här artikeln beskrivs principerna för hantering av utdatafel som kan konfigureras i Azure Stream Analytics.

Principer för hantering av utdatafel gäller endast för datakonverteringsfel som uppstår när utdatahändelsen som produceras av ett Stream Analytics-jobb inte överensstämmer med schemat för målmottagaren. Du kan konfigurera den här principen genom att välja **försök igen** eller **Släpp**. I Azure-portalen, medan du är i ett Stream Analytics-jobb, under **Konfigurera**, väljer du **Felprincip** för att göra ditt val.

![Plats för Azure Stream Analytics-utdatafelprincip](./media/stream-analytics-output-error-policy/stream-analytics-error-policy-locate.png)


## <a name="retry"></a>Försök igen
När ett fel inträffar försöker Azure Stream Analytics skriva händelsen på obestämd tid tills skrivningen lyckas. Det finns ingen timeout för återförsök. Så småningom blockeras alla efterföljande händelser från bearbetning av händelsen som försöker igen. Det här alternativet är standardprincipen för hantering av utdatafel.

## <a name="drop"></a>Släppa
Azure Stream Analytics utelämnar eventuella utdatahändelser som resulterar i datakonverteringsfel. Utelämnade händelser kan inte återställas för bearbetning vid ett senare tillfälle.


Alla tillfälliga fel (till exempel nätverksfel) görs på nytt oavsett konfigurationen av principkonfigurationen för hantering av utdatafel.


## <a name="next-steps"></a>Nästa steg
[Felsökningsguide för Azure Stream Analytics](stream-analytics-troubleshooting-guide.md)
