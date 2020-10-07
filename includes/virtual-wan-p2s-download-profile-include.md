---
title: inkludera fil
description: inkludera fil
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e1a9256cc9a015a5d8286de5e5bd7b61ed915a3b
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/07/2020
ms.locfileid: "91812797"
---
1. På sidan för ditt virtuella WAN väljer du **användare VPN-konfigurationer**.
1. Överst på sidan väljer du **Hämta användare VPN-konfiguration**. När du hämtar WAN-nivå konfigurationen får du en inbyggd Traffic Manager-baserad VPN-profil för användare. Mer information om globala profiler eller en nav baserad profil finns i [hubb profiler](https://docs.microsoft.com/azure/virtual-wan/global-hub-profile). Failover-scenarier är förenklade med global profil.

   Om en hubb av någon anledning inte är tillgänglig, garanterar den inbyggda trafik hanteringen som tjänsten tillhandahåller anslutning (via en annan hubb) till Azure-resurser för punkt-till-plats-användare. Du kan alltid hämta en hubb-speciell VPN-konfiguration genom att navigera till hubben. Under **användar-VPN (peka på plats)** hämtar du **VPN-** profilen för den virtuella hubben användare.
1. När filen har skapats väljer du länken för att ladda ned den.
1. Använd profil filen för att konfigurera VPN-klienterna.
