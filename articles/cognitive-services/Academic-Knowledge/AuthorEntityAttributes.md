---
title: Redigera entitets-attribut – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Lär dig de attribut som du kan använda med entiteten Author i Academic Knowledge API.
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: d5fc770c380397f605f8810fa41d3a8907f2358e
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146490"
---
# <a name="author-entity"></a>Redigera entitet

> [!NOTE]
> Följande attribut är speciella för Author-entiteten. (Ty = ' 1 ')

Namn | Beskrivning | Typ | Åtgärder
--- | --- | --- | ---
Id      | Enhets-ID                             |Int64      |Lika med
AuN     | Redigera normaliserat namn                    |Sträng     |Lika med
CC      | Redigera totalt antal käll hänvisningar           |Int32      |Ingen  
DAuN    | Författarens visnings namn                   |Sträng     |Ingen
E | Utökade metadata</br></br>**Viktigt**: det här attributet är inaktuellt och stöds endast för äldre program. Att begära det här attributet individuellt (t. ex. attribut = ID, ti, E) resulterar i att alla utökade metadata returneras i en *serialiserad JSON-sträng*</br></br>Alla attribut som finns i utökade metadata är nu tillgängliga som ett Top-attribut och kan begäras som sådana (t. ex. attribut = ID, ti, DOI, IA) | [Extratextrad](#extended) | Ingen
ECC     | Författare totalt antal uppskattat antal hänvisningar |Int32      |Ingen
LKA. AfId | Enhets-ID för den senast kända anknytningen som hittades för författaren | Int64 | Ingen
LKA. AfN | Normaliserat namn på den senast kända anknytningen som har hittats för författaren | Sträng | Ingen
PC | Totalt antal publikationer för författare | Int32 | Ingen

## <a name="extended"></a>Extratextrad

> [!IMPORTANT]
> Det här attributet är inaktuellt och stöds endast för äldre program. Att begära det här attributet individuellt (t. ex. attribut = ID, ti, E) resulterar i att alla utökade metadata returneras i en *serialiserad JSON-sträng*</br></br>Alla attribut som finns i utökade metadata är nu tillgängliga som ett Top-attribut och kan begäras som sådana (t. ex. attribut = ID, ti, DOI, IA)

> [!IMPORTANT]
> Stöd för att begära enskilda utökade attribut med hjälp av "E." omfånget, t. ex. "E. DN" är inaktuellt. Även om det fortfarande är tekniskt tekniskt stöds, begär enskilda utökade attribut med hjälp av "E". omfattning resulterar i att attributvärdet returneras på två platser i JSON-svaret, som en del av objektet "E" och som ett attribut på den översta nivån.

Namn | Beskrivning | Typ | Åtgärder
--- | --- | --- | ---
LKA. AfId | Enhets-ID för den senast kända anknytningen som hittades för författaren | Int64 | Ingen
LKA. AfN | Normaliserat namn på den senast kända anknytningen som har hittats för författaren | Sträng | Ingen
PC | Totalt antal publikationer för författare | Int32 | Ingen
