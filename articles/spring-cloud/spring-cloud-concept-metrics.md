---
title: Förstå mått för Azure våren Cloud
description: Lär dig hur du granskar mått i Azure våren-molnet
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: d9c6f14b7ecfc2929dc48c11e0df1fe80303c8b1
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72039128"
---
# <a name="metrics-for-azure-spring-cloud"></a>Mått för Azure våren Cloud

Azure Monitor Metrics Explorer är en komponent i Microsoft Azure-portalen som gör det möjligt att rita diagram, visuellt korrelera trender och undersöka toppar och DIP i mått. Använd Mät Utforskaren för att undersöka hälso tillståndet och användningen av dina resurser. I Azure våren-molnet erbjuder vi två alternativ för att visa mått: diagram på sidan **program översikt** och sidan mått för service nivå.

## <a name="application-overview-page"></a>Sidan program översikt

På sidan **program översikt** för varje program visas ett mått diagram som gör att du kan utföra en snabb status kontroll av ditt program.  Gå till din Azure våren Cloud Service-sida och välj **program instrument panel**och välj sedan ett program i listan.  

Vi tillhandahåller 5 diagram med mått som uppdateras varje minut för följande:

* **Http-server fel**: Fel antal för HTTP-begäranden till din app.
* **Data i**: Byte som tagits emot av din app.
* **Data ut**: Byte som skickats till din app.
* **Begär Anden**: Förfrågningar som tagits emot av din app.
* **Genomsnittlig svars tid**: Genomsnittlig svars tid från din app.

Du kan välja ett tidsintervall för diagrammet mellan 1 timme och upp till 7 dagar.

## <a name="service-level-metric-queries"></a>Mått frågor på tjänst nivå

Med Azure våren Cloud kan du övervaka olika program mått. Läs [guiden för att komma igång](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) med Azure Monitor mått om du vill veta mer om den här tjänsten.

Om du vill granska mått data väljer du ditt mått, din **agg regering**och ditt tidsintervall.  Dessa begrepp förklaras nedan.

### <a name="aggregation"></a>Sammansättning 

Azure-avsökningar och uppdaterar mått varje minut. Azure tillhandahåller tre sätt att aggregera data för en vald tids period:

* **Totalt**: Summera alla mått som mål utdata.
* **Genomsnitt**: Använd det genomsnittliga värdet i perioden som mål utdata.
* **Max/min**: Använd värdet max/min i perioden som mål utdata.

### <a name="time-range"></a>Tidsintervall

Välj ett standard tidsintervall eller definiera ditt eget.

### <a name="modifying-the-granularity-of-your-metric-query"></a>Ändra din mått frågas granularitet

Som standard aggregerar Azure mått för alla Azure våren Cloud service-program. Använd funktionen filter om du vill granska mått på program-eller instans nivå.  
Välj **Lägg till filter**, Ställ in egenskapen till **app** och välj det mål program som du vill övervaka. Du kan också använda alternativet **Använd delning** för att rita separata rader för varje app i diagrammet.

>[!TIP]
> Du kan bygga egna diagram på sidan mått och fästa dem på din **instrument panel**. Börja med att namnge diagrammet.  Välj sedan **Fäst på instrument panelen i det övre högra hörnet**. Nu kan du kontrol lera ditt program på portalens **instrument panel**.