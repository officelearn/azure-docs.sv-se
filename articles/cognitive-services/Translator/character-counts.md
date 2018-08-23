---
title: 'Microsoft Translator Text API: et-tecknet räknar | Microsoft Docs'
description: Microsoft Translator Text API Räknar hur tecken.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/20/2017
ms.author: v-jansko
ms.openlocfilehash: 1b4987509c17e4064d7c54608395e272efa8de3b
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2018
ms.locfileid: "41988626"
---
# <a name="how-the-microsoft-translator-text-api-counts-characters"></a>Hur Microsoft Translator Text API räknar tecken

Microsoft Translator räknar varje tecken av indata. Tecken i Unicode-mening, inte byte. Unicode surrogat antal som två tecken. Tomt utrymme och kodtyp räknas som tecken. Längden på svaret spelar ingen roll.

Anrop till metoder för identifiering och BreakSentence räknas inte tecknet förbrukningen. Men förväntar vi sig att anropen för att identifiera och BreakSentence metoder är i en rimlig proportion till användningen av andra funktioner som räknas. Microsoft förbehåller sig rätten att utgår identifierings- och BreakSentence. 

Mer information om antalet tecken som tillhör den [Microsoft Translator FAQ](https://www.microsoft.com/en-us/translator/faq.aspx).
