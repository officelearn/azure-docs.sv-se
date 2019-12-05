---
title: Migrera från Custom Speech Service till tal-tjänsten
titleSuffix: Azure Cognitive Services
description: Custom Speech Service är nu en del av tal tjänsten. Växla till tal tjänsten för att dra nytta av de senaste kvalitets-och funktions uppdateringarna.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 9c93286329316d081f8fd99ebd360195931b7b09
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805934"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>Migrera från Custom Speech Service till tal tjänsten

Använd den här artikeln för att migrera dina program från Custom Speech Service till tal tjänsten.

Custom Speech Service är nu en del av tal tjänsten. Växla till tal tjänsten för att dra nytta av de senaste kvalitets-och funktions uppdateringarna.

## <a name="migration-for-new-customers"></a>Migrering för nya kunder

Pris sättnings modellen är enklare och använder en timmes pris modell för tal tjänsten.  

1. Skapa en Azure-resurs i varje region där ditt program är tillgänglig. Azure resursnamnet är **tal**. Du kan använda en enda Azure-resurs för följande tjänster i samma region, istället för att skapa separata resurser:

    * Tal till text
    * Anpassad tal till text
    * Text till tal
    * Talöversättning

2. Ladda ned den [tal SDK](speech-sdk.md).

3. Följ de snabbstartguider och SDK-exempel för att använda rätt API: er. Om du använder REST-API: er, måste du också använda rätt slutpunkterna och resursnycklar.

4. Uppdatera klient programmet för att använda tal tjänsten och API: er.

## <a name="migration-for-existing-customers"></a>Migrering för befintliga kunder

Migrera dina befintliga resurs nycklar till tal tjänsten på tal tjänst portalen. Använd följande steg:

> [!NOTE]
> Resursnycklar kan bara migreras inom samma region.

1. Logga in på den [cris.ai](https://cris.ai/Home/CustomSpeech) portal och välj prenumerationen i den övre högra menyn.

2. Välj **migrera valda prenumerationen**.

3. Ange prenumerationsnyckeln i textrutan och välj **migrera**.

## <a name="next-steps"></a>Nästa steg

* [Prova röst tjänsten kostnads fritt](get-started.md).
* Lär dig [tal till text](./speech-to-text.md) begrepp.

## <a name="see-also"></a>Se också

* [Vad är tal tjänsten](overview.md)
* [Dokumentation om Speech service och Speech SDK](speech-sdk.md#get-the-sdk)
