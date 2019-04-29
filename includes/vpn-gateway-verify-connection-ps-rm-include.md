---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 10/17/2018
ms.date: 03/04/2019
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: 49f48c2d0bf865cf8c8fde831e7a597f8701d213
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60456376"
---
Du kan kontrollera att anslutningen har utförts med hjälp av cmdleten ”Get-AzVirtualNetworkGatewayConnection' med eller utan '-Debug”. 

1. Använd följande cmdlet-exempel genom att konfigurera värdena för att matcha dina egna. Välj A om du uppmanas, för att köra Alla. I exemplet avser '-Name' namnet på den anslutning du vill testa.

   ```azurepowershell
   Get-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1
   ```
2. Visa värdena när cmdlet:en har slutförts. I exemplet nedan visas anslutningsstatusen som Ansluten och du kan se ingående och utgående byte.
   
   ```
   "connectionStatus": "Connected",
   "ingressBytesTransferred": 33509044,
   "egressBytesTransferred": 4142431
   ```