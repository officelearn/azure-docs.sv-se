---
title: Migrera från Custom Speech Service till Taltjänst
titlesuffix: Azure Cognitive Services
description: Lär dig hur du migreringen från Custom Speech service till Speech-tjänsten.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: panosper
ms.openlocfilehash: 656a5df21d1f7dd2e7662dc3a0415581eae02b00
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49471245"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>Migrera från Custom Speech-tjänsten till Speech-tjänsten

Använd den här artikeln för att migrera dina program från Custom Speech-tjänsten till Speech-tjänsten.

Custom Speech Service är nu en del av tal-tjänst. Växla till tal-tjänsten för att dra nytta av de senaste uppdateringarna kvalitet och funktion.
 
## <a name="migration-for-new-customers"></a>Migrering för nya kunder

Prismodellen är enklare flytta till en timme-baserade prismodellen för Speech-tjänsten.   

1. Skapa en Azure-resurs i varje region där ditt program är tillgänglig. Azure resursnamnet är **tal**. Du kan använda en enda Azure-resurs för följande tjänster i samma region, istället för att skapa separata resurser:

    * Tal till text
    * Anpassad tal till text
    * Text till tal
    * Talöversättning

2. Ladda ned den [tal SDK](speech-sdk.md). 

3. Följ de snabbstartguider och SDK-exempel för att använda rätt API: er. Om du använder REST-API: er, måste du också använda rätt slutpunkterna och resursnycklar. 

4. Uppdatera klientprogram för att använda tal-tjänst och API: er. 

> [!NOTE]
> * LUIS - om du har aktiverat tal i Språkförståelse (LUIS), en enskild LUIS-resurs i samma region fungerar för LUIS samt alla taltjänster. Se [identifierar intentioner från tal](how-to-recognize-intents-from-speech-csharp.md) dokumentation.
> * Översättning av text-till-text är inte en del av tal-tjänst. Den behöver en egen Azure-resursprenumeration.
  


## <a name="migration-for-existing-customers"></a>Migrering för befintliga kunder

Befintliga kunder måste migrera sina befintliga resursnycklar till tal-tjänsten på Speech Service-portalen. Använd följande steg: 

> [!NOTE] 
> Resursnycklar kan bara migreras inom samma region. 

1. Logga in på den [cris.ai](http://www.cris.ai) portal och välj prenumerationen i den övre högra menyn. 

2. Välj **migrera valda prenumerationen**.

3. Ange prenumerationsnyckeln i textrutan och välj **migrera**.

## <a name="next-steps"></a>Nästa steg

* [Prova Speech Service kostnadsfritt](get-started.md)
* Lär dig [tal till text](./speech-to-text.md) begrepp

## <a name="see-also"></a>Se också

* [Vad är Speech-tjänsten](overview.md)
* [Med Taltjänsten och SDK-dokumentation](speech-sdk.md#get-the-sdk)