---
title: Data skydd i Azure Stream Analytics
description: Den här artikeln beskriver hur du krypterar dina privata data som används av ett Azure Stream Analytics jobb.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 1b3bdad0125b5bddbba20c8d807924fc3ea87e32
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79299404"
---
# <a name="data-protection-in-azure-stream-analytics"></a>Data skydd i Azure Stream Analytics 

Azure Stream Analytics är en fullständigt hanterad plattform som en tjänst med vilken du kan bygga real tids analys pipeliner. All tung lyftning, till exempel kluster etablering, skalning av noder för att passa din användning och hantering av interna kontroll punkter, hanteras i bakgrunden.

## <a name="encrypt-your-data"></a>Kryptera dina data

Stream Analytics använder automatiskt optimala krypterings standarder över hela infrastrukturen för att kryptera och skydda dina data. Du kan helt enkelt lita på Stream Analytics att lagra alla dina data på ett säkert sätt så att du inte behöver oroa dig för att hantera infrastrukturen.

Om du vill använda Kundhanterade nycklar (CMK) för att kryptera dina data kan du använda ditt eget lagrings konto (generell användning v1 eller v2) för att lagra alla privata data till gångar som krävs av Stream Analytics Runtime. Ditt lagrings konto kan krypteras efter behov. Ingen av dina privata data till gångar lagras permanent av Stream Analytics-infrastrukturen. 

Den här inställningen måste konfigureras när Stream Analytics jobb skapas och kan inte ändras under jobbets livs cykel. Det rekommenderas inte att ändra eller ta bort lagrings utrymme som används av din Stream Analytics. Om du tar bort ditt lagrings konto kommer du att ta bort alla privata data till gångar permanent, vilket leder till att jobbet Miss fungerar. 

Det går inte att uppdatera eller rotera nycklar till ditt lagrings konto med hjälp av Stream Analytics portalen. Du kan uppdatera nycklarna med hjälp av REST-API: er.


## <a name="configure-storage-account-for-private-data"></a>Konfigurera lagrings konto för privata data 

Använd följande steg för att konfigurera ditt lagrings konto för privata data till gångar. Den här konfigurationen görs från ditt Stream Analytics jobb, inte från ditt lagrings konto.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure-portalen. 

1. Välj **analys** > **Stream Analytics jobb** i resultat listan. 

1. Fyll i sidan Stream Analytics jobb med nödvändig information, till exempel namn, region och skala. 

1. Markera kryss rutan med texten *skydda alla privata data till gångar som krävs av det här jobbet i mitt lagrings konto*.

1. Välj ett lagrings konto från din prenumeration. Observera att den här inställningen inte kan ändras under jobbets livs cykel. 

   ![Inställningar för lagrings konto för privata data](./media/data-protection/storage-account-create.png)

## <a name="private-data-assets-that-are-stored"></a>Privata data till gångar som lagras

Alla privata data som måste behållas av Stream Analytics lagras i ditt lagrings konto. Exempel på privata data till gångar är: 

* Frågor som du har skapat och tillhör ande konfigurationer  

* Användardefinierade funktioner 

* Kontroll punkter som krävs av Stream Analytics runtime

* Ögonblicks bilder av referens data 

Anslutnings information för dina resurser, som används av ditt Stream Analytics jobb, lagras också. Kryptera ditt lagrings konto för att skydda alla dina data. 

För att hjälpa dig att uppfylla dina krav på efterlevnad i en reglerad bransch eller miljö kan du läsa mer om [Microsofts erbjudanden för efterlevnad](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="next-steps"></a>Nästa steg

* [Skapa ett Azure Storage konto](../storage/common/storage-account-create.md)
* [Förstå indata för Azure Stream Analytics](stream-analytics-add-inputs.md)
* [Kontroll punkts-och repetitions begrepp i Azure Stream Analytics jobb](stream-analytics-concepts-checkpoint-replay.md)
* [Använda referens data för sökningar i Stream Analytics](stream-analytics-use-reference-data.md)
