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
ms.openlocfilehash: da682e3e947c3f8643610e42ad22fb9d742796f5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
Du kan kontrollera att anslutningen har upprättats med cmdleten Get-AzureRmVirtualNetworkGatewayConnection, med eller utan flaggan -Debug. 

1. Använd följande cmdlet-exempel genom att konfigurera värdena för att matcha dina egna. Välj A om du uppmanas, för att köra Alla. I exemplet avser '-Name' namnet på den anslutning du vill testa.

  ```powershell
  Get-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnection -ResourceGroupName MyRG
  ```
2. Visa värdena när cmdlet:en har slutförts. I exemplet nedan visas anslutningsstatusen som Ansluten och du kan se ingående och utgående byte.
   
  ```
  "connectionStatus": "Connected",
  "ingressBytesTransferred": 33509044,
  "egressBytesTransferred": 4142431
  ```