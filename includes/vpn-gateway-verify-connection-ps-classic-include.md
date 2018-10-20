---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/17/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 03dc6d8bb95a952a77be31f79df36a2c1ddc8ffc
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49458655"
---
Du kan kontrollera att anslutningen har utförts med hjälp av cmdleten ”Get-AzureVNetConnection”.

1. Använd följande cmdlet-exempel genom att konfigurera värdena för att matcha dina egna. Namnet på det virtuella nätverket måste vara inom citattecken om det innehåller blanksteg.

  ```azurepowershell
  Get-AzureVNetConnection "Group ClassicRG ClassicVNet"
  ```
2. Visa värdena när cmdlet:en har slutförts. I exemplet nedan anslutning status visas som ”ansluten” och du kan se ingående och utgående byte.

        ConnectivityState         : Connected
        EgressBytesTransferred    : 181664
        IngressBytesTransferred   : 182080
        LastConnectionEstablished : 1/7/2016 12:40:54 AM
        LastEventID               : 24401
        LastEventMessage          : The connectivity state for the local network site 'RMVNetLocal' changed from Connecting to
                                    Connected.
        LastEventTimeStamp        : 1/7/2016 12:40:54 AM
        LocalNetworkSiteName      : RMVNetLocal