---
title: 'Azure VPN Gateway: Installera ett point-to-site-klientcertifikat'
description: Installera klientcert för P2S-certifikatautentisering - Windows, Mac, Linux.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/10/2020
ms.author: cherylmc
ms.openlocfilehash: 787b8a34ed4b232b9e6cc033e67b1a8162c85f6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75902837"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>Installera klientcertifikat för P2S-certifikatautentiseringsanslutningar

Alla klienter som ansluter till ett virtuellt nätverk med Point-to-Site Azure-certifikatautentisering kräver ett klientcertifikat. Den här artikeln hjälper dig att installera ett klientcertifikat som används för autentisering när du ansluter till ett virtuella nätverk med P2S.

## <a name="acquire-a-client-certificate"></a><a name="generate"></a>Skaffa ett klientcertifikat

Oavsett vilket klientoperativsystem du vill ansluta från måste du alltid ha ett klientcertifikat. Du kan generera ett klientcertifikat från antingen ett rotcertifikat som genererades med hjälp av en Enterprise-certifikatutfärdarlösning eller ett självsignerat rotcertifikat. Se [PowerShell-,](vpn-gateway-certificates-point-to-site.md) [MakeCert-](vpn-gateway-certificates-point-to-site-makecert.md)eller [Linux-instruktionerna](vpn-gateway-certificates-point-to-site-linux.md) för steg för att generera ett klientcertifikat. 

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="mac"></a><a name="installmac"></a>Mac

>[!NOTE]
>Mac VPN-klienter stöds endast för Resurshanterarens distributionsmodell. De stöds inte för den klassiska distributionsmodellen.
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="linux"></a><a name="installlinux"></a>Linux

Linux-klientcertifikatet installeras på klienten som en del av klientkonfigurationen. Se [Klientkonfiguration - Linux](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli) för instruktioner.

## <a name="next-steps"></a>Nästa steg

Fortsätt med konfigurationsstegen punkt till plats för att [skapa och installera konfigurationsfiler för VPN-klient](point-to-site-vpn-client-configuration-azure-cert.md).
