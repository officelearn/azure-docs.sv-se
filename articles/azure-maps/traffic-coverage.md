---
title: Trafik täckning i Azure Maps | Microsoft Docs
description: Lär dig mer om trafik täckning i Azure Maps
author: kgremban
ms.author: kgremban
ms.date: 11/28/2017
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: a6fc1a8f050c3b1f546b4d0150f14b2cbebc31d0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34599716"
---
# <a name="azure-maps-traffic-coverage"></a>Azure Maps trafik täckning

Azure Maps innehåller omfattande trafikinformation i form av trafik **flöde** och **incidenter**. Dessa data kan visualiseras på kartor eller används för att generera smartare vägar som factor i real körning villkor. 

Maps har dock inte samma grad av information och noggrannhet för alla regioner. Följande tabell innehåller information om vilken typ av trafikinformation som du kan begära från varje region: 

|Region  |Incidenter  |Flöde  |
|---------|:---------:|:---------:|
|Argentina      |         |✓         |
|Australien     |✓         |✓        |
|Österrike     |✓         |✓         |
|Bahrain     |         |✓         |
|Belgien     |✓         |✓         |
|Brasilien     |✓         |✓         |
|Bulgarien     |✓         |✓         |
|Kanada     |✓         |✓         |
|Chile     |✓         |✓         |
|Colombia      |         |✓         |
|Kroatien     |         |✓         |
|Tjeckien     |✓         |✓         |
|Danmark     |✓         |✓         |
|Egypten     |         |✓         |
|Estland     |         | ✓        |
|Finland     |✓         |✓         |
|+ Åland i Oceanien och Västindien      |✓         |✓         |
|Frankrike     |✓         |✓         |
|+ Monaco     |✓         |✓         |
|Tyskland     |✓         |✓         |
|Grekland     |✓         |✓         |
|Hongkong SAR     |✓         |✓         |
|Ungern     |✓         |✓         |
|Island     |         |✓         |
|Indonesien     |✓         |✓         |
|Irland (Republiken)     |✓         |✓         |
|Israel     |         |✓         |
|Italien     |✓         |✓        |
|+ San Marino     |✓         |✓         |
|Kuwait     |✓         |✓         |
|Lettland     |         |✓         |
|Lesotho     |✓         |✓         |
|Litauen     |         |✓         |
|Luxemburg     |✓         |✓         |
|Macao SAR     |         |✓         |
|Malaysia     |✓         |✓         |
|Malta     |✓         |✓         |
|Mexiko     |✓         |✓         |
|Marocko     |         |✓         |
|Nederländerna     |✓         |✓         |
|Nya Zeeland     |✓         |✓         |
|Norge     |✓         |✓         |
|Oman     |         |✓         |
|Polen     |✓         |✓         |
|Portugal     |✓         |✓         |
|+ Azorerna och Madeira     |         |✓         |
|Qatar     |         |✓         |
|Rumänien     |         |✓         |
|Ryska federationen     |✓         |✓         |
|Saudiarabien     |✓         |✓         |
|Singapore     |✓         |✓         |
|Slovakien     |✓         |✓         |
|Slovenien     |✓         |✓         |
|Sydafrika     |✓         |✓         |
|Spanien     |✓         |✓         |
|+ Andorra     |✓         |✓         |
|+ Balearerna     |✓         |✓         |
|+ Kanarieöarna     |✓         |✓         |
|+ Gibraltar     |✓         |✓         |
|Sverige     |✓         |✓         |
|Schweiz     |✓         |✓        |
|+ Liechtenstein      |✓         |✓         |
|Taiwan SAR     |✓         |✓        |
|Thailand     |✓         |✓        |
|Turkiet     |✓         |✓         |
|Ukraina     |✓         |✓         |
|Förenade Arabemiraten     |✓         |✓         |
|Storbritannien     |✓         |✓         |
|(Guernsey & Jersey)     |✓         |✓         |
|Isle of Man     |✓         |✓         |
|USA     |✓         |✓        |
|+ Registreringen     |✓         |✓         |

Mer information om Azure Maps trafikinformation finns i [trafik](https://docs.microsoft.com/rest/api/maps/traffic) referera sidor.