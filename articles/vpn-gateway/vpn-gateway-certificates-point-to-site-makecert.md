---
title: 'Skapa och exportera certifikat för punkt-till-plats: MakeCert: Azure | Microsoft Docs'
description: Skapa ett självsignerat rotcertifikat, exportera den offentliga nyckeln och generera klientcertifikat med hjälp av MakeCert.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2018
ms.author: cherylmc
ms.openlocfilehash: b2f31761e4560cf4b9b9a5b92f5de9982a663a75
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38651795"
---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-makecert"></a>Skapa och exportera certifikat till punkt-till-plats-anslutningar med hjälp av MakeCert

Punkt-till-plats-anslutningar använder certifikat för autentisering. Den här artikeln visar hur du skapar ett självsignerat rotcertifikat och generera klientcertifikat med hjälp av MakeCert. Om du letar efter konfigurationssteg för punkt-till-plats, till exempel hur du överför rotcertifikat, väljer du en av artiklarna ”Configure punkt-till-plats” i listan nedan:

> [!div class="op_single_selector"]
> * [Skapa självsignerade certifikat – PowerShell](vpn-gateway-certificates-point-to-site.md)
> * [Skapa självsignerade certifikat - MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)
> * [Konfigurera punkt-till-plats - Resource Manager – Azure portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Konfigurera punkt-till-plats - Resource Manager – PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Konfigurera punkt-till-plats - klassisk – Azure portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

Medan vi rekommenderar att du använder den [steg för Windows 10 PowerShell](vpn-gateway-certificates-point-to-site.md) för att skapa dina certifikat, vi tillhandahåller instruktionerna MakeCert som en valfri metod. De certifikat som du genererar med hjälp av någon av metoderna kan installeras på [alla stöds klientoperativsystemet](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq). MakeCert har dock följande begränsningar:

* MakeCert är inaktuellt. Det innebär att det här verktyget kan tas bort när som helst. Certifikat som du redan har skapats med hjälp av MakeCert påverkas inte när MakeCert är inte längre tillgänglig. MakeCert endast används för att generera certifikat, inte som en mekanism för verifiering.

## <a name="rootcert"></a>Skapa ett självsignerat rotcertifikat

Följande steg visar hur du skapar ett självsignerat certifikat med hjälp av MakeCert. De här stegen är inte distributionsmodellen specifika. De är giltiga för både Resource Manager och klassisk.

1. Ladda ned och installera [MakeCert](https://msdn.microsoft.com/library/windows/desktop/aa386968(v=vs.85).aspx).
2. Efter installationen kan du vanligtvis hittar du verktyget makecert.exe under den här sökvägen ”: C:\Program Files (x86) \Windows Kits\10\bin\<arch >'. Även om det är möjligt att den har installerats på en annan plats. Öppna en kommandotolk som administratör och navigera till platsen för verktyget MakeCert. Du kan använda följande exempel justera för rätt plats:

  ```cmd
  cd C:\Program Files (x86)\Windows Kits\10\bin\x64
  ```
3. Skapa och installera ett certifikat i det personliga certifikatarkivet på datorn. I följande exempel skapas en motsvarande *.cer* fil som du överför till Azure när du konfigurerar P2S. Ersätt ”P2SRootCert” och namnet ”P2SRootCert.cer” med namnet som du vill använda för certifikatet. Certifikatet finns i din ”certifikat – aktuell användare\personligt\certifikat”.

  ```cmd
  makecert -sky exchange -r -n "CN=P2SRootCert" -pe -a sha256 -len 2048 -ss My
  ```

## <a name="cer"></a>Exportera den offentliga nyckeln (.cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

Filen exported.cer måste överföras till Azure. Anvisningar finns i [konfigurera en punkt-till-plats-anslutning](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile). Om du vill lägga till en ytterligare betrodda rotcertifikat, se [i det här avsnittet](vpn-gateway-howto-point-to-site-resource-manager-portal.md#add) av artikeln.

### <a name="export-the-self-signed-certificate-and-private-key-to-store-it-optional"></a>Exportera självsignerat certifikat och lagra den (valfritt) den privata nyckeln

Du kanske vill exportera det självsignerade rotcertifikatet och lagra den på ett säkert sätt. Om behöver vara du senare kan installera den på en annan dator och generera mer klientcertifikat eller exportera en annan .cer-fil. Välj rotcertifikat för att exportera det självsignerade rotcertifikatet som PFX-fil, och använda samma steg som beskrivs i [exportera ett klientcertifikat](#clientexport).

## <a name="create-and-install-client-certificates"></a>Skapa och installera klientcertifikat

Installera inte det självsignerade certifikatet direkt på klientdatorn. Du måste generera ett klientcertifikat från det självsignerade certifikatet. Exportera och installera klientcertifikatet på klientdatorn. Följande steg är inte distributionsmodellen specifika. De är giltiga för både Resource Manager och klassisk.

### <a name="clientcert"></a>Generera ett klientcertifikat

Varje klientdator som ansluter till ett virtuellt nätverk med punkt-till-plats måste ha ett klientcertifikat installerat. Du skapar ett klientcertifikat från det självsignerade rotcertifikatet och sedan exportera och installera klientcertifikatet. Om klientcertifikatet inte är installerat, misslyckas autentiseringen. 

I följande steg vägleder dig genom att generera ett klientcertifikat från ett självsignerat rotcertifikat. Du kan skapa flera klientcertifikat från samma rotcertifikat. När du genererar klientcertifikat med nedanstående steg behöver installeras automatiskt klientcertifikatet på den dator som du använde för att generera certifikatet. Om du vill installera ett klientcertifikat på en annan klientdator kan du exportera certifikatet.
 
1. Öppna en kommandotolk som administratör på samma dator som du använde för att skapa ett självsignerat certifikat.
2. Ändra och köra exemplet för att generera ett klientcertifikat.
  * Ändra *”P2SRootCert”* till namnet på den självsignerat rotcertifikat som du genererar klientcertifikat från. Kontrollera att du använder namnet på rotcertifikatet, vilket är det den ”CN =' värdet var som du angav när du skapade självsignerat rotcertifikat.
  * Ändra *P2SChildCert* till namn som du vill generera ett klientcertifikat för att vara.

  Om du kör följande exempel utan att ändra den är resultatet ett klientcertifikat med namnet P2SChildcert i ditt personliga certifikatarkiv som har genererats från rotcertifikatet P2SRootCert.

  ```cmd
  makecert.exe -n "CN=P2SChildCert" -pe -sky exchange -m 96 -ss My -in "P2SRootCert" -is my -a sha256
  ```

### <a name="clientexport"></a>Exportera ett klientcertifikat

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]

### <a name="install"></a>Installera en exporterade klientcertifikat

Om du vill installera ett klientcertifikat, se [installera ett klientcertifikat](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="next-steps"></a>Nästa steg

Fortsätt med punkt-till-plats-konfiguration. 

* För **Resource Manager** modell distributionsstegen, se [konfigurera P2S med hjälp av Azures interna certifikatautentisering](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* För **klassiska** modell distributionsstegen, se [konfigurera en punkt-till-plats-VPN-anslutning till ett virtuellt nätverk (klassisk)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).

Information om P2S-felsökning finns i [Felsöka punkt-till-plats-anslutningar i Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).