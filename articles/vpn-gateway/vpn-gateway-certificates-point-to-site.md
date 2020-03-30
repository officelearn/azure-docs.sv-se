---
title: 'Generera och exportera certifikat för P2S: PowerShell'
titleSuffix: Azure VPN Gateway
description: Skapa ett självsignerat rotcertifikat, exportera den offentliga nyckeln och generera klientcertifikat med PowerShell i Windows 10 eller Windows Server 2016.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: cherylmc
ms.openlocfilehash: f28e76e9dcaf1331fd26a2321cd4deca1027e693
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279343"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-powershell"></a>Generera och exportera certifikat för Punkt-till-plats med PowerShell

Point-to-Site-anslutningar använder certifikat för att autentisera. Den här artikeln visar hur du skapar ett självsignerat rotcertifikat och genererar klientcertifikat med PowerShell på Windows 10 eller Windows Server 2016. Om du letar efter olika certifikatinstruktioner, se [Certifikat - Linux](vpn-gateway-certificates-point-to-site-linux.md) eller certifikat - [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md).

Du måste utföra stegen i den här artikeln på en dator med Windows 10 eller Windows Server 2016. De PowerShell-cmdletar som du använder för att generera certifikat är en del av operativsystemet och fungerar inte på andra versioner av Windows. Windows 10- eller Windows Server 2016-datorn behövs bara för att generera certifikaten. När certifikaten har genererats kan du ladda upp dem eller installera dem på alla klientoperativsystem som stöds.

Om du inte har tillgång till en Windows 10- eller Windows Server 2016-dator kan du använda [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) för att generera certifikat. De certifikat som du genererar med någon av metoderna kan installeras på alla klientoperativsystem [som stöds.](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq)

[!INCLUDE [generate and export certificates](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="install-an-exported-client-certificate"></a><a name="install"></a>Installera ett exporterat klientcertifikat

Varje klient som ansluter till det virtuella nätverket via en P2S-anslutning kräver att ett klientcertifikat installeras lokalt.

Om du vill installera ett klientcertifikat finns i [Installera ett klientcertifikat för Point-to-Site-anslutningar](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="next-steps"></a>Nästa steg

Fortsätt med konfigurationen för Punkt-till-plats.

* Steg för **Resurshanterarens** distributionsmodell finns i [Konfigurera P2S med inbyggd Azure-certifikatautentisering](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* Steg för **klassiska** distributionsmodeller finns i [Konfigurera en punkt-till-plats-VPN-anslutning till ett VNet (klassiskt)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).