---
title: Hantera enhetscertifikat – Azure IoT Edge | Microsoft-dokument
description: Skapa testcertifikat, installera och hantera dem på en Azure IoT Edge-enhet för att förbereda för produktionsdistribution.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c18c3d560adb3c3cae54bda808ee5842c260fd6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79539213"
---
# <a name="manage-certificates-on-an-iot-edge-device"></a>Hantera certifikat på en IoT Edge-enhet

Alla IoT Edge-enheter använder certifikat för att skapa säkra anslutningar mellan körningen och alla moduler som körs på enheten. IoT Edge-enheter som fungerar som gateways använder samma certifikat för att ansluta till sina nedströmsenheter också.

## <a name="install-production-certificates"></a>Installera produktionscertifikat

När du först installerar IoT Edge och etablerar enheten konfigureras enheten med tillfälliga certifikat så att du kan testa tjänsten.
Dessa tillfälliga certifikat upphör att gälla om 90 dagar eller kan återställas genom att starta om datorn.
När du är redo att flytta dina enheter till ett produktionsscenario, eller om du vill skapa ett gatewayscenario, måste du ange dina egna certifikat.
Den här artikeln visar stegen för att installera certifikat på dina IoT Edge-enheter.

Mer information om de olika typerna av certifikat och deras roller i ett IoT Edge-scenario finns i [Förstå hur Azure IoT Edge använder certifikat](iot-edge-certs.md).

>[!NOTE]
>Termen "root CA" som används i hela den här artikeln refererar till det högsta myndighetsutfärdarcertifikatet för certifikatkedjan för din IoT-lösning. Du behöver inte använda certifikatroten för en konsortiecertifikatutfärder eller roten till organisationens certifikatutfärdaren. I många fall är det faktiskt ett offentligt certifikat för den mellanliggande certifikatutfärdaren.

### <a name="prerequisites"></a>Krav

* En IoT Edge-enhet som körs antingen på [Windows](how-to-install-iot-edge-windows.md) eller [Linux](how-to-install-iot-edge-linux.md).
* Har ett certifikatutfärdarcertifikat (Root Certificate Authority), antingen självsignerat eller köpt från en betrodd kommersiell certifikatutfärdare som Baltimore, Verisign, DigiCert eller GlobalSign.

Om du inte har någon root-certifikatutfärdare ännu, men vill prova IoT Edge-funktioner som kräver produktionscertifikat (som gatewayscenarier) kan du [skapa democertifikat för att testa IoT Edge-enhetsfunktioner](how-to-create-test-certificates.md).

### <a name="create-production-certificates"></a>Skapa produktionscertifikat

Du bör använda din egen certifikatutfärdare för att skapa följande filer:

* Rotcertifikatutfärdare
* Certifikat för enhetscertifikatutfärdarcertifikat
* Privat nyckel för enhetscautator

I den här artikeln är det vi kallar *rotcertifikatutfärdaren* inte den översta certifikatutfärdaren för en organisation. Det är den översta certifikatutfärdaren för IoT Edge-scenariot, som IoT Edge-hubbmodulen, användarmoduler och alla nedströmsenheter använder för att skapa förtroende mellan varandra.

Om du vill se ett exempel på dessa certifikat granskar du skripten som skapar democertifikat i [Hantera certifikatutfärdarcertifikat för exempel och självstudier](https://github.com/Azure/iotedge/tree/master/tools/CACertificates).

### <a name="install-certificates-on-the-device"></a>Installera certifikat på enheten

Installera certifikatkedjan på IoT Edge-enheten och konfigurera IoT Edge-körningen för att referera till de nya certifikaten.

Om du till exempel har använt exempelskripten för att [skapa democertifikat kopierar](how-to-create-test-certificates.md)du följande filer till din IoT-Edge-enhet:

* Certifikat för enhetscertifikatutfärdare:`<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* Privat nyckel för enhets-CA:`<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* Rot CA:`<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

1. Kopiera de tre certifikat- och nyckelfilerna till din IoT Edge-enhet.

   Du kan använda en tjänst som [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) eller en funktion som Secure copy [protocol](https://www.ssh.com/ssh/scp/) för att flytta certifikatfilerna.  Om du har genererat certifikaten på själva IoT Edge-enheten kan du hoppa över det här steget och använda sökvägen till arbetskatalogen.

1. Öppna IoT Edge-säkerhetsdemon config-filen.

   * Windows:`C:\ProgramData\iotedge\config.yaml`
   * Linux:`/etc/iotedge/config.yaml`

1. Ange **certifikategenskaperna** i filen config.yaml till den fullständiga sökvägen till certifikatet och nyckelfilerna på IoT Edge-enheten. Ta `#` bort tecknet före certifikategenskaperna för att ta bort kommentering av de fyra raderna. Kontrollera att **certifikaten:** raden inte har något föregående blanktecken och att kapslade objekt är indragna med två blanksteg. Ett exempel:

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "c:\\<path>\\device-ca.cert.pem"
        device_ca_pk: "c:\\<path>\\device-ca.key.pem"
        trusted_ca_certs: "c:\\<path>\\root-ca.root.ca.cert.pem"
      ```

   * Linux:

      ```yaml
      certificates:
        device_ca_cert: "<path>/device-ca.cert.pem"
        device_ca_pk: "<path>/device-ca.key.pem"
        trusted_ca_certs: "<path>/root-ca.root.ca.cert.pem"
      ```

1. På Linux-enheter kontrollerar du att användaren **iotedge** har läsbehörighet för katalogen som innehåller certifikaten.

1. Om du har använt andra certifikat för IoT Edge på enheten tidigare tar du bort filerna i följande två kataloger innan du startar eller startar om IoT Edge:

   * Windows: `C:\ProgramData\iotedge\hsm\certs` och`C:\ProgramData\iotedge\hsm\cert_keys`

   * Linux: `/var/lib/iotedge/hsm/certs` och`/var/lib/iotedge/hsm/cert_keys`

## <a name="customize-certificate-lifetime"></a>Anpassa certifikatets livstid

IoT Edge genererar automatiskt certifikat på enheten i flera fall, inklusive:

* Om du inte tillhandahåller dina egna produktionscertifikat när du installerar och etablerar IoT Edge genererar säkerhetshanteraren IoT Edge automatiskt ett **enhetscertifikatutfärdarcertifikat**. Det här självsignerade certifikatet är endast avsett för utvecklings- och testscenarier, inte produktion. Certifikatet upphör att gälla efter 90 dagar.
* Säkerhetshanteraren för IoT Edge genererar också ett **certifikat för arbetsbelastningscertifikatutfärdare** som signerats av enhetscertifikatutfärdarens certifikat

Mer information om funktionen för de olika certifikaten på en IoT Edge-enhet finns i [Förstå hur Azure IoT Edge använder certifikat](iot-edge-certs.md).

För dessa två automatiskt genererade certifikat har du möjlighet att ange **auto_generated_ca_lifetime_days-flaggan** i config.yaml för att konfigurera antalet dagar för certifikatens livstid.

>[!NOTE]
>Det finns ett tredje automatiskt genererat certifikat som IoT Edge-säkerhetshanteraren skapar, **IoT Edge-hubbservercertifikatet**. Certifikatet har alltid en 90-dag, men förnyas automatiskt innan det löper ut. Värdet **auto_generated_ca_lifetime_days** påverkar inte certifikatet.

Om du vill konfigurera certifikatets förfallodatum till något annat än standard 90 dagar lägger du till värdet i dagar i **certifikatavsnittet** i filen config.yaml.

```yaml
certificates:
  device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
  device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
  trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
  auto_generated_ca_lifetime_days: <value>
```

Om du angav dina egna enhetscertifikatutfärdare gäller det här värdet fortfarande för certifikatutfärdaren för arbetsbelastningen, förutsatt att livstidsvärdet du anger är kortare än livslängden för enhetscertifikatutfärdarens certifikat.

NÃ¤r du har angett flaggan i filen config.yaml:

1. Ta bort innehållet `hsm` i mappen.

   Windows: `C:\ProgramData\iotedge\hsm\certs and C:\ProgramData\iotedge\hsm\cert_keys` Linux:`/var/lib/iotedge/hsm/certs and /var/lib/iotedge/hsm/cert_keys`

1. Starta om IoT Edge-tjänsten.

   Windows:

   ```powershell
   Restart-Service iotedge
   ```

   Linux:

   ```bash
   sudo systemctl restart iotedge
   ```

1. Bekräfta livstidsinställningen.

   Windows:

   ```powershell
   iotedge check --verbose
   ```

   Linux:

   ```bash
   sudo iotedge check --verbose
   ```

   Kontrollera utdata från **produktionsberedskapen: certifikatkontrollen,** som visar antalet dagar tills de automatiskt genererade enhetscertifikatutfärdaren upphör att gälla.

## <a name="next-steps"></a>Nästa steg

Installera certifikat på en IoT Edge-enhet är ett nödvändigt steg innan du distribuerar lösningen i produktion. Läs mer om hur [du förbereder för att distribuera din IoT Edge-lösning i produktion](production-checklist.md).
