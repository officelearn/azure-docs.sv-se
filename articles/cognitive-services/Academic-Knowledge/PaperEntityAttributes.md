---
title: Attribut för pappers entitet – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Lär dig de attribut som du kan använda med entiteten papper i Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 4b9431469a7925d26003ad9c34f6b401e5767f6d
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704944"
---
# <a name="paper-entity"></a>Pappers enhet

<sub>* Nedan attribut är bara för entiteter för pappers enheter. (Ty = ' 0 ')</sub>


Namn    |Beskrivning                                        |type       | Åtgärder
------- | ------------------------------------------------- | --------- | ----------------------------
ID      |Enhets-id                                          |Int64      |Lika med
TI      |Pappers rubrik                                        |Sträng     |Är lika med<br/>StartsWith
M       |Pappers språk kod avgränsad\@med ""\@\@          |Sträng     |Lika med
J       |Pappers år                                         |Int32      |Är lika med<br/>IsBetween
D       |Pappers datum                                         |Date       |Är lika med<br/>IsBetween
Kopia      |Antal hänvisningar                                     |Int32      |inga  
ECC     |Beräknat antal hänvisningar                           |Int32      |inga
AA.AuN  |Upphovsmannens namn                                        |Sträng     |Är lika med<br/>StartsWith
'. AuId |Författar-ID                                          |Int64      |Lika med
'. AfN  |Författarens anknytnings namn                            |Sträng     |Är lika med<br/>StartsWith
'. AfId |Författarens anknytnings-ID                              |Int64      |Lika med
'. NA    |Redigerings ordning för papperet                         |Int32      |Lika med
F.FN    |Namn på studie                                |Sträng     |Är lika med<br/>StartsWith
F. FId   |Fält för studie-ID                                  |Int64      |Lika med
J. JN    |Journal namn                                       |Sträng     |Är lika med<br/>StartsWith
J. JId   |Journal-ID                                         |Int64      |Lika med
C.CN    |Konferens seriens namn                             |Sträng     |Är lika med<br/>StartsWith
C. CId   |Konferens seriens ID                               |Int64      |Lika med
Försvinner     |Refererat dokument-ID                              |Int64 []    |Lika med
o       |Ord från papperets rubrik och sammanfattning                |String[]   |Lika med
E       |Utökade metadata (se tabellen nedan)                |Sträng     |inga  
        


## <a name="extended-metadata-attributes"></a>Utökade metadata-attribut ##

Namn    | Beskrivning               
--------|---------------------------    
UNIKT NAMN      | Dokumentets visnings namn 
s       | Källor – lista över papperets webb adresser, sorterade efter statisk rangordning
S.Ty    | Källtyp (1: HTML, 2: text, 3: PDF, 4: DOC, 5: PPT, 6: XLS, 7: PS)
S. U     | URL för källa
VFN     | Plats fullständigt namn – fullständigt namn på journalen eller konferensen
VSN     | Plats kort namn – kort namn på journalen eller konferensen
V       | Volym – Journal volym
BV      | Journal namn
BT      | 
PB      | Journal förkortningar
I       | Problem – Journal problem
FP      | FirstPage – första pappersarket
LP      | LastPage – sista sidan av papper
DOI     | Digital objekt identifierare
Kopia      | Citat kontexter – lista över refererade pappers-ID: n och motsvarande kontext i papperet (t. ex. [{123: ["Brune Foxes är kända för att kunna hoppa som det hänvisas till i pappers 123", "de Lazy-hundar är en historisk misnomer som visas i pappers 123"]})
IA      | Inverterad Sammanfattning
IA.IndexLength| Antal objekt i indexet (abstrakta ord)
IA.InvertedIndex| Lista över abstrakta ord och deras motsvarande position i den ursprungliga abstrakten (t. ex. [{": [0, 15, 30]}, {" brun ": [1]}, {" Fox ": [2]}])
