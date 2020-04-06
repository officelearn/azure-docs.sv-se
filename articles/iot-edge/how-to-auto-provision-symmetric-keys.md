---
title: Etablera enhet med symmetrisk nyckel attestation - Azure IoT Edge
description: Använd symmetrisk nyckeltrösstation för att testa automatisk enhetsetablering för Azure IoT Edge med enhetsetableringstjänst
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 4/3/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 139a2cafe137d000b991cbad8b8567e005ffc728
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668661"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>Skapa och etablera en IoT Edge-enhet med symmetrisk nyckeltysstik

Azure IoT Edge-enheter kan etableras automatiskt med hjälp av [enhetsetableringstjänsten](../iot-dps/index.yml) precis som enheter som inte är edge-aktiverade. Om du inte känner till processen för automatisk etablering läser du [begreppen för automatisk etablering](../iot-dps/concepts-auto-provisioning.md) innan du fortsätter.

I den här artikeln visas hur du skapar en individuell registrering av enhetsetableringstjänsten med symmetrisk nyckeltyssto på en IoT Edge-enhet med följande steg:

* Skapa en instans av DPS (IoT Hub Device Provisioning Service).
* Skapa en enskild registrering för enheten.
* Installera IoT Edge-körningen och anslut till IoT Hub.

Symmetrisk nyckeltysstation är en enkel metod för att autentisera en enhet med en enhetsetableringstjänstinstans. Den här attestation-metoden representerar en "Hello world"-upplevelse för utvecklare som är nya för enhetsetablering eller inte har strikta säkerhetskrav. Enhetsintyg med hjälp av ett [TPM-](../iot-dps/concepts-tpm-attestation.md) eller [X.509-certifikat](../iot-dps/concepts-security.md#x509-certificates) är säkrare och bör användas för strängare säkerhetskrav.

## <a name="prerequisites"></a>Krav

* En aktiv IoT-hubb
* En fysisk eller virtuell enhet

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Konfigurera etableringstjänsten för IoT Hub Device Provisioning

Skapa en ny instans av IoT Hub Device Provisioning Service i Azure och länka den till din IoT-hubb. Du kan följa instruktionerna i [Konfigurera IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).

När du har igång enhetsetableringstjänsten kopierar du värdet för **ID-scopet** från översiktssidan. Du använder det här värdet när du konfigurerar IoT Edge-körningen.

## <a name="choose-a-unique-registration-id-for-the-device"></a>Välj ett unikt registrerings-ID för enheten

Ett unikt registrerings-ID måste definieras för att identifiera varje enhet. Du kan använda MAC-adressen, serienumret eller någon unik information från enheten.

I det här exemplet använder vi en kombination av en MAC-adress och `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6`serienummer som utgör följande sträng för ett registrerings-ID: .

Skapa ett unikt registrerings-ID för din enhet. Giltiga tecken är gemener alfanumeriska och streck ('-').

## <a name="create-a-dps-enrollment"></a>Skapa en DPS-registrering

Använd enhetens registrerings-ID för att skapa en enskild registrering i DPS.

När du skapar en registrering i DPS har du möjlighet att deklarera ett **inledande enhets twin-tillstånd**. I enhetstvillingen kan du ange taggar för att gruppera enheter efter alla mått som du behöver i din lösning, till exempel region, miljö, plats eller enhetstyp. Dessa taggar används för att skapa [automatiska distributioner](how-to-deploy-monitor.md).

> [!TIP]
> Gruppregistreringar är också möjliga när du använder symmetrisk nyckeltysststation och involverar samma beslut som enskilda registreringar.

1. I [Azure-portalen](https://portal.azure.com)navigerar du till din instans av IoT Hub Device Provisioning Service.

1. Under **Inställningar**väljer du **Hantera registreringar**.

1. Välj **Lägg till individuell registrering** och utför sedan följande steg för att konfigurera registreringen:  

   1. För **Mekanism**väljer du **Symmetrisk nyckel**.

   1. Markera kryssrutan **Generera tangenter automatiskt.**

   1. Ange **registrerings-ID** som du har skapat för enheten.

   1. Ange ett **IoT Hub Device ID** för din enhet om du vill. Du kan använda enhets-ID:er för att rikta in dig på en enskild enhet för moduldistribution. Om du inte anger ett enhets-ID används registrerings-ID: t.

   1. Välj **Sant** om du vill deklarera att registreringen är för en IoT Edge-enhet. För en gruppregistrering måste alla enheter vara IoT Edge-enheter eller så kan ingen av dem vara det.

   > [!TIP]
   > I Azure CLI kan du skapa en [registrering](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment) eller en [registreringsgrupp](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment-group) och använda den **kantaktiverade** flaggan för att ange att en enhet, eller grupp av enheter, är en IoT Edge-enhet.

   1. Acceptera standardvärdet från enhetsetableringstjänstens allokeringsprincip för **hur du vill tilldela enheter till hubbar** eller välja ett annat värde som är specifikt för den här registreringen.

   1. Välj den länkade **IoT-hubben** som du vill ansluta enheten till. Du kan välja flera hubbar och enheten tilldelas en av dem enligt den valda fördelningsprincipen.

   1. Välj **hur du vill att enhetsdata ska hanteras vid återetablering** när enheter begär etablering efter första gången.

   1. Lägg till ett taggvärde i det **inledande enhets twin-tillståndet** om du vill. Du kan använda taggar för att rikta grupper av enheter för moduldistribution. Ett exempel:

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

   1. Kontrollera **att aktivera posten** är inställd på **Aktivera**.

   1. Välj **Spara**.

Nu när det finns en registrering för den här enheten kan IoT Edge-körningen automatiskt etablera enheten under installationen. Var noga med att kopiera registreringens **primärnyckelvärde** som ska användas när du installerar IoT Edge-körningen, eller om du ska skapa enhetsnycklar som ska användas med en gruppregistrering.

## <a name="derive-a-device-key"></a>Härleda en enhetsnyckel

> [!NOTE]
> Det här avsnittet krävs endast om du använder en gruppregistrering.

Varje enhet använder sin härledda enhetsnyckel med ditt unika registrerings-ID för att utföra symmetrisk nyckeltysst med registreringen under etableringen. Om du vill generera enhetsnyckeln använder du nyckeln som du kopierade från DPS-registreringen för att beräkna ett [HMAC-SHA256-format](https://wikipedia.org/wiki/HMAC) för det unika registrerings-ID:t för enheten och konvertera resultatet till Base64-format.

Ta inte med registreringens primära eller sekundära nyckel i enhetskoden.

### <a name="linux-workstations"></a>Linux-arbetsstationer

Om du använder en Linux-arbetsstation kan du använda openssl för att generera din härledda enhetsnyckel som visas i följande exempel.

Ersätt värdet **för KEY** med **primärnyckeln** som du noterade tidigare.

Ersätt värdet **för REG_ID** med enhetens registrerings-ID.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

### <a name="windows-based-workstations"></a>Windows-baserade arbetsstationer

Om du använder en Windows-baserad arbetsstation kan du använda PowerShell för att generera den härledda enhetsnyckeln enligt följande exempel.

Ersätt värdet **för KEY** med **primärnyckeln** som du noterade tidigare.

Ersätt värdet **för REG_ID** med enhetens registrerings-ID.

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

## <a name="install-the-iot-edge-runtime"></a>Installera IoT Edge-körningen

IoT Edge-körningen distribueras på alla IoT Edge-enheter. Dess komponenter körs i behållare och gör att du kan distribuera ytterligare behållare till enheten så att du kan köra kod vid kanten.

Du behöver följande information när du etablerar enheten:

* **Dps-ID-scopevärdet**
* **Enhetsregistrerings-ID:et** som du skapade
* Den **primära nyckeln** som du kopierade från DPS-registreringen

> [!TIP]
> För gruppregistreringar behöver du varje enhets [härledda nyckel](#derive-a-device-key) i stället för DPS-registreringsnyckeln.

### <a name="linux-device"></a>Linux-enhet

Följ instruktionerna för enhetens arkitektur. Se till att konfigurera IoT Edge-körningen för automatisk, inte manuell etablering.

[Installera Azure IoT Edge-körningen på Linux](how-to-install-iot-edge-linux.md)

Avsnittet i konfigurationsfilen för symmetrisk nyckeletablering ser ut så här:

```yaml
# DPS symmetric key provisioning configuration
provisioning:
   source: "dps"
   global_endpoint: "https://global.azure-devices-provisioning.net"
   scope_id: "<SCOPE_ID>"
   attestation:
      method: "symmetric_key"
      registration_id: "<REGISTRATION_ID>"
      symmetric_key: "<SYMMETRIC_KEY>"
```

Ersätt platshållarvärdena `<REGISTRATION_ID>`för `<SYMMETRIC_KEY>` `<SCOPE_ID>`, och med de data som du samlade in tidigare. Kontrollera att **etableringen:** raden inte har något föregående blanktecken och att kapslade objekt är indragna med två blanksteg.

### <a name="windows-device"></a>Windows-enhet

Installera IoT Edge-körningen på den enhet som du har genererat en härledd enhetsnyckel för. Du konfigurerar IoT Edge-körningen för automatisk, inte manuell etablering.

Mer detaljerad information om hur du installerar IoT Edge i Windows, inklusive förutsättningar och instruktioner för uppgifter som att hantera behållare och uppdatera IoT Edge, finns i [Installera Azure IoT Edge-körningen i Windows](how-to-install-iot-edge-windows.md).

1. Öppna ett PowerShell-fönster i administratörsläge. Var noga med att använda en AMD64-session med PowerShell när du installerar IoT Edge, inte PowerShell (x86).

1. Kommandot **Deploy-IoTEdge** kontrollerar att din Windows-dator finns i en version som stöds, aktiverar behållarfunktionen och hämtar sedan den moby-körningen och IoT Edge-körningen. Kommandot använder som standard Windows-behållare.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. Nu kan IoT Core-enheter startas om automatiskt. Andra Windows 10- eller Windows Server-enheter kan uppmana dig att starta om. Starta om enheten nu om så är fallet. När enheten är klar kör du PowerShell som administratör igen.

1. Kommandot **Initialize-IoTEdge** konfigurerar IoT Edge-körningen på datorn. Kommandot är som standard manuell etablering med Windows-behållare om du inte använder `-Dps` flaggan för att använda automatisk etablering.

   Ersätt platshållarvärdena `{registration_id}`för `{symmetric_key}` `{scope_id}`, och med de data som du samlade in tidigare.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID} -SymmetricKey {symmetric key}
   ```

## <a name="verify-successful-installation"></a>Verifiera lyckad installation

Om körningen har startat kan du gå in i din IoT Hub och börja distribuera IoT Edge-moduler till enheten. Använd följande kommandon på enheten för att kontrollera att körningen har installerats och startats.

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

Du kan kontrollera att den enskilda registreringen som du skapade i enhetsetableringstjänsten har använts. Navigera till instansen för enhetsetableringstjänst i Azure-portalen. Öppna registreringsinformationen för den enskilda registreringen som du skapade. Observera att status för registreringen **har tilldelats** och att enhets-ID:t visas.

## <a name="next-steps"></a>Nästa steg

Med registreringsprocessen för enhetsetableringstjänsten kan du ange enhets-ID- och enhetstvillingtaggarna samtidigt som du etablerar den nya enheten. Du kan använda dessa värden för att rikta in dig på enskilda enheter eller grupper av enheter med hjälp av automatisk enhetshantering. Lär dig hur du [distribuerar och övervakar IoT Edge-moduler i stor skala med Hjälp av Azure-portalen](how-to-deploy-monitor.md) eller [med Azure CLI](how-to-deploy-monitor-cli.md).
