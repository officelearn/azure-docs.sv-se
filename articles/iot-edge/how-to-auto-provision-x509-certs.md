---
title: Etablera enheter automatiskt med DPS med X.509-certifikat – Azure IoT Edge | Microsoft-dokument
description: Använda X.509-certifikat för att testa automatisk enhetsetablering för Azure IoT Edge med enhetsetableringstjänst
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: kevindaw
ms.date: 03/06/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 48c8179af4a4b69924fb943ac98918b48d3a2008
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537367"
---
# <a name="create-and-provision-an-iot-edge-device-using-x509-certificates"></a>Skapa och etablera en IoT Edge-enhet med X.509-certifikat

Med [Azure IoT Hub Device Provisioning Service (DPS)](../iot-dps/index.yml)kan du automatiskt etablera IoT Edge-enheter med X.509-certifikat. Om du inte känner till processen för automatisk etablering läser du [begreppen för automatisk etablering](../iot-dps/concepts-auto-provisioning.md) innan du fortsätter.

I den här artikeln visas hur du skapar en registrering av enhetsetableringstjänsten med X.509-certifikat på en IoT Edge-enhet med följande steg:

* Generera certifikat och nycklar.
* Skapa antingen en enskild registrering för en enhet eller en gruppregistrering för en uppsättning enheter.
* Installera IoT Edge-körningen och registrera enheten med IoT Hub.

Att använda X.509-certifikat som en attestation-mekanism är ett utmärkt sätt att skala produktionen och förenkla enhetsetablering. X.509-certifikat är vanligtvis ordnade i en certifikatkedja. Från och med ett självsignerat eller betrott rotcertifikat signerar varje certifikat i kedjan nästa lägre certifikat. Det här mönstret skapar en delegerad förtroendekedja från rotcertifikatet nedåt genom varje mellanliggande certifikat till det slutliga "leaf"-certifikatet som är installerat på en enhet.

## <a name="prerequisites"></a>Krav

* En aktiv IoT Hub.
* En fysisk eller virtuell enhet som ska vara IoT Edge-enheten.
* Den senaste versionen av [Git](https://git-scm.com/download/) installerad.
* En instans av IoT Hub Device Provisioning Service i Azure, kopplad till din IoT-hubb.
  * Om du inte har en instans för enhetsetablering följer du instruktionerna i [Konfigurera IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).
  * När du har igång enhetsetableringstjänsten kopierar du värdet för **ID-scopet** från översiktssidan. Du använder det här värdet när du konfigurerar IoT Edge-körningen.

## <a name="generate-device-identity-certificates"></a>Generera enhetsidentitetscertifikat

Enhetsidentitetscertifikatet är ett lövcertifikat som ansluter via en certifikatkedja med förtroende till det översta X.509-certifikatutfärdarens certifikat. Enhetsidentitetscertifikatet måste ha det vanliga namnet (CN) inställt på det enhets-ID som du vill att enheten ska ha i IoT-hubben.

Enhetsidentitetscertifikat används endast för att etablera IoT Edge-enheten och autentisera enheten med Azure IoT Hub. De signerar inte certifikat, till skillnad från de CA-certifikat som IoT Edge-enheten presenterar för moduler eller lövenheter för verifiering. Mer information finns i [Azure IoT Edge-certifikatanvändningsdetaljinformation](iot-edge-certs.md).

När du har skapat enhetsidentitetscertifikatet bör du ha två filer: en .cer- eller .pem-fil som innehåller den offentliga delen av certifikatet och en .cer- eller .pem-fil med certifikatets privata nyckel. Om du planerar att använda gruppregistrering i DPS behöver du också den offentliga delen av ett mellanliggande certifikat eller rotcertifikatutfärdarcertifikat i samma certifikatkedja.

### <a name="use-test-certificates"></a>Använda testcertifikat

Om du inte har en certifikatutfärdare tillgänglig för att skapa nya identitetscert och vill prova det här scenariot innehåller Azure IoT Edge git-databasen skript som du kan använda för att generera testcertifikat. Dessa certifikat är endast avsedda för utvecklingstester och får inte användas i produktionen.

Om du vill skapa testcertifikat följer du stegen i [Skapa democertifikat för att testa IoT Edge-enhetsfunktioner](how-to-create-test-certificates.md). Fyll i de två obligatoriska avsnitten för att konfigurera certifikatgenereringsskripten och skapa ett rotcertifikatutfärdarcertifikat. Följ sedan stegen för att skapa ett enhetsidentitetscertifikat. När du är klar bör du ha följande certifikatkedja och nyckelpar:

Linux:

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

Windows:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

Du behöver båda dessa certifikat på IoT Edge-enheten. Om du ska använda enskilda registreringar i DPS kommer du att ladda upp .cert.pem-filen. Om du ska använda gruppregistrering i DPS behöver du också ett mellanliggande certifikat eller ett rotcertifikatutfärdarcertifikat i samma certifikatkedja för förtroende för att överföra. Om du använder democert använder du `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` certifikatet för gruppregistrering.

## <a name="create-a-dps-individual-enrollment"></a>Skapa en enskild DPS-registrering

Använd dina genererade certifikat och nycklar för att skapa en enskild registrering i DPS för en enda IoT Edge-enhet. Enskilda registreringar tar den offentliga delen av en enhets identitetscertifikat och matchar det med certifikatet på enheten.

Om du vill etablera flera IoT Edge-enheter följer du stegen i nästa avsnitt, [Skapa en DPS-gruppregistrering](#create-a-dps-group-enrollment).

När du skapar en registrering i DPS har du möjlighet att deklarera ett **inledande enhets twin-tillstånd**. I enhetstvillingen kan du ange taggar för att gruppera enheter efter alla mått som du behöver i din lösning, till exempel region, miljö, plats eller enhetstyp. Dessa taggar används för att skapa [automatiska distributioner](how-to-deploy-monitor.md).

Mer information om registreringar i tjänsten För enhetsetablering finns i [Så här hanterar du enhetsregistreringar](../iot-dps/how-to-manage-enrollments.md).

1. I [Azure-portalen](https://portal.azure.com)navigerar du till din instans av IoT Hub Device Provisioning Service.

1. Under **Inställningar**väljer du **Hantera registreringar**.

1. Välj **Lägg till individuell registrering** och utför sedan följande steg för att konfigurera registreringen:  

   * **Mekanism:** Välj **X.509**.

   * **Primär certifikat .pem eller .cer-fil:** Ladda upp den offentliga filen från enhetsidentitetscertifikatet. Om du använde skripten för att generera ett testcertifikat väljer du följande fil:

      `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`

   * **IoT Hub Device ID:** Ange ett ID för din enhet om du vill. Du kan använda enhets-ID:er för att rikta in dig på en enskild enhet för moduldistribution. Om du inte anger något enhets-ID används det vanliga namnet (CN) i X.509-certifikatet.

   * **IoT Edge-enhet**: Välj **True** om du vill deklarera att registreringen är för en IoT Edge-enhet.

   * **Välj de IoT-hubbar som den här enheten kan tilldelas:** Välj den länkade IoT-hubben som du vill ansluta enheten till. Du kan välja flera hubbar och enheten tilldelas en av dem enligt den valda fördelningsprincipen.

   * **Inledande enhets twin-tillstånd:** Lägg till ett taggvärde som ska läggas till i enhetstvillingen om du vill. Du kan använda taggar för att rikta grupper av enheter för automatisk distribution. Ett exempel:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. Välj **Spara**.

Nu när det finns en registrering för den här enheten kan IoT Edge-körningen automatiskt etablera enheten under installationen. Fortsätt till avsnittet [Installera IoT Edge-körningen](#install-the-iot-edge-runtime) för att konfigurera IoT Edge-enheten.

## <a name="create-a-dps-group-enrollment"></a>Skapa en DPS-gruppregistrering

Använd dina genererade certifikat och nycklar för att skapa en gruppregistrering i DPS för flera IoT Edge-enheter. Gruppregistreringar använder ett mellanliggande certifikat eller rotcertifikatutfärdarcertifikat från certifikatkedjan för förtroende som används för att generera de enskilda enhetsidentitetscertifikaten.

Om du vill etablera en enda IoT Edge-enhet i stället följer du stegen i föregående avsnitt, [Skapa en enskild DPS-registrering](#create-a-dps-individual-enrollment).

När du skapar en registrering i DPS har du möjlighet att deklarera ett **inledande enhets twin-tillstånd**. I enhetstvillingen kan du ange taggar för att gruppera enheter efter alla mått som du behöver i din lösning, till exempel region, miljö, plats eller enhetstyp. Dessa taggar används för att skapa [automatiska distributioner](how-to-deploy-monitor.md).

### <a name="verify-your-root-certificate"></a>Verifiera rotcertifikatet

När du skapar en registreringsgrupp kan du välja att använda ett verifierat certifikat. Du kan verifiera ett certifikat med DPS genom att bevisa att du har ägarskap för rotcertifikatet. Mer information finns i [Så här gör du bevisbeklädnad för X.509 CA-certifikat](../iot-dps/how-to-verify-certificates.md).

1. I [Azure-portalen](https://portal.azure.com)navigerar du till din instans av IoT Hub Device Provisioning Service.

1. Välj **Certifikat** på menyn till vänster.

1. Välj **Lägg till** om du vill lägga till ett nytt certifikat.

1. Ange ett eget namn för certifikatet och bläddra sedan till filfilen .cer eller .pem som representerar den offentliga delen av X.509-certifikatet.

   Om du använder democertifikaten laddar `<wrkdir>/certs/azure-iot-test-only.root.ca.cert.pem` du upp certifikatet.

1. Välj **Spara**.

1. Ditt certifikat ska nu anges på sidan **Certifikat.** Markera den för att öppna certifikatinformationen.

1. Välj **Generera verifieringskod** och kopiera den genererade koden.

1. Oavsett om du tog med ditt eget certifikatutfärdare eller använder democertifikaten kan du använda verifieringsverktyget som finns i IoT Edge-databasen för att verifiera bevis på innehav. Verifieringsverktyget använder ca-certifikatet för att signera ett nytt certifikat som har den angivna verifieringskoden som ämnesnamn.

   * Windows:

     ```powershell
     New-CACertsVerificationCert "<verification code>"
     ```

   * Linux:

     ```bash
     ./certGen.sh create_verification_certificate <verification code>
     ```

1. Ladda upp det nyligen genererade verifieringscertifikatet på samma informationssida för certifikat i Azure-portalen.

1. Välj **Verify** (Verifiera).

### <a name="create-enrollment-group"></a>Skapa registreringsgrupp

Mer information om registreringar i tjänsten För enhetsetablering finns i [Så här hanterar du enhetsregistreringar](../iot-dps/how-to-manage-enrollments.md).

1. I [Azure-portalen](https://portal.azure.com)navigerar du till din instans av IoT Hub Device Provisioning Service.

1. Under **Inställningar**väljer du **Hantera registreringar**.

1. Välj **Lägg till registreringsgrupp** och slutför sedan följande steg för att konfigurera registreringen:

   * **Gruppnamn**: Ange ett minnesvärt namn för den här gruppregistreringen.

   * **Attestation Typ**: Välj **certifikat**.

   * **IoT Edge-enhet**: Välj **True**. För en gruppregistrering måste alla enheter vara IoT Edge-enheter eller så kan ingen av dem vara det.

   * **Certifikattyp:** Välj **CERTIFIKATUTfärdarcertifikat** om du har ett verifierat CERTIFIKAT som lagras med DPS eller **Mellanliggande certifikat** om du vill ladda upp en ny fil för just den här registreringen.

   * **Primärt certifikat**: Om du väljer certifikatutfärdarcertifikat i det sista avsnittet väljer du ditt certifikat i listrutan. Om du väljer mellanliggande certifikat laddar du upp den offentliga filen från ett certifikatutfärdarcertifikat i certifikatkedjan som användes för att generera enhetsidentitetscertifikaten.

   * **Välj de IoT-hubbar som den här enheten kan tilldelas:** Välj den länkade IoT-hubben som du vill ansluta enheten till. Du kan välja flera hubbar och enheten tilldelas en av dem enligt den valda fördelningsprincipen.

   * **Inledande enhets twin-tillstånd:** Lägg till ett taggvärde som ska läggas till i enhetstvillingen om du vill. Du kan använda taggar för att rikta grupper av enheter för automatisk distribution. Ett exempel:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. Välj **Spara**.

Nu när det finns en registrering för den här enheten kan IoT Edge-körningen automatiskt etablera enheten under installationen. Fortsätt till nästa avsnitt för att konfigurera IoT Edge-enheten.

## <a name="install-the-iot-edge-runtime"></a>Installera IoT Edge-körningen

IoT Edge-körningen distribueras på alla IoT Edge-enheter. Dess komponenter körs i behållare och gör att du kan distribuera ytterligare behållare till enheten så att du kan köra kod vid kanten.

X.509-etablering med DPS stöds endast i IoT Edge version 1.0.9 eller nyare.

Du behöver följande information när du etablerar enheten:

* **DPS-ID-scopevärdet.** Du kan hämta det här värdet från översiktssidan för din DPS-instans i Azure-portalen.
* Enhetsidentitetscertifikatfilen på enheten.
* Enhetsidentitetsnyckelfilen på enheten.
* Ett valfritt registrerings-ID (hämtat från det gemensamma namnet i enhetens identitetscertifikat om det inte anges).

### <a name="linux-device"></a>Linux-enhet

Använd följande länk för att installera Azure IoT Edge-körningen på din enhet med hjälp av de kommandon som är lämpliga för enhetens arkitektur. När du kommer till avsnittet om att konfigurera säkerhetsdemonen konfigurerar du IoT Edge-körningen för X.509 automatisk, inte manuell, etablering. Du bör ha all information och alla certifikatfiler som du behöver när du har slutfört föregående avsnitt i den här artikeln.

[Installera Azure IoT Edge-körningen på Linux](how-to-install-iot-edge-linux.md)

När du lägger till X.509-certifikatet och nyckelinformationen i filen config.yaml ska sökvägarna anges som fil-URI:er. Ett exempel:

* `file:///<path>/identity_certificate.pem`
* `file:///<path>/identity_key.pem`

Avsnittet i konfigurationsfilen för automatisk etablering av X.509 ser ut så här:

```yaml
# DPS X.509 provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "x509"
#   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
    identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
    identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
```

Ersätt platshållarvärdena `identity_cert` `identity_pk` för `scope_id`, , med scope-ID:et från DPS-instansen och URI:erna till cert- och nyckelfilplatserna på enheten. Ange `registration_id` en för enheten om du vill, eller lämna den här raden kommenterade ut för att registrera enheten med CN-namnet på identitetscertifikatet.

Starta alltid om säkerhetsdemonen när filen config.yaml har uppdaterats.

```bash
sudo systemctl restart iotedge
```

### <a name="windows-device"></a>Windows-enhet

Installera IoT Edge-körningen på den enhet som du genererade identitetscertifikatet och identitetsnyckeln för. Du konfigurerar IoT Edge-körningen för automatisk, inte manuell etablering.

Mer detaljerad information om hur du installerar IoT Edge i Windows, inklusive förutsättningar och instruktioner för uppgifter som att hantera behållare och uppdatera IoT Edge, finns i [Installera Azure IoT Edge-körningen i Windows](how-to-install-iot-edge-windows.md).

1. Öppna ett PowerShell-fönster i administratörsläge. Var noga med att använda en AMD64-session med PowerShell när du installerar IoT Edge, inte PowerShell (x86).

1. Kommandot **Deploy-IoTEdge** kontrollerar att din Windows-dator finns i en version som stöds, aktiverar behållarfunktionen och hämtar sedan den moby-körningen och IoT Edge-körningen. Kommandot använder som standard Windows-behållare.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. Nu kan IoT Core-enheter startas om automatiskt. Andra Windows 10- eller Windows Server-enheter kan uppmana dig att starta om. Starta om enheten nu om så är fallet. När enheten är klar kör du PowerShell som administratör igen.

1. Kommandot **Initialize-IoTEdge** konfigurerar IoT Edge-körningen på datorn. Kommandot är som standard manuell etablering om `-Dps` du inte använder flaggan för att använda automatisk etablering.

   Ersätt platshållarvärdena `{identity cert path}`för `{identity key path}` `{scope_id}`, och med lämpliga värden från DPS-instansen och filsökvägarna på enheten. Om du vill ange registrerings-ID, inkludera `-RegistrationId {registration_id}` också, ersätta platshållaren efter behov.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -X509IdentityCertificate {identity cert path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >Filen config.yaml lagrar certifikatet och nyckelinformationen som fil-URI:er. Kommandot Initialize-IoTEdge hanterar dock det här formateringssteget åt dig, så att du kan ange den absoluta sökvägen till certifikatet och nyckelfilerna på enheten.

## <a name="verify-successful-installation"></a>Verifiera lyckad installation

Om körningen har startat kan du gå in i din IoT Hub och börja distribuera IoT Edge-moduler till enheten.

Du kan kontrollera att den enskilda registreringen som du skapade i enhetsetableringstjänsten har använts. Navigera till instansen för enhetsetableringstjänst i Azure-portalen. Öppna registreringsinformationen för den enskilda registreringen som du skapade. Observera att status för registreringen **har tilldelats** och att enhets-ID:t visas.

Använd följande kommandon på enheten för att kontrollera att körningen har installerats och startats.

### <a name="linux-device"></a>Linux-enhet

Kontrollera status för IoT Edge-tjänsten.

```cmd/sh
systemctl status iotedge
```

Undersök serviceloggar.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Lista löpmoduler.

```cmd/sh
iotedge list
```

### <a name="windows-device"></a>Windows-enhet

Kontrollera status för IoT Edge-tjänsten.

```powershell
Get-Service iotedge
```

Undersök serviceloggar.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Lista löpmoduler.

```powershell
iotedge list
```

## <a name="next-steps"></a>Nästa steg

Med registreringsprocessen för enhetsetableringstjänsten kan du ange enhets-ID- och enhetstvillingtaggarna samtidigt som du etablerar den nya enheten. Du kan använda dessa värden för att rikta in dig på enskilda enheter eller grupper av enheter med hjälp av automatisk enhetshantering. Lär dig hur du [distribuerar och övervakar IoT Edge-moduler i stor skala med Hjälp av Azure-portalen](how-to-deploy-monitor.md) eller [med Azure CLI](how-to-deploy-monitor-cli.md).
