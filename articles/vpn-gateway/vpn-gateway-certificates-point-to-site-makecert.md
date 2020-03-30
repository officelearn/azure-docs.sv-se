---
title: 'Azure VPN Gateway: Generera & exportcertifikat för P2S: MakeCert'
description: Skapa ett självsignerat rotcertifikat, exportera den offentliga nyckeln och generera klientcertifikat med MakeCert.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/05/2018
ms.author: cherylmc
ms.openlocfilehash: ad2ab31e6771efc54238d5747863fa2a9bb2f356
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75833979"
---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-makecert"></a>Generera och exportera certifikat för Point-to-Site-anslutningar med MakeCert

Point-to-Site-anslutningar använder certifikat för att autentisera. Den här artikeln visar hur du skapar ett självsignerat rotcertifikat och genererar klientcertifikat med MakeCert. Om du letar efter olika certifikatinstruktioner läser du [Certifikat - PowerShell](vpn-gateway-certificates-point-to-site.md) eller [certifikat - Linux](vpn-gateway-certificates-point-to-site-linux.md).

Vi rekommenderar att du använder [Windows 10 PowerShell-stegen](vpn-gateway-certificates-point-to-site.md) för att skapa dina certifikat, men vi tillhandahåller dessa MakeCert-instruktioner som en valfri metod. De certifikat som du genererar med någon av metoderna kan installeras på [alla klientoperativsystem som stöds](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq). MakeCert har dock följande begränsning:

* MakeCert är inaktuellt. Detta innebär att det här verktyget kan tas bort när som helst. Alla certifikat som du redan har genererat med MakeCert påverkas inte när MakeCert inte längre är tillgängligt. MakeCert används endast för att generera certifikaten, inte som en validerande mekanism.

## <a name="create-a-self-signed-root-certificate"></a><a name="rootcert"></a>Skapa ett självsignerat rotcertifikat

Följande steg visar hur du skapar ett självsignerat certifikat med MakeCert. Dessa steg är inte distributionsmodellspecifika. De gäller både Resurshanteraren och klassiskt.

1. Ladda ner och installera [MakeCert](https://msdn.microsoft.com/library/windows/desktop/aa386968(v=vs.85).aspx).
2. Efter installationen kan du vanligtvis hitta verktyget makecert.exe under den här sökvägen: "C:\Program Files (x86)\Windows Kits\10\bin\<arch>". Även om det är möjligt att det installerades på en annan plats. Öppna en kommandotolk som administratör och navigera till platsen för verktyget MakeCert. Du kan använda följande exempel och justera för rätt plats:

   ```cmd
   cd C:\Program Files (x86)\Windows Kits\10\bin\x64
   ```
3. Skapa och installera ett certifikat i det personliga certifikatarkivet på datorn. I följande exempel skapas en motsvarande *.cer-fil* som du överför till Azure när du konfigurerar P2S. Ersätt "P2SRootCert" och "P2SRootCert.cer" med det namn som du vill använda för certifikatet. Certifikatet finns i certifikaten "Certifikat - aktuell användare\Personliga\certifikat".

   ```cmd
   makecert -sky exchange -r -n "CN=P2SRootCert" -pe -a sha256 -len 2048 -ss My
   ```

## <a name="export-the-public-key-cer"></a><a name="cer"></a>Exportera den offentliga nyckeln (.cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

Filen exported.cer måste överföras till Azure. Instruktioner finns i [Konfigurera en point-to-site-anslutning](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile). Information om hur du lägger till ytterligare ett betrott rotcertifikat finns i [det här avsnittet](vpn-gateway-howto-point-to-site-resource-manager-portal.md#add) i artikeln.

### <a name="export-the-self-signed-certificate-and-private-key-to-store-it-optional"></a>Exportera det självsignerade certifikatet och den privata nyckeln för att lagra det (valfritt)

Du kanske vill exportera det självsignerade rotcertifikatet och lagra det på ett säkert sätt. Om det behövs kan du senare installera den på en annan dator och generera fler klientcertifikat eller exportera en annan CER-fil. Om du vill exportera det självsignerade rotcertifikatet som en PFX markerar du rotcertifikatet och använder samma steg som beskrivs i [Exportera ett klientcertifikat](#clientexport).

## <a name="create-and-install-client-certificates"></a>Skapa och installera klientcertifikat

Du installerar inte det självsignerade certifikatet direkt på klientdatorn. Du måste generera ett klientcertifikat från det självsignerade certifikatet. Du exporterar och installerar sedan klientcertifikatet till klientdatorn. Följande steg är inte distributionsmodellspecifika. De gäller både Resurshanteraren och klassiskt.

### <a name="generate-a-client-certificate"></a><a name="clientcert"></a>Generera ett klientcertifikat

Varje klientdator som ansluter till ett virtuellt nätverk med punkt-till-plats måste ha ett klientcertifikat installerat. Du kan generera ett klientcertifikat från det självsignerade rotcertifikatet och sedan exportera och installera klientcertifikatet. Om klientcertifikatet inte är installerat misslyckas autentiseringen. 

I följande steg går du igenom att generera ett klientcertifikat från ett självsignerat rotcertifikat. Du kan generera flera klientcertifikat från samma rotcertifikat. När du genererar klientcertifikat med hjälp av stegen nedan installeras klientcertifikatet automatiskt på den dator som du använde för att generera certifikatet. Om du vill installera ett klientcertifikat på en annan klientdator kan du exportera certifikatet.
 
1. Öppna en kommandotolk som administratör på samma dator som du använde för att skapa det självsignerade certifikatet.
2. Ändra och kör exemplet för att generera ett klientcertifikat.
   * Ändra *"P2SRootCert"* till namnet på den självsignerade rot som du genererar klientcertifikatet från. Kontrollera att du använder namnet på rotcertifikatet, vilket är vad "CN="-värdet angavs när du skapade den självsignerade roten.
   * Ändra *P2SChildCert* till det namn som du vill generera ett klientcertifikat som ska vara.

   Om du kör följande exempel utan att ändra det blir resultatet ett klientcertifikat med namnet P2SChildcert i ditt personliga certifikatarkiv som genererades från rotcertifikat P2SRootCert.

   ```cmd
   makecert.exe -n "CN=P2SChildCert" -pe -sky exchange -m 96 -ss My -in "P2SRootCert" -is my -a sha256
   ```

### <a name="export-a-client-certificate"></a><a name="clientexport"></a>Exportera ett klientcertifikat

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]

### <a name="install-an-exported-client-certificate"></a><a name="install"></a>Installera ett exporterat klientcertifikat

Om du vill installera ett klientcertifikat finns i [Installera ett klientcertifikat](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="next-steps"></a>Nästa steg

Fortsätt med konfigurationen för Punkt-till-plats. 

* Steg för **Resurshanterarens** distributionsmodell finns i [Konfigurera P2S med inbyggd Azure-certifikatautentisering](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* Steg för **klassiska** distributionsmodeller finns i [Konfigurera en punkt-till-plats-VPN-anslutning till ett VNet (klassiskt)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).

Information om P2S-felsökning finns i [Felsöka punkt-till-plats-anslutningar i Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).