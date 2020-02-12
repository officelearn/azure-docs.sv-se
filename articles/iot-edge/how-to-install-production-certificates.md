---
title: Installera certifikat på enheten – Azure IoT Edge | Microsoft Docs
description: Skapa test certifikat och lär dig hur du installerar dem på en Azure IoT Edge enhet för att förbereda för produktions distribution.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: fe46e968aa2dcebaa483cd38fd2e050ccfe43054
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149906"
---
# <a name="install-production-certificates-on-an-iot-edge-device"></a>Installera produktions certifikat på en IoT Edge enhet

Alla IoT Edge enheter använder certifikat för att skapa säkra anslutningar mellan körningen och alla moduler som körs på enheten.
IoT Edge enheter som fungerar som gateways använder samma certifikat för att ansluta till deras underordnade enheter också.

När du först installerar IoT Edge och etablerar enheten, konfigureras enheten med tillfälliga certifikat så att du kan testa tjänsten.
Dessa tillfälliga certifikat upphör att gälla om 90 dagar eller kan återställas genom att starta om datorn.
När du är redo att flytta dina enheter till ett produktions scenario, eller om du vill skapa ett Gateway-scenario, måste du ange dina egna certifikat.
Den här artikeln visar stegen för att installera certifikat på dina IoT Edge enheter.

Mer information om de olika typerna av certifikat och deras roller i ett IoT Edge scenario finns i [förstå hur Azure IoT Edge använder certifikat](iot-edge-certs.md).

>[!NOTE]
>Termen "rot certifikat utfärdare" som används i den här artikeln refererar till det offentliga utfärdade certifikat kedjan för din IoT-lösning. Du behöver inte använda certifikat roten för en insyndikerad certifikat utfärdare eller roten för organisationens certifikat utfärdare. I många fall är det faktiskt ett offentligt certifikat för certifikat utfärdare.

## <a name="prerequisites"></a>Förutsättningar

* En IoT Edge enhet som körs antingen på [Windows](how-to-install-iot-edge-windows.md) eller [Linux](how-to-install-iot-edge-linux.md).
* Ha ett certifikat från en rot certifikat utfärdare (CA), antingen självsignerat eller köpt från en betrodd kommersiell certifikat utfärdare som Baltimore, VeriSign, DigiCert eller GlobalSign.

Om du inte har en rot certifikat utfärdare än, men vill testa IoT Edge funktioner som kräver produktions certifikat (till exempel Gateway-scenarier) kan du [skapa demonstrations certifikat för att testa IoT Edge enhets funktioner](how-to-create-test-certificates.md).

## <a name="create-production-certificates"></a>Skapa produktions certifikat

Du bör använda din egen certifikat utfärdare för att skapa följande filer:

* Rot certifikat utfärdare
* Enhets-CA-certifikat
* Privat nyckel för enhets certifikat utfärdare

I den här artikeln refererar vi till som *rot certifikat utfärdare* är inte den översta certifikat utfärdaren för en organisation. Det är den översta certifikat utfärdaren för IoT Edge scenariot, som IoT Edge Hub-modulen, användarattribut och eventuella underordnade enheter använder för att upprätta förtroende mellan varandra.

Om du vill se ett exempel på dessa certifikat granskar du skripten som skapar demo certifikat i [Hantera test CA-certifikat för exempel och självstudier](https://github.com/Azure/iotedge/tree/master/tools/CACertificates).

## <a name="install-certificates-on-the-device"></a>Installera certifikat på enheten

Installera certifikat kedjan på den IoT Edge enheten och konfigurera IoT Edge runtime så att den refererar till de nya certifikaten.

Om du till exempel använde exempel skripten för att [skapa demo certifikat](how-to-create-test-certificates.md), är de tre filer som du måste kopiera till din IoT Edge-enhet följande:

* Enhetens CA-certifikat: `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* Privat nyckel för enhetens certifikat utfärdare: `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* Rot certifikat utfärdare: `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

1. Kopiera de tre certifikat-och nyckelfilerna till din IoT Edge-enhet.

   Du kan använda en tjänst som [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) eller en funktion som [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) för att flytta certifikatfiler.  Om du har genererat certifikaten på själva enheten för IoT Edge kan du hoppa över det här steget och använda sökvägen till arbets katalogen.

2. Öppna konfigurationsfilen för IoT Edge security daemon.

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Ange **certifikat** egenskaperna i filen config. yaml till fil-URI: n för certifikatet och nyckelfilen på den IoT Edge enheten. Ta bort `#`-tecknen innan certifikat egenskaperna tar bort kommentarer till de fyra raderna. Se till att det inte finns några föregående blank steg i raden **certifikat:** rad och att kapslade objekt är indragna med två blank steg. Några exempel:

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "file:///c:/path/device-ca.cert.pem"
        device_ca_pk: "file:///c:/path/device-ca.key.pem"
        trusted_ca_certs: "file:///c:/path/root-ca.root.ca.cert.pem"
      ```

   * Linux:

      ```yaml
      certificates:
        device_ca_cert: "file:///path/device-ca.cert.pem"
        device_ca_pk: "file:///path/device-ca.key.pem"
        trusted_ca_certs: "file:///path/root-ca.root.ca.cert.pem"
      ```

4. På Linux-enheter ser du till att användar **iotedge** har Läs behörighet för den katalog som innehåller certifikaten.

5. Om du har använt andra certifikat för IoT Edge på enheten tidigare tar du bort filerna i följande två kataloger innan du startar eller startar om IoT Edge:

   * Windows: `C:\ProgramData\iotedge\hsm\certs` och `C:\ProgramData\iotedge\hsm\cert_keys`

   * Linux: `/var/lib/iotedge/hsm/certs` och `/var/lib/iotedge/hsm/cert_keys`

## <a name="next-steps"></a>Nästa steg

Att installera certifikat på en IoT Edge enhet är ett nödvändigt steg innan du distribuerar din lösning i produktionen. Lär dig mer om hur du [förbereder för att distribuera IoT Edge-lösningen i produktionen](production-checklist.md).
