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
ms.openlocfilehash: 945d701a2a7dffc259c601b4dab9fa1333ccc066
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74896608"
---
Innan du startar konfigurationen kontrollerar du att du uppfyller följande kriterier:

* Om du redan har ett virtuellt nätverk som du vill ansluta till kontrollerar du att inget av undernäten i det lokala nätverket överlappar det. Det virtuella nätverket kräver inget gateway-undernät och kan inte ha några virtuella nätverksgateways. Om du inte har ett virtuellt nätverk kan du skapa ett med hjälp av stegen i den här artikeln.
* Hämta ett IP-adressintervall för din hubbregion. Navet är ett virtuellt nätverk och adressintervallet som du anger för hubbregionen kan inte överlappa med ett befintligt virtuellt nätverk som du ansluter till. Det kan inte heller överlappa med de adressintervall som du ansluter till lokalt. Om du inte känner till IP-adressintervallen i din lokala nätverkskonfiguration samordnar du med någon som kan ange dessa uppgifter åt dig.
* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.