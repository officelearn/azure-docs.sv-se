---
title: Dataskydd i Azure Stream Analytics
description: I den här artikeln beskrivs hur du krypterar dina privata data som används av ett Azure Stream Analytics-jobb.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 1b3bdad0125b5bddbba20c8d807924fc3ea87e32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299404"
---
# <a name="data-protection-in-azure-stream-analytics"></a>Dataskydd i Azure Stream Analytics 

Azure Stream Analytics är en fullständigt hanterad plattform som en tjänst som gör att du kan skapa analyspipelpipelar i realtid. Alla tunga lyft, till exempel klusteretablering, skalningsnoder för att hantera din användning och hantera interna kontrollpunkter, hanteras bakom kulisserna.

## <a name="encrypt-your-data"></a>Kryptera dina data

Stream Analytics använder automatiskt förstklassiga krypteringsstandarder i hela sin infrastruktur för att kryptera och skydda dina data. Du kan helt enkelt lita på Stream Analytics för att lagra alla dina data på ett säkert sätt så att du inte behöver oroa dig för att hantera infrastrukturen.

Om du vill använda kundhanterade nycklar (CMK) för att kryptera dina data kan du använda ditt eget lagringskonto (det allmänna syftet V1 eller V2) för att lagra alla privata datatillgångar som krävs enligt Stream Analytics-körningen. Ditt lagringskonto kan krypteras efter behov. Ingen av dina privata datatillgångar lagras permanent av Stream Analytics-infrastrukturen. 

Den här inställningen måste konfigureras när Stream Analytics skapas och kan inte ändras under jobbets livscykel. Det rekommenderas inte att ändra eller ta bort lagringsutrymme som används av Din Stream Analytics. Om du tar bort ditt lagringskonto tar du bort alla privata datatillgångar permanent, vilket gör att jobbet misslyckas. 

Det går inte att uppdatera eller rotera nycklar till ditt lagringskonto med hjälp av Stream Analytics-portalen. Du kan uppdatera nycklarna med REST-API:erna.


## <a name="configure-storage-account-for-private-data"></a>Konfigurera lagringskonto för privata data 

Följ följande steg för att konfigurera ditt lagringskonto för privata datatillgångar. Den här konfigurationen är gjord från ditt Stream Analytics-jobb, inte från ditt lagringskonto.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure-portalen. 

1. Välj **Analytics** > **Stream Analytics-jobb** i resultatlistan. 

1. Fyll i jobbsidan Stream Analytics med nödvändig information som namn, region och skala. 

1. Markera kryssrutan som säger *Säkra alla privata datatillgångar som behövs för det här jobbet i mitt lagringskonto*.

1. Välj ett lagringskonto från din prenumeration. Observera att den här inställningen inte kan ändras under jobbets hela livscykel. 

   ![Inställningar för konto för privat datalagring](./media/data-protection/storage-account-create.png)

## <a name="private-data-assets-that-are-stored"></a>Privata datatillgångar som lagras

Alla privata data som måste sparas av Stream Analytics lagras i ditt lagringskonto. Exempel på privata datatillgångar är: 

* Frågor som du har skapat och deras relaterade konfigurationer  

* Användardefinierade funktioner 

* Kontrollpunkter som behövs av Stream Analytics-körningen

* Ögonblicksbilder av referensdata 

Anslutningsinformation om dina resurser, som används av ditt Stream Analytics-jobb, lagras också. Kryptera ditt lagringskonto för att skydda alla dina data. 

För att hjälpa dig att uppfylla dina efterlevnadsskyldigheter i en reglerad bransch eller miljö kan du läsa mer om [Microsofts efterlevnadserbjudanden](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="next-steps"></a>Nästa steg

* [Skapa ett Azure Storage-konto](../storage/common/storage-account-create.md)
* [Förstå indata för Azure Stream Analytics](stream-analytics-add-inputs.md)
* [Kontrollpunkts- och reprisbegrepp i Azure Stream Analytics-jobb](stream-analytics-concepts-checkpoint-replay.md)
* [Använda referensdata för uppslag i Stream Analytics](stream-analytics-use-reference-data.md)
