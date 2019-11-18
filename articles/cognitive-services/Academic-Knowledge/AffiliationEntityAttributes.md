---
title: Attribut för entiteten anknytning i Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Lär dig de attribut som du kan använda med entiteten anknytning i Academic Knowledge API.
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: 52dcccebe1a1cbab7a6afadeb6b543b34b8b1eb7
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74143934"
---
# <a name="affiliation-entity"></a>Anknytnings enhet

> [!NOTE]
> Följande attribut är speciella för en anknytnings enhet. (Ty = ' 5 ')

Namn | Beskrivning | Typ | Åtgärder
--- | --- | --- | ---
AfN | Namn på anknytning, normaliserad |Sträng |Lika med
CC | Totalt antal hänvisningar i anknytning |Int32        |ingen  
DAfN | Visnings namn för anknytning |Sträng |ingen
E | Utökade metadata</br></br>**Viktigt**: det här attributet är inaktuellt och stöds endast för äldre program. Att begära det här attributet individuellt (t. ex. attribut = ID, ti, E) resulterar i att alla utökade metadata returneras i en *serialiserad JSON-sträng*</br></br>Alla attribut som finns i utökade metadata är nu tillgängliga som ett Top-attribut och kan begäras som sådana (t. ex. attribut = ID, ti, DOI, IA) | [Extratextrad](#extended) | Ingen
ECC | Sammanlagt antal beräknade antal hänvisningar i anknytning |Int32 |ingen
Id | Enhets-ID |Int64 |Lika med
PC | Totalt antal publikationer skrivna i anknytning till den här entiteten | Int32 | Ingen

## <a name="extended"></a>Extratextrad

> [!IMPORTANT]
> Det här attributet är inaktuellt och stöds endast för äldre program. Att begära det här attributet individuellt (t. ex. attribut = ID, ti, E) resulterar i att alla utökade metadata returneras i en *serialiserad JSON-sträng*</br></br>Alla attribut som finns i utökade metadata är nu tillgängliga som ett Top-attribut och kan begäras som sådana (t. ex. attribut = ID, ti, DOI, IA)

> [!IMPORTANT]
> Stöd för att begära enskilda utökade attribut med hjälp av "E." omfånget, t. ex. "E. DN" är inaktuellt. Även om det fortfarande är tekniskt tekniskt stöds, begär enskilda utökade attribut med hjälp av "E". omfattning resulterar i att attributvärdet returneras på två platser i JSON-svaret, som en del av objektet "E" och som ett attribut på den översta nivån.

Namn | Beskrivning | Typ | Åtgärder
--- | --- | --- | ---
PC | Totalt antal publikationer skrivna i anknytning till den här entiteten | Int32 | Ingen
