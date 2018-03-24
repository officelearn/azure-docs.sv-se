---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5b2aa7fedbc203c50796a0e0c8d9cdb3895ae6c1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>Steg 1: Gå till den virtuella nätverksgatewayen

1. I den [Azure-portalen](https://portal.azure.com), gå till **alla resurser**. 
2. Navigera till den virtuella nätverksgatewayen som du vill ta bort och klicka på den för att öppna sidan gateway för virtuellt nätverk.

### <a name="step-2-delete-connections"></a>Steg 2: Ta bort anslutningar

1. På sidan för din virtuella nätverksgateway **anslutningar** att visa alla anslutningar till gatewayen.
2. Klicka på den **”...”** på raden på namnet på anslutningen väljer **ta bort** i listrutan.
3. Klicka på **Ja** bekräfta att du vill ta bort anslutningen. Om du har flera anslutningar kan du ta bort varje anslutning.

### <a name="step-3-delete-the-virtual-network-gateway"></a>Steg 3: Ta bort den virtuella nätverksgatewayen

Tänk på att om du har en P2S-konfiguration till detta virtuella nätverk utöver konfigurationen S2S tar bort virtuell nätverksgateway automatiskt frånkopplas alla P2S-klienter utan varning.

1. På sidan virtuella nätverkets gateway **översikt**.
2. På den **översikt** klickar du på **ta bort** att ta bort denna gateway.
