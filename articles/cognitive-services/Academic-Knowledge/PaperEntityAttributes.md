---
title: Paper entity attributes - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Läs om de attribut som du kan använda med dokumentet entiteten i Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: d36a39f10f9e0129af3b730a65387d7108209004
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55168387"
---
# <a name="paper-entity"></a>Dokumentet entitet

<sub> * Nedan attribut är specifika för dokumentet entitet. (Ty = ”0”) </sub>


Namn    |Beskrivning                                        |Typ       | Åtgärder
------- | ------------------------------------------------- | --------- | ----------------------------
Id      |Enhets-id                                          |Int64      |Lika med
Ti      |Dokumentet rubrik                                        |Sträng     |Är lika med,<br/>StartsWith
L       |Språkkod för dokumentet avgränsade med ”\@@@”            |Sträng     |Lika med
Y       |Dokumentet år                                         |Int32      |Är lika med,<br/>IsBetween
D       |Dokumentet datum                                         |Date       |Är lika med,<br/>IsBetween
Kopia      |Antalet källhänvisningar                                     |Int32      |inga  
ECC     |Beräknad citat antal                           |Int32      |inga
AA.AuN  |Upphovsmannens namn                                        |Sträng     |Är lika med,<br/>StartsWith
AA.AuId |Författare-ID                                          |Int64      |Lika med
AA.AfN  |Författarens namn i anknytning                            |Sträng     |Är lika med,<br/>StartsWith
AA.AfId |Författare anknytning-ID                              |Int64      |Lika med
AA.S    |Författare för dokumentet                         |Int32      |Lika med
F.FN    |Fältet för studier namn                                |Sträng     |Är lika med,<br/>StartsWith
F.FId   |Fältet för studie-ID                                  |Int64      |Lika med
J.JN    |Namn                                       |Sträng     |Är lika med,<br/>StartsWith
J.JId   |Ändringsjournalens ID                                         |Int64      |Lika med
C.CN    |Konferensen serienamn                             |Sträng     |Är lika med,<br/>StartsWith
C.CId   |ID för konferens-serien                               |Int64      |Lika med
RId     |Refererade dokument-ID                              |Int64[]    |Lika med
W       |Ord från papper rubrik och sammanfattning                |String[]   |Lika med
E       |Utökade metadata (se tabellen nedan)                |Sträng     |inga  
        


## <a name="extended-metadata-attributes"></a>Utökade attribut Metadata ##

Namn    | Beskrivning               
--------|---------------------------    
DN      | Visningsnamnet för dokumentet 
S       | Källor - lista över webbadresser av dokumentet, sorterade efter statiska rangordning
S.Ty    | Typ av datakälla (1:HTML, 2:Text, 3:PDF, 4:DOC, 5:PPT, 6:XLS, 7:PS)
S.U     | URL för källa
VFN     | Fullständigt namn för plats - fullständigt namn i journalen eller konferens
VSN     | Kort namn för plats - korta namnet för ändringsjournalen eller konferens
V       | Volym - journalen volym
BV      | Namn
BT      | 
PB      | Journalen Abreviations
I       | Problem - journalen problemet
FP      | Förstasida - första sidan i dokumentet
LP      | SistaSida - sista sidan i dokumentet
DOI     | Digitala objekt-ID:
Kopia      | Citat kontexter – lista över refererade dokument-ID: n och motsvarande kontexten i dokumentet (t.ex. [{123: [”bruna rävar är kända för att hoppa som anges i dokumentet 123”, ”lazy hundar är en historisk misnomer enligt dokumentet 123”]})
IA      | Vägar i inverterad abstrakt
IA.IndexLength| Antal objekt i indexet (abstrakts ordräkning)
IA.InvertedIndex| Lista över abstrakt ord och deras motsvarande position i det ursprungliga abstract (t.ex. [{”den”: [0, 15, 30]}, {”bruna”: [1]}, {”fox":[2]}])
