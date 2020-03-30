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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187214"
---
### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>Steg 1: Navigera till den virtuella nätverksgatewayen

1. Navigera till **alla resurser**i [Azure-portalen](https://portal.azure.com). 
2. Om du vill öppna sidan för virtuell nätverksgateway navigerar du till den virtuella nätverksgateway som du vill ta bort och klickar på den.

### <a name="step-2-delete-connections"></a>Steg 2: Ta bort anslutningar

1. Klicka på **Anslutningar** på sidan för den virtuella nätverksgatewayen om du vill visa alla anslutningar till gatewayen.
2. Klicka på **'...'** på raden med namnet på anslutningen och välj sedan **Ta bort** från listrutan.
3. Klicka på **Ja** för att bekräfta att du vill ta bort anslutningen. Om du har flera anslutningar tar du bort varje anslutning.

### <a name="step-3-delete-the-virtual-network-gateway"></a>Steg 3: Ta bort den virtuella nätverksgatewayen

Tänk på att om du har en P2S-konfiguration till det här virtuella nätverket utöver din S2S-konfiguration, kommer om du tar bort den virtuella nätverksgatewayen automatiskt att koppla från alla P2S-klienter utan förvarning.

1. Klicka på **Översikt**på sidan Virtual Network Gateway .
2. Klicka på Ta **bort** på sidan **Översikt** om du vill ta bort gatewayen.
