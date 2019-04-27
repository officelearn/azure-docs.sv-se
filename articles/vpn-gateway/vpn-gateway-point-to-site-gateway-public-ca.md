---
title: Övergången till offentlig CA-certifikat för P2S-gatewayer | Azure VPN Gateway | Microsoft Docs
description: Den här artikeln får du går över till de nya offentliga CA-certifikat för P2S-gatewayer.
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: conceptual
origin.date: 03/12/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.openlocfilehash: 29f2aeee53e07adfeafb8017c489c0b830f24b36
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60859655"
---
# <a name="transition-to-a-public-ca-gateway-certificate-for-p2s"></a>Övergång till ett offentligt CA-gatewaycertifikat för P2S

Azure VPN-Gateway utfärdar inte längre självsignerade certifikat för Azure-nivå till dess gatewayer för P2S-anslutningar. Utfärdade certifikat är nu signerade av en offentlig certifikatutfärdare (CA). Men kanske vissa äldre gateway fortfarande använder självsignerade certifikat. De självsignerade certifikaten närmar sig sitt förfallodatum och måste gå över till offentlig CA-certifikat.

>[!NOTE]
> * Självsignerat certifikat för klientautentisering för P2S påverkas inte av den här Azure-nivå certifikat ändringen. Du kan fortsätta att utfärda och använda självsignerade certifikat som vanligt.
>

Certifikat i det här sammanhanget är ett certifikat för ytterligare Azure-nivå. De är inte certifikatkedjor som du använder när du genererar din egen självsignerade rotcertifikat och klientcertifikat för autentisering. Dessa certifikat påverkas och går ut på datumen du genererat dem för att göra detta.

Tidigare behövde ett självsignerat certifikat för gateway (utfärdats i bakgrunden av Azure) uppdateras varje 18 månader. VPN-klientkonfigurationsfiler behövde sedan skapas och distribueras om till alla P2S-klienter. Flytta till offentliga certifikat eliminerar den här begränsningen. Förutom övergången för certifikat finns den här ändringen även förbättringar för plattformen, bättre mått och förbättrad stabilitet.

Endast äldre gatewayer som påverkas av den här ändringen. Om din gateway-certifikatet måste överföras, får du kommunikation eller popup i Azure-portalen. Du kan kontrollera om din gateway påverkas med hjälp av stegen i den här artikeln.

> [!IMPORTANT]
> Övergången är schemalagd för mars 12 2019 kl. 18:00 UTC. Du kan skapa ett supportärende om du föredrar ett annat tidsintervall. Se och slutföra din begäran minst 24 timmar i förväg.  Du kan begära en av följande fönster:
>
> * 06:00 UTC den 25 februari
> * 18:00 UTC den 25 februari
> * 06:00 UTC på 1 mars
> * 18:00 UTC på 1 mars
>
> **Alla återstående gatewayer övergår mars 12 2019 kl. 18:00 UTC**.
>
> Kunderna får ett e-postmeddelande när deras gateway Slutför övergången.
> 

## <a name="1-verify-your-certificate"></a>1. Kontrollera ditt certifikat

### <a name="resource-manager"></a>Resource Manager

1. Kontrollera om du påverkas av den här uppdateringen. Ladda ned din aktuella VPN-klientkonfiguration med hjälp av stegen i [i den här artikeln](point-to-site-vpn-client-configuration-azure-cert.md).

2. Öppna eller extrahera zip-filen och bläddra till mappen ”Generic”. I den generiska mappen visas två filer, varav *VPNSettings.xml*.
3. Öppna *VPNSettings.xml* i en xml-viewer /-redigerare. Sök efter följande fält i xml-fil:

   * `<ServerCertRootCn>DigiCert Global Root CA</ServerCertRootCn>`
   * `<ServerCertIssuerCn>DigiCert Global Root CA</ServerCertIssuerCn>`
4. Om *ServerCertRotCn* och *ServerCertIssuerCn* är ”DigiCert globala Rotcertifikatutfärdare”, du påverkas inte av den här uppdateringen och du behöver inte fortsätta med stegen i den här artikeln. Men om de visar något annat gateway-certifikatet är en del av uppdateringen och kommer att överföras.

### <a name="classic"></a>Klassisk

1. Navigera till sökväg på en klientdator `%appdata%/Microsoft/Network/Connections/Cm/<gatewayID>`. Du kan visa certifikatet i Gateway-ID-mappen.
2. Kontrollera att certifikatutfärdaren är ”DigiCert globala Rotcertifikatutfärdare” på fliken Allmänt för certifikatet. Om du har något annat än denna utfärdande myndighet, gateway-certifikatet är en del av uppdateringen och kommer att överföras.

## <a name="2-check-certificate-transition-schedule"></a>2. Kontrollera certifikatet övergången schema

Om certifikatet ingår i uppdateringen, kommer gateway-certifikatet att överföras. Referera till den **viktigt** Obs för övergången gånger. Efter uppdateringen P2S-klienter inte att ansluta med sina gamla profilen. Du måste generera nya profiler för VPN-klienten och installera dem på klienter.

## <a name="3-generate-vpn-client-configuration-profile"></a>3. Generera VPN-profil för klienten

När certifikatet har övergått, kan du hämta den nya VPN-profilen (VPN-klientkonfigurationsfiler) från Azure-portalen. Anvisningar finns i [skapa och installera VPN-klientkonfigurationsfiler](point-to-site-vpn-client-configuration-azure-cert.md). Du behöver inte att generera nya klientcertifikat.

## <a name="4-deploy-vpn-client-profile"></a>4. Distribuera VPN-profil för klienten

Distribuera den nya profilen till alla klienter för punkt-till-plats-VPN. VPN-klienter kommer att förlora anslutningen förrän den nya VPN-profilen för punkt-till-plats-anslutningar hämtas och distribueras till klientenheter. Klientcertifikaten som redan är installerade på VPN-klientdatorer behöver inte utfärdas.

## <a name="5-connect-the-vpn-client"></a>5. Ansluta VPN-klienten

Ansluta till Azure från VPN-klienten som vanligt.