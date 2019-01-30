---
title: Översätt bakom brandväggar – Translator Text API
titlesuffix: Azure Cognitive Services
description: Översätt bakom brandväggar med Translator Text API.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: v-jansko
ms.openlocfilehash: 7dec156eeb49f7b9f088fb721893f6c82239d509
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55219659"
---
# <a name="how-to-translate-behind-ip-firewalls-with-the-translator-text-api"></a>Så här att översätta bakom brandväggar med Translator Text API

Translator Text API kan översätta bakom brandväggar med domännamn eller IP-filtrering. Domännamn filtrering är den bästa metoden. Vi **rekommenderar inte** som kör Microsoft Translator bakom en IP-adress filtrerade brandväggen. Installationen är troligt att bryta i framtiden utan föregående meddelande. 

## <a name="translator-ip-addresses"></a>Translator IP-adresser
IP-adresser för api.cognitive.microsofttranslator.com - Microsoft Translator Text API från och med 20 November 2018:

* **Asien/Stillahavsområdet:** 40.90.139.163, 104.44.89.44
* **Europa:** 40.90.138.4, 40.90.141.99
* **Nordamerika:** 40.90.139.36, 40.90.139.2


## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Översätt bakom brandväggar i din Translator API-anrop](reference/v3-0-translate.md)
