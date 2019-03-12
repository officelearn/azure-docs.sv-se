---
title: Migrera från Custom Speech Service till Speech Services
titlesuffix: Azure Cognitive Services
description: Custom Speech Service är nu en del av tal-tjänst. Växla till tal-tjänsten för att dra nytta av de senaste uppdateringarna kvalitet och funktion.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 44e13a301117ffe11e978666a38897ffd878c223
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551286"
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

## <a name="migration-for-existing-customers"></a>Migrering för befintliga kunder

Migrera dina befintliga resursnycklar till Speech-tjänsten på Speech Service-portalen. Använd följande steg:

> [!NOTE]
> Resursnycklar kan bara migreras inom samma region.

1. Logga in på den [cris.ai](https://www.cris.ai) portal och välj prenumerationen i den övre högra menyn.

2. Välj **migrera valda prenumerationen**.

3. Ange prenumerationsnyckeln i textrutan och välj **migrera**.

## <a name="next-steps"></a>Nästa steg

* [Prova Speech Service kostnadsfritt](get-started.md).
* Lär dig [tal till text](./speech-to-text.md) begrepp.

## <a name="see-also"></a>Se också

* [Vad är Speech Service](overview.md)
* [Med Taltjänsten och SDK-dokumentation](speech-sdk.md#get-the-sdk)
