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
ms.openlocfilehash: d36d2be59010c47348a8e196b28d87e5b967868e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60845698"
---
### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>Steg 1: Navigera till den virtuella nätverksgatewayen

1. I den [Azure-portalen](https://portal.azure.com), gå till **alla resurser**. 
2. Öppna sidan gateway för virtuellt nätverk genom att navigera till den virtuella nätverksgatewayen som du vill ta bort och klicka på den.

### <a name="step-2-delete-connections"></a>Steg 2: Ta bort anslutningar

1. På sidan för din virtuella nätverksgateway **anslutningar** att visa alla anslutningar till gatewayen.
2. Klicka på den **”...”** på raden i namnet på anslutningen och sedan väljer **ta bort** i listrutan.
3. Klicka på **Ja** att bekräfta att du vill ta bort anslutningen. Om du har flera anslutningar kan du ta bort varje anslutning.

### <a name="step-3-delete-the-virtual-network-gateway"></a>Steg 3: Ta bort den virtuella nätverksgatewayen

Tänk på att om du har en P2S-konfiguration till den här virtuella nätverket utöver din S2S-konfiguration, tar bort den virtuella nätverksgatewayen kommer automatiskt bort alla P2S-klienter utan varning.

1. På sidan virtuellt nätverk gateway **översikt**.
2. På den **översikt** klickar du på **ta bort** ta bort gatewayen.
