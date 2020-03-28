---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: 07e7cc991f127bf4bb4f466c0108962786e45bce
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68423570"
---
Observera **skjutreglaget Sannolikhetströskel** i den vänstra rutan på fliken **Prestanda.** Detta är den förtroendenivå som en förutsägelse måste ha för att anses vara korrekt (för att beräkna precision och återkallande). 

När du tolkar förutsägelseanrop med en tröskel för hög sannolikhet tenderar&mdash;de att returnera resultat med hög precision på bekostnad av återkallandet de upptäckta klassificeringarna är korrekta, men många förblir oupptäckta. En låg sannolikhet tröskel&mdash;gör motsatsen de flesta av de faktiska klassificeringarna upptäcks, men det finns fler falska positiva inom den uppsättningen. Med detta i åtanke bör du ställa in sannolikhetströskeln enligt projektets specifika behov. Senare, när du får förutsägelseresultat på klientsidan, bör du använda samma tröskelvärde för sannolikhet som du använde här.