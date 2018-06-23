---
title: Microsoft Translator tecken API räknar | Microsoft Docs
description: Hur Microsoft översättare Text API räknar tecken.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/20/2017
ms.author: v-jansko
ms.openlocfilehash: ebe3e3606a0413730e1fbfd704a6403f77275f89
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352584"
---
# <a name="how-the-microsoft-translator-text-api-counts-characters"></a>Hur Microsoft översättare Text API antal tecken

Microsoft Translator räknar alla tecken i indata. Tecken i Unicode-mening, inte byte. Unicode surrogat antal två tecken. Tomt utrymme och markup räknas som tecken. Längden på svaret spelar ingen roll.

Anrop till metoder för att identifiera och BreakSentence räknas inte i förbrukningen tecken. Men förväntar vi sig att anrop för att identifiera och BreakSentence metoder är i en rimlig proportion till användning av andra funktioner som ska räknas. Microsoft förbehåller sig rätten att starta inventering identifiera och BreakSentence. 

Översätta erbjudanden automatisk identifiering om du utelämnar parametern från språk. 

Mer information om antal tecken är i den [Microsoft översättare FAQ](https://www.microsoft.com/en-us/translator/faq.aspx).
