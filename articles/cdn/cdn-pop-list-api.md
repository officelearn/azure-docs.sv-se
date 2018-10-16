---
title: Hämta den aktuella Verizon POP-listan för Azure CDN | Microsoft Docs
description: Lär dig hur du hämtar den aktuella Verizon POP-listan med hjälp av REST-API.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 34bd958260056d7a0297c4d3545d97543a363480
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344800"
---
# <a name="retrieve-the-current-verizon-pop-list-for-azure-cdn"></a>Hämta den aktuella Verizon POP-listan för Azure CDN

Du kan använda REST API för att hämta uppsättning IP-adresser för Verizons närvaro (POP) servrar. Dessa POP-servrar gör förfrågningar till ursprungsservrar som är associerade med Azure Content Delivery Network (CDN) slutpunkter på en Verizon-profil (**Azure CDN Standard från Verizon** eller **Azure CDN Premium från Verizon**). Observera att den här uppsättningen med IP-adresser skiljer sig från IP-adresser som en klient skulle se vid begäranden till POP: erna. 

Syntaxen för REST API-åtgärd för hämtning av POP-listan, se [Kantnoder - listan](https://docs.microsoft.com/rest/api/cdn/edgenodes/edgenodes_list).

## <a name="typical-use-case"></a>Vanligt användningsfall

Du kan använda den här IP-listan för att genomdriva att begäranden till ursprungsservern görs endast från en giltig Verizon POP av säkerhetsskäl. Om någon identifieras värdnamnet eller IP-adress för en CDN-slutpunkt ursprungsservern, göra någon till exempel begäranden direkt till ursprungsservern därför kringgår den skalning och säkerhetsfunktioner som tillhandahålls av Azure CDN. Det här scenariot kan förhindras genom att ange de IP-adresserna i den returnerade listan som den enda tillåtna IP-adresser på en ursprungsserver. Att kontrollera att de har du den senaste POP-listan, hämta minst en gång om dagen. 

## <a name="next-steps"></a>Nästa steg

Information om REST API finns i [Azure CDN REST API](https://docs.microsoft.com/rest/api/cdn/).
