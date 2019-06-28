---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 690219347782aad2140b0a1b0541590c5426c568
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67459733"
---
Formuläret Igenkännande fungerar på inkommande dokument som uppfyller dessa krav:

* Formatet måste vara JPG, PNG- eller PDF (text eller skannade). Text som är inbäddad PDF-filer är bästa eftersom det finns ingen risk för fel i teckenextrahering och plats.
* Filstorleken måste vara mindre än 4 MB (megabyte).
* Storleken måste vara mellan 50 x 50 bildpunkter och 4200 x 4200 bildpunkter för avbildningar.
* Om genomsökts från pappersdokument vara formulär genomsökningar av hög kvalitet.
* Text måste använda det latinska alfabetet (engelska tecken).
* Data måste skrivas (handskriven inte).
* Data måste innehålla nycklar och värden.
* Nycklar kan visas ovanför eller till vänster av värden, men inte mindre än eller till höger.

Formuläret Igenkännande stöder inte för närvarande dessa typer av indata:

* Komplexa tabeller (kapslade tabeller, sammanfogade rubriker eller celler och så vidare).
* Kryssrutorna eller radio knappar.
* PDF-dokument är längre än 50 sidor.