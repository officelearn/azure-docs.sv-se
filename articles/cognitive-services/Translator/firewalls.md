---
title: Översätt bakom brand väggar – Translator Text API
titleSuffix: Azure Cognitive Services
description: Översätt bakom IP-brandväggar med Translator Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 3d5c775d24c89d126962b6c4bccb4d5a572801ac
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906769"
---
# <a name="how-to-translate-behind-ip-firewalls-with-the-translator-text-api"></a>Så här översätter du bakom IP-brandväggar med Translator Text API

Translator Text API kan översätta bakom brand väggar med antingen domän namn eller IP-filtrering. Filtrering av domän namn är den bästa metoden. Vi **rekommenderar inte** att du kör Microsoft Translator bakom en IP-filtrerad brand vägg. Installations programmet kommer sannolikt att brytas i framtiden utan föregående meddelande.

## <a name="translator-ip-addresses"></a>Översättnings-IP-adresser
IP-adresserna för api.cognitive.microsofttranslator.com – Microsoft Translator Text API från och med den 21 augusti 2019:

* **Asien och stillahavsområdet:** 20.40.125.208, 20.43.88.240, 20.184.58.62, 40.90.139.163, 104.44.89.44
* **Östeuropa** 40.90.138.4, 40.90.141.99, 51.105.170.64, 52.155.218.251
* **Nordamerika:** 40.90.139.36, 40.90.139.2, 40.119.2.134, 52.224.200.129, 52.249.207.163


## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Översätt bakom IP-brandväggar i Translator API-anrop](reference/v3-0-translate.md)
