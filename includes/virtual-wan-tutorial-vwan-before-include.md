---
title: ta med fil
description: ta med fil
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 66e21aefa5648262ca2fcc61501905f725ac0e4b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60457903"
---
Kontrollera att du har uppfyllt följande villkor innan du påbörjar konfigurationen:

* Om du redan har ett virtuellt nätverk som du vill ansluta till ska du kontrollera att inget av undernäten i det lokala nätverket överlappar de virtuella nätverk som du vill ansluta till. Det virtuella nätverket kräver inget gatewayundernät och kan inte ha några virtuella nätverksgatewayer. Om du inte har något virtuellt nätverk kan du skapa ett med anvisningarna i den här artikeln.
* Hämta ett IP-adressintervall för din hubbregion. Hubben är ett virtuellt nätverk och det adressintervall du anger för hubbregionen får inte överlappa något av de befintliga virtuella nätverk som du ansluter till. Det får inte heller överlappa det adressintervall som du ansluter till lokalt. Om du inte vet vilka IP-adressintervaller som används i din lokala nätverkskonfiguration kontaktar du relevant person som kan ge dig den här informationen.
* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.