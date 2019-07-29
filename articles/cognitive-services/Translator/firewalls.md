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
ms.openlocfilehash: 567e869ab9ccb2f29cd0e88ba2e44d7d1b4a296c
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595268"
---
# <a name="how-to-translate-behind-ip-firewalls-with-the-translator-text-api"></a>Så här översätter du bakom IP-brandväggar med Translator Text API

Translator Text API kan översätta bakom brand väggar med antingen domän namn eller IP-filtrering. Filtrering av domän namn är den bästa metoden. Vi **rekommenderar inte** att du kör Microsoft Translator bakom en IP-filtrerad brand vägg. Installations programmet kommer sannolikt att brytas i framtiden utan föregående meddelande.

## <a name="translator-ip-addresses"></a>Översättnings-IP-adresser
IP-adresserna för api.cognitive.microsofttranslator.com-Microsoft Translator Text API den 20 november 2018:

* **Asien och stillahavsområdet:** 40.90.139.163, 104.44.89.44
* **Östeuropa** 40.90.138.4, 40.90.141.99
* **Nordamerika:** 40.90.139.36, 40.90.139.2


## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Översätt bakom IP-brandväggar i Translator API-anrop](reference/v3-0-translate.md)
