---
title: Programinställningar - LUIS
titleSuffix: Azure Cognitive Services
description: Programinställningar för Azure Cognitive Services språköverensningsappar lagras i appen och portalen.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270809"
---
# <a name="application-settings"></a>Programinställningar

Dessa programinställningar lagras i den [exporterade](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) appen och [uppdateras](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) med REST-API:erna. Om du ändrar inställningarna för appversionen återställs appens träningsstatus till otränad.

|Inställning|Standardvärde|Anteckningar|
|--|--|--|
|Normaliserapunktsmanövrering|True|Tar bort skiljetecken.|
|NormaliseraDiakritiska tecken|True|Tar bort diakritiska tecken.|

## <a name="diacritics-normalization"></a>Normalisering av diakritiska tecken

Aktivera uttrycksnormalisering för diakritiska tecken till luis `settings` Json-appfilen i parametern.

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
]
```

Följande yttranden visar hur diakritiska tecken normalisering påverkar yttranden:

|Med diakritiska tecken inställd på falska|Med diakritiska tecken inställd på true|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>Språkstöd för diakritiska tecken

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>Brasilianska `pt-br` portugisiska diakritiska tecken

|Diakritiska tecken inställd på false|Diakritiska tecken inställd på true|
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

#### <a name="dutch-nl-nl-diacritics"></a>Nederländska `nl-nl` diakritiska tecken

|Diakritiska tecken inställd på false|Diakritiska tecken inställd på true|
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

#### <a name="french-fr--diacritics"></a>Franska `fr-` diakritiska tecken

Detta inkluderar både franska och kanadensiska subkulturer.

|Diakritiska tecken inställd på false|Diakritiska tecken inställd på true|
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

#### <a name="german-de-de-diacritics"></a>Tyska `de-de` diakritiska tecken

|Diakritiska tecken inställd på false|Diakritiska tecken inställd på true|
|--|--|
|`ä`|`a`|
|`ö`|`o`|
|`ü`|`u`|

#### <a name="italian-it-it-diacritics"></a>Italienska `it-it` diakritiska tecken

|Diakritiska tecken inställd på false|Diakritiska tecken inställd på true|
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

#### <a name="spanish-es--diacritics"></a>Spanska `es-` diakritiska tecken

Detta inkluderar både spanska och kanadensiska mexikanska.

|Diakritiska tecken inställd på false|Diakritiska tecken inställd på true|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>Interpunktionnormalisering

Aktivera uttrycksnormalisering för interpunktion till luis Json-appfilen i parametern. `settings`

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
]
```

Följande yttranden visar hur skiljetecken påverkar yttranden:

|Med interpunktion inställd på Falskt|Med interpunktion inställd på Sant|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Skiljetecken har tagits bort

Följande interpunktion tas `NormalizePunctuation` bort med är inställd på true.

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
