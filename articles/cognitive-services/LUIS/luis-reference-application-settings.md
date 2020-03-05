---
title: Program inställningar – LUIS
titleSuffix: Azure Cognitive Services
description: Program inställningar för Azure Cognitive Services Language förståelseing-appar lagras i appen och portalen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: d1ead09f6248a6ad14646371aa70b42b57cf8e3f
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270809"
---
# <a name="application-settings"></a>Programinställningar

Dessa program inställningar lagras i den [exporterade](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) appen och [uppdateras](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) med REST-API: erna. Om du ändrar inställningarna för program versionen återställs appens utbildnings status till inte tränad.

|Inställning|Standardvärde|Anteckningar|
|--|--|--|
|NormalizePunctuation|True|Tar bort interpunktion.|
|NormalizeDiacritics|True|Tar bort dia kritiska tecken.|

## <a name="diacritics-normalization"></a>Normalisering av dia kritiska tecken

Aktivera uttryck-normalisering för dia kritiska tecken till din LUIS JSON-app-fil i `settings`-parametern.

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
]
```

Följande yttranden visar hur dia kritiska normalisering påverkar yttranden:

|Med dia kritiska tecken inställt på falskt|Med dia kritiska tecken inställt på Sant|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>Språk stöd för dia kritiska tecken

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>Portugisiska (Brasilien) `pt-br` dia kritiska tecken

|Dia kritiska tecken har angetts till false|Dia kritiska tecken har angetts till true|
|-|-|
|`á`|`a`|
|`â`|`a`|
|`ã`|`a`|
|`à`|`a`|
|`ç`|`c`|
|`é`|`e`|
|`ê`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ô`|`o`|
|`õ`|`o`|
|`ú`|`u`|
|||

#### <a name="dutch-nl-nl-diacritics"></a>Nederländska `nl-nl` dia kritiska tecken

|Dia kritiska tecken har angetts till false|Dia kritiska tecken har angetts till true|
|-|-|
|`á`|`a`|
|`à`|`a`|
|`é`|`e`|
|`ë`|`e`|
|`è`|`e`|
|`ï`|`i`|
|`í`|`i`|
|`ó`|`o`|
|`ö`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|||

#### <a name="french-fr--diacritics"></a>Franska `fr-` dia kritiska tecken

Detta inkluderar både franska och kanadensiska underkulturer.

|Dia kritiska tecken har angetts till false|Dia kritiska tecken har angetts till true|
|--|--|
|`é`|`e`|
|`à`|`a`|
|`è`|`e`|
|`ù`|`u`|
|`â`|`a`|
|`ê`|`e`|
|`î`|`i`|
|`ô`|`o`|
|`û`|`u`|
|`ç`|`c`|
|`ë`|`e`|
|`ï`|`i`|
|`ü`|`u`|
|`ÿ`|`y`|

#### <a name="german-de-de-diacritics"></a>Tyska `de-de` dia kritiska tecken

|Dia kritiska tecken har angetts till false|Dia kritiska tecken har angetts till true|
|--|--|
|`ä`|`a`|
|`ö`|`o`|
|`ü`|`u`|

#### <a name="italian-it-it-diacritics"></a>Italienska `it-it` dia kritiska tecken

|Dia kritiska tecken har angetts till false|Dia kritiska tecken har angetts till true|
|--|--|
|`à`|`a`|
|`è`|`e`|
|`é`|`e`|
|`ì`|`i`|
|`í`|`i`|
|`î`|`i`|
|`ò`|`o`|
|`ó`|`o`|
|`ù`|`u`|
|`ú`|`u`|

#### <a name="spanish-es--diacritics"></a>Spanska `es-` dia kritiska tecken

Detta inkluderar både spanska och kanadensiska mexikanska.

|Dia kritiska tecken har angetts till false|Dia kritiska tecken har angetts till true|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>Normalisering av interpunktion

Aktivera uttryck-normalisering för interpunktion till din LUIS JSON-app-fil i `settings`-parametern.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
]
```

Följande yttranden visar hur interpunktion påverkar yttranden:

|Med skiljetecken inställt på falskt|Med skiljetecken inställt på Sant|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Interpunktion borttagen

Följande skiljetecken tas bort med `NormalizePunctuation` har värdet true.

|Skiljetecken|
|--|
|`-`|
|`.`|
|`'`|
|`"`|
|`\`|
|`/`|
|`?`|
|`!`|
|`_`|
|`,`|
|`;`|
|`:`|
|`(`|
|`)`|
|`[`|
|`]`|
|`{`|
|`}`|
|`+`|
|`¡`|
