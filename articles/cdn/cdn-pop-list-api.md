---
title: Hämta den aktuella Verizon POP-listan för Azure CDN | Microsoft Docs
description: Lär dig hur du hämtar den aktuella Verizon POP-listan med hjälp av REST API.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: v-deasim
ms.custom: ''
ms.openlocfilehash: f796d18a03e14fdf652af72366762e1365523f09
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36754674"
---
# <a name="retrieve-the-current-verizon-pop-list-for-azure-cdn"></a>Hämta den aktuella Verizon POP-listan för Azure CDN

Du kan använda REST API för att hämta en uppsättning IP-adresser för Verizons förekomst (POP) servrar. Servrarna POP begär ursprung servrar som är associerade med Azure Content Delivery Network (CDN) slutpunkter för en profil för Verizon (**Azure CDN Standard från Verizon** eller **Azure CDN Premium från Verizon**). Observera att denna uppsättning IP-adresser skiljer sig från IP-adresser som en klient skulle se vid begäranden till POP. 

Syntaxen för REST API-åtgärden för att hämta listan över POP finns [kant - noder i listan](https://docs.microsoft.com/rest/api/cdn/edgenodes/list).

## <a name="typical-use-case"></a>Vanligt användningsfall

Av säkerhetsskäl bör använda du IP-listan för att genomdriva att begäranden till ursprungsservern görs endast från en giltig Verizon POP. Om någon identifieras värdnamnet eller IP-adress för en CDN-slutpunkt ursprungsservern, gör en exempelvis begäranden direkt till ursprungsservern därför kringgå skalningen och säkerhetsfunktioner som tillhandahålls av Azure CDN. Det här scenariot kan förhindras genom att ställa in IP-adresser i listan returnerade som det enda tillåtna IP-adresser på ett ursprungsservern. Att kontrollera att de har den senaste POP-listan, hämta minst en gång om dagen. 

## <a name="next-steps"></a>Nästa steg

Information om REST-API finns [Azure CDN REST API](https://docs.microsoft.com/en-us/rest/api/cdn/).