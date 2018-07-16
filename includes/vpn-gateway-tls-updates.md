---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/05/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9a17f34333503436d3da340670abdde154e45ef6
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38727546"
---
Från och med 1 juli 2018 tas stödet för TLS 1.0 och 1.1 bort från Azure VPN Gateway. VPN Gateway kommer endast att stödja TLS 1.2. Om du vill behålla TLS-stöd och -anslutning för dina punkt-till-plats-klienter med Windows 7 och Windows 8 som använder TLS rekommenderar vi att du installerar följande uppdateringar:

•   [Uppdatering för Microsoft EAP-implementering som ger stöd för användning av TLS](https://support.microsoft.com/help/2977292/microsoft-security-advisory-update-for-microsoft-eap-implementation-th)

•   [Uppdatering för att aktivera TLS 1.1 och TLS 1.2 som standardsäkerhetsprotokoll i WinHTTP](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-a-default-secure-protocols-in)

Följande äldre algoritmer kommer också att bli inaktuella för TLS 1 juli 2018:

* RC4 (Rivest Cipher 4)
* DES (Data Encryption Algorithm, datakrypteringsalgoritm)
* 3DES (Triple Data Encryption Algorithm, tredubbel datakrypteringsalgoritm)
* MD5 (Message Digest 5)
