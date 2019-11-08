---
title: Azure FarmBeats-arkitektur
description: Beskriver arkitekturen i Azure FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 8eaba4c2a987f47c56169af138ef380835d56740
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797990"
---
# <a name="integration-patterns"></a>Integreringsmönster

Azure FarmBeats är ett erbjudande för affärs till företag som finns på Azure Marketplace. FarmBeats möjliggör agg regering av jordbruks data uppsättningar mellan leverantörer och skapandet av användbara insikter genom att bygga artificiell intelligens (AI) eller Machine Learning (ML) genom att fusing data uppsättningarna.

![Taktslag i projekt grupp](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

I följande avsnitt beskrivs integrerings mönstret för Azure FarmBeats.

## <a name="why-integrate-with-azure-farmbeats"></a>Varför integreras med Azure FarmBeats?

Det här avsnittet fokuserar på partners som vill integrera sina data system (t. ex. sensorer, drönare, väder stationer) i Azure FarmBeats.

Azure FarmBeats är ett utöknings Bart erbjudande som gör det möjligt för jordbruks företag att lägga till sina olika historiska och real tids data uppsättningar i en enda plattform. Azure FarmBeats hjälper en jord handel att normalisera, sätta och samla in sina data i en server grupps kontext.

Genom att bli en data partner med Azure FarmBeats kan du öppna dina system för att komma igång och kontakta fler kunder med dina data erbjudanden. Azure FarmBeats tillhandahåller ett utöknings Bart API-lager som kallas data hubb, som hjälper dig att mata in data från dina enheter systematiskt och till ett standardiserat schema.

När data är tillgängliga i dina kunders Azure FarmBeats-instans kan dina kunder bygga bättre analyser och verktyg ovanpå dina data.

## <a name="next-steps"></a>Nästa steg

Mer information om integrering av sensor data finns i integrering av [sensor data](sensor-partner-integration.md) och för integrering av bilder-partner, se [bilder partner integration](imagery-partner-integration.md).
