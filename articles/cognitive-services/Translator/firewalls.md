---
title: Översätt bakom brandväggar – Translator Text API
titlesuffix: Azure Cognitive Services
description: Översätt bakom brandväggar med Translator Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: d0ebce2dd41b170a18057ca76dd3ae4cf3ce0be2
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67435988"
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
