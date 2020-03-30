---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/15/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4c232e1ce183c6935d625b5bc9987a4981865ae4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187223"
---
Om du arbetar med resurshanterarens distributionsmodell kan du ändra till de nya gateway-SKU:erna. När du byter från en äldre gateway SKU till en ny SKU tar du bort den befintliga VPN-gatewayen och skapar en ny VPN-gateway.

Arbetsflöde:

1. Ta bort flera anslutningar till en virtuell nätverksgateway.
2. Ta bort VPN gatewayen.
3. Skapa den nya VPN gatewayen.
4. Uppdatera din lokala VPN-enhet med den nya IP-adressen för VPN-gatewayen (för plats-till-plats-anslutningar).
5. Uppdatera gateway IP-adressvärdet för alla lokala nätverket VNet-till-VNet-gateways som ska ansluta till den här gatewayen.
6. Hämta den nya klientpaket för VPN-konfiguration för P2S-klienter som ansluter till det virtuella nätverket via den här VPN-gatewayen.
7. Skapa om flera anslutningar till en virtuell nätverksgateway.

Överväganden:

* Om du vill flytta till de nya SKU:erna måste din VPN-gateway finnas i resurshanterarens distributionsmodell.
* Om du har en klassisk VPN-gateway måste du fortsätta att använda de äldre äldre SKU:erna för den gatewayen, men du kan ändra storlek på mellan de äldre SKU:erna. Du kan inte ändra till de nya SKU:erna.
* Du kommer att ha anslutningsstopp när du byter från en äldre SKU till en ny SKU.
* När du byter till en ny gateway SKU ändras den offentliga IP-adressen för din VPN-gateway. Detta händer även om du anger samma offentliga IP-adressobjekt som du använde tidigare.