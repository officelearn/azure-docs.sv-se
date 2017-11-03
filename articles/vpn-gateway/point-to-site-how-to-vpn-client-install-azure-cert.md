---
title: "Installera ett certifikat för P2S | Azure"
description: "Den här artikeln hjälper dig att installera en klient certifikat för P2S certifikatautentisering."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager, azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2017
ms.author: cherylmc
ms.openlocfilehash: fc0cc37794ef291c9d27b094211b38cec90da55c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="install-a-client-certificate-for-point-to-site-azure-certificate-authentication-connections"></a>Installera ett klientcertifikat för autentisering anslutningar för punkt-till-plats Azure certifikat

Alla klienter som ansluter till ett virtuellt nätverk med Azure certifikatautentisering för punkt-till-plats kräver ett klientcertifikat. Den här artikeln hjälper dig att installera ett klientcertifikat som används för autentisering när du ansluter till ett VNet med P2S.

## <a name="generate"></a>Skapa och exportera ett certifikat

Du kan generera ett certifikat från ett rotcertifikat som skapades med en Företagscertifikatutfärdare lösning eller ett självsignerat rotcertifikat. Finns det [PowerShell](vpn-gateway-certificates-point-to-site.md) eller [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) instruktioner om åtgärder. Exportera dem efter genererar klientcertifikat som PFX-filer. Se till att inkludera hela certifikatkedjan när du exporterar.

## <a name="installwin"></a>Installera ett certifikat på Windows-klienter

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="installmac"></a>Installera ett certifikat på Mac-klienter

Mac-VPN-klienter stöds endast distributionsmodell hanteraren för filserverresurser. De stöds inte för den klassiska distributionsmodellen.

> [!NOTE]
>  IKEv2 finns för närvarande i en förhandsversion.
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="next-steps"></a>Nästa steg

Fortsätt med konfigurationssteg punkt-till-plats.

* [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
* [Azure Portal (klassisk)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)