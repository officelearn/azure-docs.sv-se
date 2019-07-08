---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 816fa4b0df2fab77bcde261f30d8bad584257fd7
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592622"
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