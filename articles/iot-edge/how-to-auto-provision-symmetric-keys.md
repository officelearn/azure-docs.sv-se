---
title: Etablera enheter med DPS med hjälp av symmetrisk nyckel attestering-Azure IoT Edge | Microsoft Docs
description: Använd symmetrisk nyckel attestering för att testa automatisk enhets etablering för Azure IoT Edge med enhets etablerings tjänsten
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 07/10/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5a7e7fa011c0287d5e97ad7a8cd2e3ba77f298dd
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299853"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>Skapa och etablera en IoT Edge enhet med hjälp av symmetrisk nyckel attestering

Azure IoT Edge-enheter kan vara autoprovisioned med hjälp av den [Device Provisioning-tjänsten](../iot-dps/index.yml) precis som enheter som inte är Microsoft edge-aktiverade. Om du är bekant med processen för autoprovisioning kan du granska den [autoprovisioning begrepp](../iot-dps/concepts-auto-provisioning.md) innan du fortsätter.

Den här artikeln visar hur du skapar en individuell registrering av enhets etablerings tjänsten med hjälp av symmetrisk nyckel attestering på en IoT Edge enhet med följande steg:

* Skapa en instans av IoT Hub enheten Provisioning Service (DPS).
* Skapa en enskild registrering för enheten.
* Installera IoT Edge Runtime och Anslut till IoT Hub.

Symmetrisk nyckel attestering är en enkel metod för att autentisera en enhet med en enhets etablerings tjänst instans. Den här attesterings metoden representerar en "Hello World"-upplevelse för utvecklare som är nya för enhets etablering eller som inte har strikta säkerhets krav. Enhets attestering med en [TPM](../iot-dps/concepts-tpm-attestation.md) är säkrare och bör användas för mer långtgående säkerhets krav.

## <a name="prerequisites"></a>Förutsättningar

* En aktiv IoT Hub
* En fysisk eller virtuell enhet

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Konfigurera IoT Hub Device Provisioning-tjänsten

Skapa en ny instans av IoT Hub Device Provisioning-tjänsten i Azure och länka det till din IoT hub. Du kan följa anvisningarna i [konfigurera IoT Hub-DPS](../iot-dps/quick-setup-auto-provision.md).

När du har Device Provisioning-tjänsten körs, kopierar du värdet för **ID-omfång** från översiktssidan. Du kan använda det här värdet när du konfigurerar IoT Edge-körningen.

## <a name="choose-a-unique-registration-id-for-the-device"></a>Välj ett unikt registrerings-ID för enheten

Ett unikt registrerings-ID måste definieras för att identifiera varje enhet. Du kan använda MAC-adressen, serie numret eller någon unik information från enheten.

I det här exemplet använder vi en kombination av en MAC-adress och ett serie nummer som utgör följande sträng för ett registrerings-ID.

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Skapa ett unikt registrerings-ID för din enhet. Giltiga tecken är alfanumeriska bokstäver och bindestreck (-).

## <a name="create-a-dps-enrollment"></a>Skapa en DPS-registrering

Använd enhetens registrerings-ID för att skapa en enskild registrering i DPS.

När du skapar en registrering i DPS har möjlighet att deklarera en **starttillstånd för Enhetstvilling**. I enhetstvillingen, kan du ställa in etiketter att gruppera enheter efter valfritt mått som du behöver i din lösning som region, miljö, plats eller enhet. Dessa taggar som används för att skapa [automatiska distributioner](how-to-deploy-monitor.md).

> [!TIP]
> Grupp registreringar är också möjliga när du använder symmetrisk nyckel attestering och inbegriper samma beslut som enskilda registreringar.

1. I [Azure Portal](https://portal.azure.com)navigerar du till din instans av IoT Hub Device Provisioning service.

1. Under **inställningar**väljer **hantera registreringar**.

1. Välj **Lägg till enskild registrering** utför följande steg om du vill konfigurera registreringen:  

   1. För **mekanism**väljer du **symmetrisk nyckel**.

   1. Markera kryss rutan **generera nycklar automatiskt** .

   1. Ange det **registrerings-ID** som du har skapat för din enhet.

   1. Ange ett **IoT Hub enhets-ID** för din enhet om du vill. Du kan använda enhets-ID för att rikta en enskild enhet för distribution av principmodul. Om du inte anger något enhets-ID används registrerings-ID: t.

   1. Välj **Sant** för att deklarera att registreringen är för en IoT Edge enhet. För en grupp registrering måste alla enheter vara IoT Edge enheter eller så kan ingen av dem vara.

   1. Acceptera standardvärdet från enhets etablerings tjänstens Allokeringsregel för **hur du vill tilldela enheter till hubbar** eller välja ett annat värde som är särskilt för den här registreringen.

   1. Välj den länkade **IoT Hub** som du vill ansluta enheten till. Du kan välja flera hubbar och enheten tilldelas en av dem enligt den valda allokeringsregeln.

   1. Välj **hur du vill att enhets data ska hanteras vid ny etablering** när enheter begär etablering efter första gången.

   1. Lägg till ett visst Taggvärde till den **starttillstånd för Enhetstvilling** om du vill ha. Du kan använda taggar för målgrupper för enheter för distribution av principmodul. Exempel:

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

   1. Se till att **Aktivera post** är **aktive**rad.

   1. Välj **Spara**.

Nu när en registrering finns för den här enheten kan IoT Edge runtime automatiskt etablera enheten under installationen. Se till att kopiera din registrerings **primär nyckel** värde som ska användas när du installerar IoT Edge runtime, eller om du ska skapa enhets nycklar för användning med en grupp registrering.

## <a name="derive-a-device-key"></a>Härled en enhets nyckel

> [!NOTE]
> Det här avsnittet krävs endast om du använder en grupp registrering.

Varje enhet använder den härledda enhets nyckeln med ditt unika registrerings-ID för att utföra symmetrisk nyckel attestering med registreringen under etableringen. Om du vill generera enhets nyckeln använder du nyckeln som du kopierade från din DPS-registrering för att beräkna en [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) av det unika registrerings-ID: t för enheten och konvertera resultatet till base64-format.

Ta inte med din registrerings primära eller sekundära nyckel i enhets koden.

### <a name="linux-workstations"></a>Linux-arbetsstationer

Om du använder en Linux-arbetsstation kan du använda OpenSSL för att generera en härledd enhets nyckel som visas i följande exempel.

Ersätt värdet för **Key** med den **primära nyckel** du noterade tidigare.

Ersätt värdet för **REG_ID** med enhetens registrerings-ID.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

### <a name="windows-based-workstations"></a>Windows-baserade arbets stationer

Om du använder en Windows-baserad arbets Station kan du använda PowerShell för att generera en härledd enhets nyckel som visas i följande exempel.

Ersätt värdet för **Key** med den **primära nyckel** du noterade tidigare.

Ersätt värdet för **REG_ID** med enhetens registrerings-ID.

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

IoT Edge-körningen distribueras på alla IoT Edge-enheter. Dess komponenter körs i behållare och gör att du kan distribuera ytterligare behållare till enheten så att du kan köra kod på gränsen.

Du behöver följande information när du konfigurerar din enhet:

* DPS **-ID** omfångs värde
* ID för enhets **registrering** som du har skapat
* Den **primära nyckeln** som du kopierade från DPS-registreringen

> [!TIP]
> För grupp registreringar behöver du varje enhets [härledda nyckel](#derive-a-device-key) snarare än DPS-registrerings nyckeln.

### <a name="linux-device"></a>Linux-enhet

Följ anvisningarna för enhetens arkitektur. Se till att konfigurera IoT Edge-körningen för automatisk och manuell och etablering.

[Installera Azure IoT Edge runtime på Linux](how-to-install-iot-edge-linux.md)

Avsnittet i konfigurations filen för symmetrisk nyckel etablering ser ut så här:

```yaml
# DPS symmetric key provisioning configuration
provisioning:
   source: "dps"
   global_endpoint: "https://global.azure-devices-provisioning.net"
   scope_id: "{scope_id}"
   attestation:
      method: "symmetric_key"
      registration_id: "{registration_id}"
      symmetric_key: "{symmetric_key}"
```

Ersätt plats hållarnas värden för `{scope_id}`, `{registration_id}`och `{symmetric_key}` med de data som du samlade in tidigare.

### <a name="windows-device"></a>Windows-enhet

Följ anvisningarna för att installera IoT Edge runtime på enheten som du skapade en härledd enhets nyckel för. Se till att konfigurera IoT Edge-körningen för automatisk och manuell och etablering.

[Installera och etablera IoT Edge automatiskt i Windows](how-to-install-iot-edge-windows.md#option-2-install-and-automatically-provision)

## <a name="verify-successful-installation"></a>Verifiera installationen

Om körningen har startats kan du gå till din IoT-hubb och börja distribuera IoT Edge-moduler till din enhet. Använd följande kommandon på din enhet för att kontrollera att körningen installerad och har startats.

### <a name="linux-device"></a>Linux-enhet

Kontrollera status för IoT Edge-tjänsten.

```cmd/sh
systemctl status iotedge
```

Undersök tjänst loggar.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Lista med moduler.

```cmd/sh
iotedge list
```

### <a name="windows-device"></a>Windows-enhet

Kontrollera status för IoT Edge-tjänsten.

```powershell
Get-Service iotedge
```

Undersök tjänst loggar.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Lista med moduler.

```powershell
iotedge list
```

Du kan kontrol lera att den enskilda registrering som du skapade i enhets etablerings tjänsten användes. Navigera till din enhets etablerings tjänst instans i Azure Portal. Öppna registrerings informationen för den enskilda registrering som du har skapat. Observera att statusen för registreringen är tilldelad och att enhets-ID visas.

## <a name="next-steps"></a>Nästa steg

Registreringen Device Provisioning-tjänsten kan du ange enhets-ID och device twin taggar samtidigt som du etablerar den nya enheten. Du kan använda dessa värden för att rikta enskilda enheter eller grupper av enheter med hjälp av automatisk enheter. Lär dig hur du [distribuera och övervaka IoT Edge-moduler i skala med Azure portal](how-to-deploy-monitor.md) eller [med Azure CLI](how-to-deploy-monitor-cli.md).
