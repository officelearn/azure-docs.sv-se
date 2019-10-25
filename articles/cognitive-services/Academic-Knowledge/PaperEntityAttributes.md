---
title: Attribut för pappers entitet – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Lär dig de attribut som du kan använda med entiteten papper i Academic Knowledge API.
services: cognitive-services
author: DarrinEide
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: c300a6477daa5759a68d5d11d40b1a71b46bd808
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793826"
---
# <a name="paper-entity"></a>Pappers enhet

<sub>* Nedan attribut är bara för entiteter för pappers enheter. (Ty = ' 0 ')</sub>

Namn | Beskrivning | Typ | Operations
--- | --- | --- | ---
'. AfId | Författarens anknytnings-ID | Int64 | Lika med
'. AfN | Författarens anknytnings namn | Sträng | Lika med, StartsWith
'. AuId | Författar-ID | Int64 | Lika med
'. AuN | Normaliserat författar namn | Sträng | Lika med, StartsWith
'. DAuN | Ursprungligt författar namn | Sträng | Inget
'. DAfN | Ursprungligt anknytnings namn | Sträng | Inget
'. Na | Numerisk position i författar lista | Int32 | Lika med
CC | Antal hänvisningar | Int32 | Inget  
C. CId | Konferens seriens ID | Int64 | Lika med
C.CN | Konferens seriens namn | Sträng | Lika med, StartsWith
D | Publicerings datum i ÅÅÅÅ-MM-DD-format | Datum | Lika med, IsBetween
Ö | Utökade metadata (se tabellen nedan) | Sträng | Gäller inte  
ECC | Beräknat antal hänvisningar | Int32 | Inget
F. DFN | Ursprungligt fält för studie namn | Sträng | Inget
F. FId | Fält för studie-ID | Int64 | Lika med
F. FN | Normaliserat fält för studie namn | Sträng | Lika med, StartsWith
Id | Pappers-ID | Int64 | Lika med
J. JId | Journal-ID | Int64 | Lika med
J. JN | Journal namn | Sträng | Lika med, StartsWith
Komma | Publikationstyp (0: okänd, 1: Journal artikel, 2: patent, 3: konferens papper, 4: bok kapitel, 5: bok, 6: bok referens post, 7: data uppsättning, 8: databas | Sträng | Lika med
Försvinner | Lista över refererade pappers-ID: n | Int64 [] | Lika med
TI | Normaliserad rubrik | Sträng | Lika med, StartsWith
W | Unika ord i rubrik | Sträng [] | Lika med
Y | År publicerat | Int32 | Lika med, IsBetween

## <a name="extended-metadata-attributes"></a>Utökade metadata-attribut ##

Namn | Beskrivning               
--- | ---
BT | BibTex-dokument typ (' a ': Journal artikel, ' b ': bok, ' c ': bok kapitel, ' p ': konferens papper)
BV | Namn på BibTex-platser
CC | Citat kontexter – lista över refererade pappers-ID: n och motsvarande kontext i papperet (t. ex. [{123: ["Brune Foxes är kända för att kunna hoppa som det hänvisas till i pappers 123", "de Lazy-hundar är en historisk misnomer som visas i pappers 123"]})
NAMNET | Original papperets rubrik
DOI | Digital objekt identifierare
FP | Första pappers sidan i publikationen
I | Publicerings problem
IA | Inverterad Sammanfattning
IA. IndexLength | Antal objekt i indexet (abstrakta ord)
IA. InvertedIndex | Lista över abstrakta ord och deras motsvarande position i den ursprungliga abstrakten (t. ex. [{": [0, 15, 30]}, {" brun ": [1]}, {" Fox ": [2]}])
LP | Sista sidan av papperet i publikationen
PT | Utgivare
S | Källor – lista över papperets webb adresser, sorterade efter statisk rangordning
S. ty | Källtyp (1: HTML, 2: text, 3: PDF, 4: DOC, 5: PPT, 6: XLS, 7: PS)
S. U | Käll-URL
Lodrät | Publikations volym
VFN | Fullständigt namn på journal-eller konferens platser
VSN | Kort namn på journal-eller konferens platser
