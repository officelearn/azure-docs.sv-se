---
title: Migrera från Custom Speech Service till Speech Services
titlesuffix: Azure Cognitive Services
description: Custom Speech Service är nu en del av tal-tjänst. Växla till tal-tjänsten för att dra nytta av de senaste uppdateringarna kvalitet och funktion.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 594233b9e345f9578c218b042a64ea167d50addb
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55211040"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>Migrera från Custom Speech Service till Speech-tjänsten

Använd den här artikeln för att migrera dina program från Custom Speech Service till Speech-tjänsten.

Custom Speech Service är nu en del av tal-tjänst. Växla till tal-tjänsten för att dra nytta av de senaste uppdateringarna kvalitet och funktion.

## <a name="migration-for-new-customers"></a>Migrering för nya kunder

Prismodellen är enklare med hjälp av en timme-baserade prismodellen för tjänsten tal.  

1. Skapa en Azure-resurs i varje region där ditt program är tillgänglig. Azure resursnamnet är **tal**. Du kan använda en enda Azure-resurs för följande tjänster i samma region, istället för att skapa separata resurser:

    * Tal till text
    * Anpassad tal till text
    * Text till tal
    * Talöversättning

2. Ladda ned den [tal SDK](speech-sdk.md).

3. Följ de snabbstartguider och SDK-exempel för att använda rätt API: er. Om du använder REST-API: er, måste du också använda rätt slutpunkterna och resursnycklar.

4. Uppdatera klientprogram för att använda tal-tjänst och API: er.

> [!NOTE]
> * Om du har aktiverat tal i Språkförståelse (LUIS) fungerar en enskild LUIS-resurs i samma region för LUIS samt alla taltjänster. Mer information finns i [identifierar intentioner från tal](how-to-recognize-intents-from-speech-csharp.md).
> * Översättning av text-till-text är inte en del av tal-tjänst. Den här funktionen kräver en egen prenumeration på Azure-resurs.
 


## <a name="migration-for-existing-customers"></a>Migrering för befintliga kunder

Migrera dina befintliga resursnycklar till Speech-tjänsten på Speech Service-portalen. Använd följande steg:

> [!NOTE]
> Resursnycklar kan bara migreras inom samma region.

1. Logga in på den [cris.ai](http://www.cris.ai) portal och välj prenumerationen i den övre högra menyn.

2. Välj **migrera valda prenumerationen**.

3. Ange prenumerationsnyckeln i textrutan och välj **migrera**.

## <a name="next-steps"></a>Nästa steg

* [Prova Speech Service kostnadsfritt](get-started.md).
* Lär dig [tal till text](./speech-to-text.md) begrepp.

## <a name="see-also"></a>Se också

* [Vad är Speech Service](overview.md)
* [Med Taltjänsten och SDK-dokumentation](speech-sdk.md#get-the-sdk)
