---
title: Azure Marketplace SEO vägledning
description: Ger vägledning om hur du maximerar sökmotoroptimering (SEO).
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: dsindona
ms.openlocfilehash: 761cdc2233bce3619d4c2c9ce1d7d7177d3bc407
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280158"
---
# <a name="azure-marketplace-seo-guidance"></a>Azure Marketplace SEO vägledning

I den här artikeln beskrivs hur du maximerar erbjudandets upptäckbarhet genom sökfunktionerna på [Azure Marketplace](https://azuremarketplace.microsoft.com) och [AppSource](https://appsource.microsoft.com). 


## <a name="general-explanation-of-algorithm"></a>Allmän förklaring av algoritm

Microsoft-marknadsplatser använder Azure Cognitive Search för att driva webbplatsens sökfunktioner. Algoritmen är baserad på termen frekvens-omvänd dokumentfrekvens ([TF-IDF](https://en.wikipedia.org/wiki/Tf–idf)). Standarden [Lucene Analyzer](https://lucene.apache.org/core/) används.

I allmänhet alla textfält, kategorier och branscher och inkluderade i relevansens viktning. Specialiserade termer som används sällan av appar men som ofta i appen genererar en högre matchpoäng med sökning. Så inklusive termer som "VM" skulle erbjuda liten nytta medan "Azure-sökning" skulle vara mycket mer specialiserade.
Nedan finns de mest relevanta fälten att tänka på.

 
|  Field                   | Betydelse | Riktlinjer                                                                                            |
|  --------------------    | ----------                   | ---------------                                                                   |
| Erbjudandets namn               |  Hög      | Exakt eller nära en fullständig matchning med sökfrågan ger hög ranking.                       |
| Utgivarens namn           |  Hög      | Exakt eller nära en fullständig matchning med sökfrågan ger hög ranking.                       |
| Kort beskrivning        |  Medel    | Med tanke på namngivning av appar och utgivarens namn garanterar nästan en hög ranking, kanske det inte är den mest relevanta. I det här fallet är en kort beskrivning kritisk. Håll texten koncis och till saken. Nyckelord och förväntade söktermer bör inkluderas för bästa resultat.  Till exempel "Detta är den bästa Retail POS byggd helt ovanpå Dynamics 365" är mindre effektiv än "Retail POS (point of sale) för Dynamics 365".  | 
| Lång beskrivning         |  Låg       | Beskrivning erbjuder ett sätt att gå in mer på djupet. De mest effektiva beskrivningarna är av rimlig längd och nyckelord används.  En to-the-point beskrivningar med nyckelord kommer att gynna mer än lång, lång text. Kontrollera att nyckeltermer, till exempel "IoT", finns i beskrivningen.  |
| Produktkategorier       | Medel     |  Produktkategorier bestäms av en kombination av utgivarval och Microsoft. Välj dessa kategorier på rätt sätt så att användarna enkelt kan hitta apparna i rätt kategori. |
|  |  |  |


## <a name="other-tips"></a>Andra tips

-   Sökningen föreslår får tung användaraktivitet. Den prioriterar matchningar mot appens namn/utgivare. Sammanfattning blir nyckelfältet för när söktermen inte är en exakt matchning med utgivaren/appnamnet.
-   Dokument för nedladdning ingår inte i sökviktningen.
-   Dina appar faktiska förvärv och användning kommer att påverka sökning ranking också. Till exempel, två likvärdiga appar där man har betydligt fler användare kommer att få en högre ranking.
