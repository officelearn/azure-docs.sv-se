---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 11783f8fac147e6fb2cf371ee1a588cf318a5e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75379456"
---
Formulärreformare fungerar på indatadokument som uppfyller dessa krav:

* Formatet måste vara JPG, PNG, PDF (text eller skannad) eller TIFF. Text inbäddat PDF-filer är bäst eftersom det inte finns någon risk för fel i teckenextrahering och plats.
* Om pdf-filerna är lösenordslåst måste du ta bort låset innan du skickar dem.
* PDF- och TIFF-dokument måste vara högst 200 sidor, och den totala storleken på utbildningsdatauppsättningen måste vara högst 500 sidor.
* För bilder måste dimensionerna vara mellan 600 x 100 pixlar och 4200 x 4200 pixlar.
* Om de skannas från pappersdokument bör formulären vara högkvalitativa genomsökningar.
* Texten måste använda det latinska alfabetet (engelska tecken).
* För oövervakad inlärning (utan etiketterade data) måste data innehålla nycklar och värden.
* För oövervakad inlärning (utan etiketterade data) måste nycklarna visas ovanför eller till vänster om värdena. de kan inte visas under eller till höger.

Formuläre recognizer stöder för närvarande inte dessa typer av indata:

* Komplexa tabeller (kapslade tabeller, sammanfogade rubriker eller celler och så vidare).
* Kryssrutor eller alternativknappar.
