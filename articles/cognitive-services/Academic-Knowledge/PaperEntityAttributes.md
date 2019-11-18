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
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: fc3bb5987c31fe718951a3cae02ed7c4ddc29957
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123074"
---
# <a name="paper-entity"></a>Pappers enhet

> [!NOTE]
> Nedanstående attribut är bara för entiteter av papper. (Ty = ' 0 ')

Namn | Beskrivning | Typ | Åtgärder
--- | --- | --- | ---
'. AfId | Författarens anknytnings-ID | Int64 | Lika med
'. AfN | Författarens anknytnings namn | Sträng | Lika med, StartsWith
'. AuId | Författar-ID | Int64 | Lika med
AA.AuN | Normaliserat författar namn | Sträng | Lika med, StartsWith
'. DAuN | Ursprungligt författar namn | Sträng | Ingen
'. DAfN | Ursprungligt anknytnings namn | Sträng | Ingen
'. Na | Numerisk position i författar lista | Int32 | Lika med
BT | BibTex-dokument typ (' a ': Journal artikel, ' b ': bok, ' c ': bok kapitel, ' p ': konferens papper) | Sträng | Ingen
BV | Namn på BibTex-platser | Sträng | Ingen
C. CId | Konferens seriens ID | Int64 | Lika med
C.CN | Konferens seriens namn | Sträng | Lika med, StartsWith
CC | Antal hänvisningar | Int32 | Ingen  
CitCon | Citat kontexter</br></br>Lista över refererade pappers-ID: n och motsvarande kontext i papperet (t. ex. [{123: ["Brune Foxes är kända för att kunna hoppa som det hänvisas till i papper 123", "de Lazy-hundar är en historisk misnomer som visas i pappers 123"]}) | Anpassat | Ingen
D | Publicerings datum i ÅÅÅÅ-MM-DD-format | Date | Lika med, IsBetween
UNIKT NAMN | Original papperets rubrik | Sträng | Ingen
DOI | Digital objekt identifierare | Sträng | Lika med, StartsWith
E | Utökade metadata</br></br>**Viktigt**: det här attributet är inaktuellt och stöds endast för äldre program. Att begära det här attributet individuellt (t. ex. attribut = ID, ti, E) resulterar i att alla utökade metadata returneras i en *serialiserad JSON-sträng*</br></br>Alla attribut som finns i utökade metadata är nu tillgängliga som ett Top-attribut och kan begäras som sådana (t. ex. attribut = ID, ti, DOI, IA) | [Extratextrad](#extended) | Ingen
ECC | Beräknat antal hänvisningar | Int32 | Ingen
F. DFN | Ursprungligt fält för studie namn | Sträng | Ingen
F. FId | Fält för studie-ID | Int64 | Lika med
F.FN | Normaliserat fält för studie namn | Sträng | Lika med, StartsWith
FP | Första pappers sidan i publikationen | Sträng | Lika med
I | Publicerings problem | Sträng | Lika med
IA | Inverterad Sammanfattning | [InvertedAbstract](#invertedabstract) | Ingen
Id | Pappers-ID | Int64 | Lika med
J. JId | Journal-ID | Int64 | Lika med
J. JN | Journal namn | Sträng | Lika med, StartsWith
LP | Sista sidan av papperet i publikationen | Sträng | Lika med
PT | Utgivare | Sträng | Ingen
Komma | Publikationstyp (0: okänd, 1: Journal artikel, 2: patent, 3: konferens papper, 4: bok kapitel, 5: bok, 6: bok referens post, 7: data uppsättning, 8: databas | Sträng | Lika med
Försvinner | Lista över refererade pappers-ID: n | Int64 [] | Lika med
S | Lista över käll webb adresser för papperet, sorterat efter relevans | [Källa](#source)[] | Ingen
TI | Normaliserad rubrik | Sträng | Lika med, StartsWith
V | Publikations volym | Sträng | Lika med
VFN | Fullständigt namn på journal-eller konferens platser | Sträng | Ingen
VSN | Kort namn på journal-eller konferens platser | Sträng | Ingen
W | Unika ord i rubrik | String[] | Lika med
Y | År publicerat | Int32 | Lika med, IsBetween

## <a name="extended"></a>Extratextrad
> [!IMPORTANT]
> Det här attributet är inaktuellt och stöds endast för äldre program. Att begära det här attributet individuellt (t. ex. attribut = ID, ti, E) resulterar i att alla utökade metadata returneras i en *serialiserad JSON-sträng*</br></br>Alla attribut som finns i utökade metadata är nu tillgängliga som ett Top-attribut och kan begäras som sådana (t. ex. attribut = ID, ti, DOI, IA)

> [!IMPORTANT]
> Stöd för att begära enskilda utökade attribut med hjälp av "E." omfånget, t. ex. "E. DN" är inaktuellt. Även om det fortfarande är tekniskt tekniskt stöds, begär enskilda utökade attribut med hjälp av "E". omfattning resulterar i att attributvärdet returneras på två platser i JSON-svaret, som en del av objektet "E" och som ett attribut på den översta nivån.

Namn | Beskrivning | Typ | Åtgärder
--- | --- | --- | ---
BT | BibTex-dokument typ (' a ': Journal artikel, ' b ': bok, ' c ': bok kapitel, ' p ': konferens papper) | Sträng | Ingen
BV | Namn på BibTex-platser | Sträng | Ingen
CC | Citat kontexter</br></br>Lista över refererade pappers-ID: n och motsvarande kontext i papperet (t. ex. [{123: ["Brune Foxes är kända för att kunna hoppa som det hänvisas till i papper 123", "de Lazy-hundar är en historisk misnomer som visas i pappers 123"]}) | Anpassat | Ingen
UNIKT NAMN | Original papperets rubrik | Sträng | Ingen
DOI | Digital objekt identifierare | Sträng | Ingen
FP | Första pappers sidan i publikationen | Sträng | Ingen
I | Publicerings problem | Sträng | Ingen
IA | Inverterad Sammanfattning | [InvertedAbstract](#invertedabstract) | Ingen
LP | Sista sidan av papperet i publikationen | Sträng | Ingen
PT | Utgivare | Sträng | Ingen
S | Lista över käll webb adresser för papperet, sorterat efter relevans | [Källa](#source)[] | Ingen
V | Publikations volym | Sträng | Ingen
VFN | Fullständigt namn på journal-eller konferens platser | Sträng | Ingen
VSN | Kort namn på journal-eller konferens platser | Sträng | Ingen

## <a name="invertedabstract"></a>InvertedAbstract

> [!IMPORTANT]
> InvertedAbstract-attribut kan inte direkt begäras som ett retur attribut. Om du behöver ett enskilt attribut måste du begära attributet "IA" på översta nivån, d.v.s. för att hämta IA. Attribut för IndexLength-begäran = IA

Namn | Beskrivning | Typ | Åtgärder
--- | --- | --- | ---
IndexLength | Antal objekt i indexet (abstrakta ord) | Int32 | Ingen
InvertedIndex | Lista över abstrakta ord och deras motsvarande position i den ursprungliga abstrakten (t. ex. [{": [0, 15, 30]}, {" brun ": [1]}, {" Fox ": [2]}]) | Anpassat | Ingen

## <a name="source"></a>Källa

> [!IMPORTANT]
> Det går inte att skicka käll-attribut direkt som ett retur-attribut. Om du behöver ett enskilt attribut måste du begära attribut på översta nivån, d.v.s. för att hämta S. U attribut för begäran = S

Namn | Beskrivning | Typ | Åtgärder
--- | --- | --- | ---
Ty | Käll-URL-typ (1: HTML, 2: text, 3: PDF, 4: DOC, 5: PPT, 6: XLS, 7: PS) | Sträng | Ingen
U | Käll-URL | Sträng | Ingen
