---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 55fa01d100c60c6411774373428ff4bbd9a56822
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986783"
---
## <a name="windows-clients"></a><a name="windows"></a>Windows-klienter

1. Ladda ner och installera OpenVPN-klienten (version 2.4 eller senare) från den officiella [OpenVPN-webbplatsen](https://openvpn.net/index.php/open-source/downloads.html).
2. Ladda ned VPN-profilen för gatewayen. Detta kan göras från fliken Konfiguration mellan punkt och plats i Azure-portalen eller "New-AzVpnClientConfiguration" i PowerShell.
3. Packa upp profilen. Öppna sedan konfigurationsfilen *vpnconfig.ovpn* från OpenVPN-mappen med Anteckningar.
4. Exportera det point-to-site-klientcertifikat som du skapade och överfört till din P2S-konfiguration på gatewayen. Använd följande artikellänkar:

   * [INSTRUKTIONER för VPN-gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport)
   
   * [Virtuella WAN-instruktioner](../articles/virtual-wan/certificates-point-to-site.md#clientexport)
5. Extrahera den privata nyckeln och bas64-tumavtrycket från *.pfx*. Det finns flera sätt att göra detta på. Att använda OpenSSL på din maskin är ett sätt. *Filen profileinfo.txt* innehåller den privata nyckeln och tumavtrycket för certifikatutfärdaren och klientcertifikatet. Var noga med att använda tumavtrycket för klientcertifikatet.

   ```
   openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
   ```
6. Öppna *profileinfo.txt* i Anteckningar. Om du vill hämta tumavtrycket för klientcertifikatet (underordnad) markerar du texten (inklusive och mellan)"-----BEGIN CERTIFICATE-----" och "-----END CERTIFICATE-----" för det underordnade certifikatet och kopierar det. Du kan identifiera det underordnade certifikatet genom att titta på ämnesraden=/raden.
7. Växla till *filen vpnconfig.ovpn* som du öppnade i Anteckningar från steg 3. Hitta avsnittet nedan och ersätt allt mellan "cert" och "/cert".

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Öppna *profileinfo.txt* i Anteckningar. Om du vill hämta den privata nyckeln markerar du texten (inklusive och mellan) "-----BEGIN PRIVATE KEY-----" och "-----END PRIVATE KEY-----" och kopierar den.
9. Gå tillbaka till filen vpnconfig.ovpn i Anteckningar och hitta det här avsnittet. Klistra in den privata nyckeln som ersätter allt mellan och "nyckel" och "/nyckel".

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```
10. Ändra inte några andra fält. Använd den ifyllda konfigurationen i klientindata för att ansluta till VPN.
11. Kopiera filen vpnconfig.ovpn till mappen C:\Program Files\OpenVPN\config.
12. Högerklicka på OpenVPN-ikonen i systemfältet och klicka på Anslut.

## <a name="mac-clients"></a><a name="mac"></a>Mac-klienter

1. Ladda ner och installera en OpenVPN-klient, till exempel [TunnelBlick](https://tunnelblick.net/downloads.html). 
2. Ladda ned VPN-profilen för gatewayen. Detta kan göras från konfigurationsfliken point-to-site i Azure-portalen eller genom att använda "New-AzVpnClientConfiguration" i PowerShell.
3. Packa upp profilen. Öppna konfigurationsfilen vpnconfig.ovpn från OpenVPN-mappen i en textredigerare.
4. Fyll i avsnittet för P2S-klientcertifikatet med P2S-klientcertifikatets offentliga nyckel i base64. I ett PEM-formaterat certifikat öppnar du bara CER-filen och kopierar över base64-nyckeln mellan certifikathuvudena. Använd följande artikellänkar för information om hur du exporterar ett certifikat för att hämta den kodade offentliga nyckeln:

   * [INSTRUKTIONER för VPN-gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer) 
   
   * [Virtuella WAN-instruktioner](../articles/virtual-wan/certificates-point-to-site.md#cer)
5. Fyll i avsnittet för den privata nyckeln med P2S-klientcertifikatets privata nyckel i base64. Se [exportera din privata nyckel](https://openvpn.net/community-resources/how-to/#pki) på OpenVPN-webbplatsen för information om hur du extraherar en privat nyckel.
6. Ändra inte några andra fält. Använd den ifyllda konfigurationen i klientindata för att ansluta till VPN.
7. Dubbelklicka på profilfilen för att skapa profilen i Tunnelblick.
8. Starta Tunnelblick från programmappen.
9. Klicka på Tunnelblick-ikonen i systemfältet och välj anslut.

> [!IMPORTANT]
>Endast iOS 11.0 och högre och MacOS 10.13 och högre stöds med OpenVPN-protokoll.
>
## <a name="ios-clients"></a><a name="iOS"></a>iOS-klienter

1. Installera OpenVPN-klienten (version 2.4 eller senare) från App Store.
2. Ladda ned VPN-profilen för gatewayen. Detta kan göras från konfigurationsfliken point-to-site i Azure-portalen eller genom att använda "New-AzVpnClientConfiguration" i PowerShell.
3. Packa upp profilen. Öppna konfigurationsfilen vpnconfig.ovpn från OpenVPN-mappen i en textredigerare.
4. Fyll i avsnittet för P2S-klientcertifikatet med P2S-klientcertifikatets offentliga nyckel i base64. I ett PEM-formaterat certifikat öppnar du bara CER-filen och kopierar över base64-nyckeln mellan certifikathuvudena. Använd följande artikellänkar för information om hur du exporterar ett certifikat för att hämta den kodade offentliga nyckeln:

   * [INSTRUKTIONER för VPN-gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer) 
   
   * [Virtuella WAN-instruktioner](../articles/virtual-wan/certificates-point-to-site.md#cer)
5. Fyll i avsnittet för den privata nyckeln med P2S-klientcertifikatets privata nyckel i base64. Se [Exportera din privata nyckel](https://openvpn.net/community-resources/how-to/#pki) på OpenVPN-webbplatsen för information om hur du extraherar en privat nyckel.
6. Ändra inte några andra fält.
7. Skicka profilfilen (.ovpn) e-post till ditt e-postkonto som konfigureras i e-postappen på din iPhone. 
8. Öppna e-postmeddelandet i e-postappen på iPhone och tryck på den bifogade filen

    ![Öppna e-post](./media/vpn-gateway-vwan-config-openvpn-clients/ios2.png)

9. Tryck på **Mer** om alternativet **Kopiera till OpenVPN** inte visas

    ![Mer](./media/vpn-gateway-vwan-config-openvpn-clients/ios3.png)

10. Tryck på **Kopiera till OpenVPN** 

    ![Kopiera till OpenVPN](./media/vpn-gateway-vwan-config-openvpn-clients/ios4.png)

11. Tryck på **ADD** på sidan **Importera profil**

    ![Lägg till](./media/vpn-gateway-vwan-config-openvpn-clients/ios5.png)

12. Tryck på **ADD** på sidan **Importerad profil**

    ![Tryck på LÄGG TILL](./media/vpn-gateway-vwan-config-openvpn-clients/ios6.png)

13. Starta OpenVPN-appen och skjut växeln på **profilsidan** direkt för att ansluta

    ![Anslut](./media/vpn-gateway-vwan-config-openvpn-clients/ios8.png)


## <a name="linux-clients"></a><a name="linux"></a>Linux-klienter

1. Öppna en ny terminalsession. Du kan öppna en ny session genom att trycka på "Ctrl + Alt + t" samtidigt.
2. Ange följande kommando för att installera nödvändiga komponenter:

   ```
   sudo apt-get install openvpn
   sudo apt-get -y install network-manager-openvpn
   sudo service network-manager restart
   ```
3. Ladda ned VPN-profilen för gatewayen. Detta kan göras från fliken Konfiguration mellan punkt och plats i Azure-portalen.
4. Exportera P2S-klientcertifikatet som du skapade och laddade upp till din P2S-konfiguration på gatewayen. Använd följande artikellänkar:

   * [INSTRUKTIONER för VPN-gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport) 
   
   * [Virtuella WAN-instruktioner](../articles/virtual-wan/certificates-point-to-site.md#clientexport)
5. Extrahera den privata nyckeln och base64 tumavtrycket från .pfx. Det finns flera sätt att göra detta på. Att använda OpenSSL på datorn är ett sätt.

    ```
    openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
    ```
   *Filen profileinfo.txt* innehåller den privata nyckeln och tumavtrycket för certifikatutfärdaren och klientcertifikatet. Var noga med att använda tumavtrycket för klientcertifikatet.

6. Öppna *profileinfo.txt* i en textredigerare. Om du vill hämta tumavtrycket för klientcertifikatet (underordnat) markerar du texten inklusive och mellan "-----BEGIN CERTIFICATE-----" och "-----END CERTIFICATE-----" för det underordnade certifikatet och kopierar det. Du kan identifiera det underordnade certifikatet genom att titta på ämnesraden=/raden.

7. Öppna *filen vpnconfig.ovpn* och hitta avsnittet nedan. Ersätt allt mellan och "cert" och "/cert".

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Öppna profileinfo.txt i en textredigerare. Om du vill hämta den privata nyckeln markerar du texten inklusive och mellan "-----BEGIN PRIVATE KEY-----" och "-----END PRIVATE KEY-----" och kopiera den.

9. Öppna filen vpnconfig.ovpn i en textredigerare och hitta det här avsnittet. Klistra in den privata nyckeln som ersätter allt mellan och "nyckel" och "/nyckel".

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```

10. Ändra inte några andra fält. Använd den ifyllda konfigurationen i klientindata för att ansluta till VPN.
11. Om du vill ansluta med kommandoraden skriver du följande kommando:
  
    ```
    sudo openvpn --config <name and path of your VPN profile file>&
    ```
12. Om du vill ansluta med det grafiska gränssnittet går du till systeminställningarna.
13. Klicka **+** här om du vill lägga till en ny VPN-anslutning.
14. Under **Lägg till VPN**väljer du Importera från **fil...**
15. Bläddra till profilfilen och dubbelklicka eller välj **Öppna**.
16. Klicka på **Lägg till** i fönstret Lägg **till VPN.**
  
    ![Importera från fil](./media/vpn-gateway-vwan-config-openvpn-clients/import.png)
17. Du kan ansluta genom att slå på VPN **på** sidan **Nätverksinställningar** eller under nätverksikonen i systemfältet.
