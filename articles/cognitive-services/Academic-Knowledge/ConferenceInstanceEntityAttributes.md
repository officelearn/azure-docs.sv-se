---
title: Attribut för entiteten konferens instans – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Lär dig de attribut som du kan använda med entiteten konferens instans i Academic Knowledge API.
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: c6761206a58724dae96d9dc6f6234658892d4d18
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146558"
---
# <a name="conference-instance-entity"></a>Konferens instans entitet

> [!NOTE]
> Följande attribut är speciella för en konferens instans-entitet. (Ty = ' 4 ')

Namn | Beskrivning | Typ | Åtgärder
--- | --- | --- | ---
CC      |Totalt antal käll hänvisningar i en konferens instans           |Int32      |Ingen  
Installations. Styr    |Datum för händelse av konferens instans    |Date       |Lika med, IsBetween
Installations. Tera    |Rubrik för en konferens instans händelse   |Date       |Lika med, IsBetween
CIARD   |Förfallo datum för abstrakt registrering för konferens instansen  |Date       |Lika med, IsBetween
CIED    |Sista datum för konferens instansen    |Date       |Lika med, IsBetween
CIFVD   |Slut versionens förfallo datum för konferens instansen  |Date       |Lika med, IsBetween
CIL     |Plats för konferens instansen    |Sträng     |Lika med, StartsWith
CIN     |Konferens instans normaliserat namn |Sträng        |Lika med
CINDD   |Meddelande datum för konferens instansen   |Date       |Lika med, IsBetween
CISD    |Start datum för konferens instansen  |Date       |Lika med, IsBetween
CISDD   |Förfallo datum för konferens instans     |Date       |Lika med, IsBetween
DCN     |Visnings namn för konferens instans  |Sträng      |Ingen
E | Utökade metadata</br></br>**Viktigt**: det här attributet är inaktuellt och stöds endast för äldre program. Att begära det här attributet individuellt (t. ex. attribut = ID, ti, E) resulterar i att alla utökade metadata returneras i en *serialiserad JSON-sträng*</br></br>Alla attribut som finns i utökade metadata är nu tillgängliga som ett Top-attribut och kan begäras som sådana (t. ex. attribut = ID, ti, DOI, IA) | [Extratextrad](#extended) | Ingen
ECC     |Konferens instans totalt antal beräknade antal hänvisningar |Int32      |Ingen
FN | Fullständigt namn på konferens instans | Sträng | Ingen
Id      |Enhets-ID                              |Int64      |Lika med
PC | Totalt antal publiceringar av konferens instanser | Int32 | Ingen
Datorer. NOMENKLATUR  |Överordnad konferens seriens namn på instansen |Sträng  |Lika med
Datorer. CId |Instansens överordnade konferens serie-ID |Int64     |Lika med

## <a name="extended"></a>Extratextrad

> [!IMPORTANT]
> Det här attributet är inaktuellt och stöds endast för äldre program. Att begära det här attributet individuellt (t. ex. attribut = ID, ti, E) resulterar i att alla utökade metadata returneras i en *serialiserad JSON-sträng*</br></br>Alla attribut som finns i utökade metadata är nu tillgängliga som ett Top-attribut och kan begäras som sådana (t. ex. attribut = ID, ti, DOI, IA)

> [!IMPORTANT]
> Stöd för att begära enskilda utökade attribut med hjälp av "E." omfånget, t. ex. "E. DN" är inaktuellt. Även om det fortfarande är tekniskt tekniskt stöds, begär enskilda utökade attribut med hjälp av "E". omfattning resulterar i att attributvärdet returneras på två platser i JSON-svaret, som en del av objektet "E" och som ett attribut på den översta nivån.

Namn | Beskrivning | Typ | Åtgärder
--- | --- | --- | ---
FN | Fullständigt namn på konferens instans | Sträng | Ingen
PC | Totalt antal publiceringar av konferens instanser | Int32 | Ingen
