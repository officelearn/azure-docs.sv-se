---
title: Installera ett certifikat för P2S | Azure
description: Installera en Mac- eller Windows client-certifikat för P2S certifikatautentisering.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jeconnoc
editor: ''
tags: azure-resource-manager, azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2018
ms.author: cherylmc
ms.openlocfilehash: bf2788fff64ab8b3a5ccf75b8a80f2bd5aba5151
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="install-a-client-certificate-for-point-to-site-azure-certificate-authentication-connections"></a>Installera ett klientcertifikat för autentisering anslutningar för punkt-till-plats Azure certifikat

Alla klienter som ansluter till ett virtuellt nätverk med Azure certifikatautentisering för punkt-till-plats kräver ett klientcertifikat. Den här artikeln hjälper dig att installera ett klientcertifikat som används för autentisering när du ansluter till ett VNet med P2S.

## <a name="generate"></a>Skapa och exportera ett certifikat

Du kan generera ett certifikat från ett rotcertifikat som skapades med en Företagscertifikatutfärdare lösning eller ett självsignerat rotcertifikat. Finns det [PowerShell](vpn-gateway-certificates-point-to-site.md) eller [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) instruktioner om åtgärder. Exportera dem efter genererar klientcertifikat som PFX-filer. Se till att inkludera hela certifikatkedjan när du exporterar.

## <a name="installwin"></a>Installera certifikat – Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="installmac"></a>Installera certifikat – Mac

Mac-VPN-klienter stöds endast distributionsmodell hanteraren för filserverresurser. De stöds inte för den klassiska distributionsmodellen.

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="next-steps"></a>Nästa steg

Fortsätt med konfigurationssteg punkt-till-plats.

* [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
* [Azure Portal (klassisk)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
