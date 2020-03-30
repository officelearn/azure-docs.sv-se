---
title: Azure FarmBeats-arkitektur
description: Beskriver arkitekturen för Azure FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: d64c2175072d9979cfda2ea5f75beb34d3ad0d6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482379"
---
# <a name="integration-patterns"></a>Integreringsmönster

Azure FarmBeats är ett business-to-business-erbjudande som är tillgängligt på Azure Marketplace. FarmBeats möjliggör aggregering av jordbruksdatauppsättningar över leverantörer och generering av användbara insikter genom att bygga modeller för artificiell intelligens (AI) eller Maskininlärning (ML) genom att smälta datauppsättningarna.

![Projekt Farm Beats](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

I följande avsnitt beskrivs integrationsmönstret för Azure FarmBeats.

## <a name="why-integrate-with-azure-farmbeats"></a>Varför integreras med Azure FarmBeats?

Det här avsnittet fokuserar på partner som vill integrera sina datasystem (som sensorer, drönare, väderstationer) i Azure FarmBeats.

Azure FarmBeats är ett utökningsbart erbjudande som gör det möjligt för jordbruksföretag att lägga till sina olika historiska och realtidsbaserade jordbruksdatauppsättningar i en enda plattform. Azure FarmBeats hjälper ett jordbruksföretag att normalisera, kontextualisera och aggregera sina data i samband med en servergrupp.

Genom att bli datapartner med Azure FarmBeats kan du öppna systemen för bredare användning och nå ut till fler kunder med dina dataerbjudanden. Azure FarmBeats tillhandahåller ett utökningsbart API-lager som kallas Datahub, vilket hjälper dig att använda data från dina enheter systematiskt och in i ett standardiserat schema.

När data är tillgängliga i dina kunders Azure FarmBeats-instans kan dina kunder skapa rikare analyser och verktyg ovanpå dina data.

## <a name="next-steps"></a>Nästa steg

Mer information om integrering av sensordata finns i [integrering av sensordata](sensor-partner-integration-in-azure-farmbeats.md) och integrering av bildpartner finns i [bildpartnerintegration](imagery-partner-integration-in-azure-farmbeats.md).
