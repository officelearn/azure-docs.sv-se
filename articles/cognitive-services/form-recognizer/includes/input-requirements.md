---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 4079b0cab0126a9750bbb91ca7046aa18dbe9ece
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657879"
---
Formulär tolken arbetar med inmatade dokument som uppfyller följande krav:

* Formatet måste vara JPG, PNG eller PDF (text eller genomsökt). Text-inbäddade PDF-filer är bäst eftersom det inte går att extrahera fel i tecken extrahering och plats.
* Om dina PDF-filer är lösenordsskyddade måste du ta bort låset innan du skickar dem.
* Fil storleken måste vara mindre än 4 MB.
* För bilder måste dimensionerna vara mellan 600 x 100 pixlar och 4200 x 4200 bild punkter.
* Om du skannar från pappers dokument bör formulär vara av hög kvalitet.
* Texten måste använda det latinska alfabetet (engelska tecken).
* Data måste innehålla nycklar och värden.
* Nycklar kan visas ovanför eller till vänster om värdena, men inte under eller till höger.

Formulär tolken stöder för närvarande inte följande typer av indata:

* Komplexa tabeller (kapslade tabeller, sammanfogade rubriker eller celler osv.).
* Kryss rutor eller alternativ knappar.
* PDF-dokument som är längre än 50 sidor.
