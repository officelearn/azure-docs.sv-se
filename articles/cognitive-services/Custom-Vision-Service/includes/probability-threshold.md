---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: 07e7cc991f127bf4bb4f466c0108962786e45bce
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "68423570"
---
Observera skjutreglaget för **sannolikhets tröskel** i den vänstra rutan på fliken **prestanda** . Detta är den nivå av säkerhet som en förutsägelse måste ha för att anses korrekt (i syfte att beräkna precision och återkalla). 

När du tolkar förutsägelse samtal med en hög sannolikhets tröskel, tenderar det att returnera resultat med hög precision vid utgiften om&mdash;att de identifierade klassificeringarna är korrekta, men många förblir oidentifierade. Ett tröskelvärde för låg sannolikhet gör att&mdash;de motstående största av de faktiska klassificeringarna upptäcks, men det finns fler falska positiva identifieringar i den uppsättningen. Med detta i åtanke bör du ställa in sannolikhets tröskeln enligt projektets speciella behov. Senare, när du får förutsägelse resultat på klient sidan, bör du använda samma sannolikhets tröskel som du använde här.