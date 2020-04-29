---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 11783f8fac147e6fb2cf371ee1a588cf318a5e9c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "75379456"
---
Formulär tolken arbetar med inmatade dokument som uppfyller följande krav:

* Formatet måste vara JPG, PNG, PDF (text eller scannat) eller TIFF. Text-inbäddade PDF-filer är bäst eftersom det inte går att extrahera fel i tecken extrahering och plats.
* Om dina PDF-filer är lösenordsskyddade måste du ta bort låset innan du skickar dem.
* PDF-och TIFF-dokument måste vara 200 sidor eller mindre och den totala storleken på tränings data uppsättningen måste vara 500 sidor eller mindre.
* För bilder måste dimensionerna vara mellan 600 x 100 pixlar och 4200 x 4200 bild punkter.
* Om du skannar från pappers dokument bör formulär vara av hög kvalitet.
* Texten måste använda det latinska alfabetet (engelska tecken).
* För oövervakad inlärning (utan märkta data) måste data innehålla nycklar och värden.
* För oövervakad inlärning (utan märkta data) måste nycklarna visas ovanför eller till vänster om värdena. de kan inte visas under eller till höger.

Formulär tolken stöder för närvarande inte följande typer av indata:

* Komplexa tabeller (kapslade tabeller, sammanfogade rubriker eller celler osv.).
* Kryss rutor eller alternativ knappar.
