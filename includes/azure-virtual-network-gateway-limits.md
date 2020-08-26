---
title: inkludera fil
description: inkludera fil
services: networking
author: anzaman
ms.service: VPN Gateway
ms.topic: include
ms.date: 08/25/2020
ms.author: alzam
ms.custom: include file
ms.openlocfilehash: 01796985a05f88b59786be7bbe0b06907cf0bc25
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854068"
---
| Resurs                                | Gräns        |
|-----------------------------------------|------------------------------|
| Prefix för VNet-adress                   | 600 per VPN-gateway          |
| Aggregera BGP-vägar                    | 4 000 per VPN-gateway        |
| Adressprefix för lokal nätverksgateway  | 1000 per lokal nätverksgateway               |
| S2S-anslutningar                         | [Beror på Gateway-SKU: n](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku)|
| P2S-anslutningar                         | [Beror på Gateway-SKU: n](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku) |
| P2S-flödes gräns – IKEv2                 | 256 för icke-Windows **/** 25 för Windows           |
| P2S-flödes gräns – OpenVPN               | 1000                         |
| Max. flows                              | 100 000 för VpnGw1/AZ  **/**  512 kB för VpnGw2-4/AZ|