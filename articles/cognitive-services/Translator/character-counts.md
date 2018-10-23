---
title: Tecken antal - API för textöversättning
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
ms.openlocfilehash: 8e04158d34765b8a077fc56f2108ea6d7b4b03a6
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2018
ms.locfileid: "49649202"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Hur Translator Text API räknar tecken

Translator Text API räknar varje tecken av indata. Tecken i Unicode-mening, inte byte. Unicode surrogat antal som två tecken. Tomt utrymme och kodtyp räknas som tecken. Längden på svaret spelar ingen roll.

Anrop till metoder för identifiering och BreakSentence räknas inte tecknet förbrukningen. Men förväntar vi sig att anropen för att identifiera och BreakSentence metoder är i en rimlig proportion till användningen av andra funktioner som räknas. Microsoft förbehåller sig rätten att utgår identifierings- och BreakSentence.

Mer information om antalet tecken som tillhör den [Microsoft Translator FAQ](https://www.microsoft.com/en-us/translator/faq.aspx).
