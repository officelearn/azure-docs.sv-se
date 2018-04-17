---
title: ta med fil
description: ta med fil
services: virtual-network
author: genli
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 10f723d5298e745520c4db41b994bd2b97aaa365
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
Även om användningen av systemvägar förenklar trafik automatiskt för din distribution så finns det fall där du kan vilja kontrollera routingen för paket genom en virtuell installation. Du kan göra det genom att skapa användardefinierade vägar som anger next hop för paket som flödar till ett specifikt undernät så att de istället går till din virtuella installation och som aktiverar IP-vidarebefordring för VM:en som kör den virtuella installationen.

Några av de fall där virtuella installationer kan användas är:

* Övervaka trafik med ett intrång identifiering system (ID)
* Kontrollera trafik med en brandvägg

Mer information om UDR och IP-vidarebefordran finns [användardefinierade vägar och IP-vidarebefordring](../articles/virtual-network/virtual-networks-udr-overview.md).

