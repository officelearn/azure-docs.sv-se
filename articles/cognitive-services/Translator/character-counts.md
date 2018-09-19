---
title: 'Räkna för Translator Text API: et-tecken'
titlesuffix: Azure Cognitive Services
description: Hur räknas Translator Text API tecken.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: v-jansko
ms.openlocfilehash: c6234a46ae55d73739dcc23110c5e0f6375c3f96
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "46128748"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Hur Translator Text API räknar tecken

Translator Text API räknar varje tecken av indata. Tecken i Unicode-mening, inte byte. Unicode surrogat antal som två tecken. Tomt utrymme och kodtyp räknas som tecken. Längden på svaret spelar ingen roll.

Anrop till metoder för identifiering och BreakSentence räknas inte tecknet förbrukningen. Men förväntar vi sig att anropen för att identifiera och BreakSentence metoder är i en rimlig proportion till användningen av andra funktioner som räknas. Microsoft förbehåller sig rätten att utgår identifierings- och BreakSentence. 

Mer information om antalet tecken som tillhör den [Microsoft Translator FAQ](https://www.microsoft.com/en-us/translator/faq.aspx).
