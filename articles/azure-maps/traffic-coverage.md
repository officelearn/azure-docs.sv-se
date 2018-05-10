---
title: Trafik täckning i Azure Maps | Microsoft Docs
description: Lär dig mer om trafik täckning i Azure Maps
services: azure-maps
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 11/28/2017
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: ''
ms.openlocfilehash: 08a4f3fc135aae2772bd60c67cbd282603cd4f8d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
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

Mer information om Azure Maps trafikinformation finns i [trafik](https://docs.microsoft.com/rest/api/azure-maps/traffic) referera sidor.