---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/16/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b8a61586acd888301350277d924f3d4fe176b4c8
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148202"
---
### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>Steg 1: navigera till den virtuella Nätverksgatewayen

1. I [Azure Portal](https://portal.azure.com)navigerar du till **alla resurser**. 
2. Öppna sidan virtuell nätverksgateway genom att gå till den virtuella Nätverksgatewayen som du vill ta bort och klicka på den.

### <a name="step-2-delete-connections"></a>Steg 2: ta bort anslutningar

1. På sidan för den virtuella Nätverksgatewayen klickar du på **anslutningar** för att visa alla anslutningar till gatewayen.
2. Klicka på **...** på raden i namnet på anslutningen och välj sedan **ta bort** i list rutan.
3. Klicka på **Ja** för att bekräfta att du vill ta bort anslutningen. Om du har flera anslutningar tar du bort varje anslutning.

### <a name="step-3-delete-the-virtual-network-gateway"></a>Steg 3: ta bort den virtuella Nätverksgatewayen

Tänk på att om du har en P2S-konfiguration till det här virtuella nätverket förutom din S2S-konfiguration tar borttagning av den virtuella Nätverksgatewayen automatiskt bort alla P2S-klienter utan varning.

1. På sidan virtuell nätverksgateway klickar du på **Översikt**.
2. På sidan **Översikt** klickar du på **ta bort** för att ta bort gatewayen.
