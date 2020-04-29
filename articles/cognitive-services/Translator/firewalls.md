---
title: Översätt bakom brand väggar – Translator Text API
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services Translator Text API kan översätta bakom brand väggar med antingen domän namn eller IP-filtrering.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: cd7904fedd3ab3f64315cb6f98d99b8fd12254f6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "73837392"
---
# <a name="how-to-translate-behind-ip-firewalls-with-the-translator-text-api"></a>Så här översätter du bakom IP-brandväggar med Translator Text API

Translator Text API kan översätta bakom brand väggar med antingen domän namn eller IP-filtrering. Filtrering av domän namn är den bästa metoden. Vi **rekommenderar inte** att du kör Microsoft Translator bakom en IP-filtrerad brand vägg. Installations programmet kommer sannolikt att brytas i framtiden utan föregående meddelande.

## <a name="translator-ip-addresses"></a>Översättnings-IP-adresser
IP-adresserna för api.cognitive.microsofttranslator.com – Microsoft Translator Text API från och med den 21 augusti 2019:

* **Asien och Stillahavsområdet:** 20.40.125.208, 20.43.88.240, 20.184.58.62, 40.90.139.163, 104.44.89.44
* **Europa:** 40.90.138.4, 40.90.141.99, 51.105.170.64, 52.155.218.251
* **Nordamerika:** 40.90.139.36, 40.90.139.2, 40.119.2.134, 52.224.200.129, 52.249.207.163


## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Översätt bakom IP-brandväggar i Translator API-anrop](reference/v3-0-translate.md)
