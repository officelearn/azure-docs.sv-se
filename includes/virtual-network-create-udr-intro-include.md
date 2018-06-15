---
title: ta med fil
description: ta med fil
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 226dfd9add69e8d89a030b858c819691d7b20627
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
ms.locfileid: "31805171"
---
Även om användningen av systemvägar förenklar trafik automatiskt för din distribution så finns det fall där du kan vilja kontrollera routingen för paket genom en virtuell installation. Du kan göra det genom att skapa användardefinierade vägar som anger next hop för paket som flödar till ett specifikt undernät så att de istället går till din virtuella installation och som aktiverar IP-vidarebefordring för VM:en som kör den virtuella installationen.

Några av de fall där virtuella installationer kan användas är:

* Övervaka trafik med ett intrång identifiering system (ID)
* Kontrollera trafik med en brandvägg

Mer information om UDR och IP-vidarebefordran finns [användardefinierade vägar och IP-vidarebefordring](../articles/virtual-network/virtual-networks-udr-overview.md).

