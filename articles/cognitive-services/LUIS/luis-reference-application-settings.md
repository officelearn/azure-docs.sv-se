---
title: Programinställningar
titleSuffix: Azure Cognitive Services
description: Förstå inställningarna för program för Language understanding appar.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 3682b9e0c38344be1522440290b46f8c10bd5607
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275911"
---
# <a name="application-settings"></a>Programinställningar

Dessa programinställningar lagras i den [exporteras](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) app och [uppdateras](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) med REST-API: er. Ändra inställningarna för din app version återställer din status för utbildning-app till omdöme.

|Inställning|Standardvärde|Anteckningar|
|--|--|--|
|NormalizePunctuation|Sant|Tar bort skiljetecken.|
|NormalizeDiacritics|Sant|Tar bort diakritiska tecken.|

## <a name="diacritics-normalization"></a>Diakritiska tecken normalisering 

Aktivera uttryck normalisering för diakritiska tecken i LUIS JSON appfilen i den `settings` parametern.

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
] 
```

Följande uttryck visar hur diakritiska tecken normalisering påverkar yttranden:

|Med diakritiska tecken inställd på false|Med diakritiska tecken inställd på true|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>Språkstöd för diakritiska tecken

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>Portugisiska (Brasilien) `pt-br` diakritiska tecken

|Diakritiska tecken inställd på false|Diakritiska tecken är angett till true|
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

|Diakritiska tecken inställd på false|Diakritiska tecken är angett till true|
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

|Diakritiska tecken inställd på false|Diakritiska tecken är angett till true|
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

|Diakritiska tecken inställd på false|Diakritiska tecken är angett till true|
|--|--|
|`ä`|`a`|
|`ö`|`o`| 
|`ü`|`u`| 

#### <a name="italian-it-it-diacritics"></a>Italienska `it-it` diakritiska tecken

|Diakritiska tecken inställd på false|Diakritiska tecken är angett till true|
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

Detta inkluderar både spanska och kanadensiska Mexikanska.

|Diakritiska tecken inställd på false|Diakritiska tecken är angett till true|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`| 
|`ó`|`o`| 
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>Skiljetecken normalisering

Aktivera uttryck normalisering för skiljetecken i LUIS JSON appfilen i den `settings` parametern.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
] 
```

Följande uttryck visar hur diakritiska tecken påverkar yttranden:

|Med diakritiska tecken inställd på False|Med diakritiska tecken som har angetts till True|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Interpunktion

Följande punkter tas bort med `NormalizePunctuation` har angetts till true.

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
