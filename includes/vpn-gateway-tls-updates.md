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
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "36336034"
---
Från 1 juli 2018, tas support bort för TLS 1.0- och 1.1 från Azure VPN-Gateway. VPN-Gateway stöder endast TLS 1.2. Om du vill behålla stöd för TLS- och anslutningen för Windows 7 och Windows 8 punkt-till-plats-klienter som använder TLS, rekommenderar vi att du installera följande uppdateringar:

• [Uppdateringen för Microsoft EAP-implementering som gör det möjligt att använda TLS](https://support.microsoft.com/help/2977292/microsoft-security-advisory-update-for-microsoft-eap-implementation-th)

• [Update för att aktivera TLS 1.1 och TLS 1.2 som standard säkerhetsprotokoll i WinHTTP](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-a-default-secure-protocols-in)

Följande äldre algoritmer kommer också vara inaktuell för TLS på 1 juli 2018:

* RC4 (Rivest Cipher 4)
* DES (Data-krypteringsalgoritmen)
* 3DES (trippel Datakrypteringsalgoritm)
* MD5 (Message Digest 5)
