---
title: Översätt bakom brandväggar - Translator Text API
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services Translator Text API kan översätta bakom brandväggar med antingen domännamn eller IP-filtrering.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: cd7904fedd3ab3f64315cb6f98d99b8fd12254f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837392"
---
# <a name="how-to-translate-behind-ip-firewalls-with-the-translator-text-api"></a>Så här översätter du bakom IP-brandväggar med Translator Text API

Translator Text API kan översätta bakom brandväggar med antingen domännamn eller IP-filtrering. Filtrering av domännamn är den metod som föredras. Vi **rekommenderar inte att** du kör Microsoft Translator bakom en IP-filtrerad brandvägg. Installationen kommer sannolikt att bryta i framtiden utan förvarning.

## <a name="translator-ip-addresses"></a>ÖVERSÄTTARE IP-adresser
IP-adresserna för api.cognitive.microsofttranslator.com - Microsoft Translator Text API från och med 21 augusti 2019:

* **Asien och Stillahavsområdet:** 20.40.125.208, 20.43.88.240, 20.184.58.62, 40.90.139.163, 104.44.89.44
* **Europa:** 40.90.138.4, 40.90.141.99, 51.105.170.64, 52.155.218.251
* **Nordamerika:** 40.90.139.36, 40.90.139.2, 40.119.2.134, 52.224.200.129, 52.249.207.163


## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Översätt bakom IP-brandväggar i ditt Translator API-anrop](reference/v3-0-translate.md)
