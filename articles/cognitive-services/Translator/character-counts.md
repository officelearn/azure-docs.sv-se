---
title: Tecken antal - API för textöversättning
titlesuffix: Azure Cognitive Services
description: Hur räknas Translator Text API tecken.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: v-jansko
ms.openlocfilehash: c56622ee5e25fc422d02cec6ecfaa1f847e9e769
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55226442"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Hur Translator Text API räknar tecken

Translator Text API räknar varje tecken av indata. Tecken i Unicode-mening, inte byte. Unicode surrogat antal som två tecken. Tomt utrymme och kodtyp räknas som tecken. Längden på svaret spelar ingen roll.

Anrop till metoder för identifiering och BreakSentence räknas inte tecknet förbrukningen. Men förväntar vi sig att anropen för att identifiera och BreakSentence metoder är i en rimlig proportion till användningen av andra funktioner som räknas. Microsoft förbehåller sig rätten att utgår identifierings- och BreakSentence.

Mer information om antalet tecken som tillhör den [Microsoft Translator FAQ](https://www.microsoft.com/en-us/translator/faq.aspx).
