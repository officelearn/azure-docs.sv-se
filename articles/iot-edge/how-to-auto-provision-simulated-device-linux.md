---
title: Etablera enhet med en virtuell TPM på Linux VM - Azure IoT Edge
description: Använda en simulerad TPM på en virtuell Linux-dator för att testa Azure Device Provisioning Service for Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/2/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b62f551e2532e0205159358b3618695524ae85c8
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666690"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Skapa och etablera en IoT Edge-enhet med en virtuell TPM på en virtuell Linux-dator

Azure IoT Edge-enheter kan etableras automatiskt med hjälp av [enhetsetableringstjänsten](../iot-dps/index.yml). Om du inte känner till processen för automatisk etablering läser du [begreppen för automatisk etablering](../iot-dps/concepts-auto-provisioning.md) innan du fortsätter.

I den här artikeln visas hur du testar automatisk etablering på en simulerad IoT Edge-enhet med följande steg:

* Skapa en virtuell Linux-dator (VM) i Hyper-V med en simulerad TPM (Trusted Platform Module) för maskinvarusäkerhet.
* Skapa en instans av DPS (IoT Hub Device Provisioning Service).
* Skapa en enskild registrering för enheten
* Installera IoT Edge-körningen och anslut enheten till IoT Hub

> [!TIP]
> I den här artikeln beskrivs hur du testar DPS-etablering med hjälp av en TPM-simulator, men mycket av det gäller för fysisk TPM-maskinvara, till exempel [Infineon OPTIGA&trade; TPM](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board), en Azure Certified for IoT-enhet.
>
> Om du använder en fysisk enhet kan du gå vidare till avsnittet [Hämta etablering från en fysisk enhet](#retrieve-provisioning-information-from-a-physical-device) i den här artikeln.

## <a name="prerequisites"></a>Krav

* En Windows-utvecklingsdator med [Hyper-V aktiverat](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). Den här artikeln använder Windows 10 som kör en virtuell Ubuntu-server.
* En aktiv IoT Hub.
* Om du använder en simulerad TPM aktiveras [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 eller senare med arbetsbelastningen ["Skrivbordsutveckling med C++".](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/)

> [!NOTE]
> TPM 2.0 krävs när du använder TPM-intyg med DPS och kan endast användas för att skapa enskilda, inte grupp- registreringar.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Skapa en virtuell Linux-dator med en virtuell TPM

I det här avsnittet skapar du en ny virtuell Linux-dator på Hyper-V. Du har konfigurerat den här virtuella datorn med en simulerad TPM så att du kan använda den för att testa hur automatisk etablering fungerar med IoT Edge.

### <a name="create-a-virtual-switch"></a>Skapa en virtuell växel

En virtuell växel gör det möjligt för den virtuella datorn att ansluta till ett fysiskt nätverk.

1. Öppna Hyper-V Manager på din Windows-dator.

2. Välj Virtual Switch **Manager**på **Åtgärdsmenyn** .

3. Välj en **extern** virtuell växel och välj sedan **Skapa virtuell växel**.

4. Ge den nya virtuella växeln ett namn, till exempel **EdgeSwitch**. Kontrollera att anslutningstypen är inställd på **Externt nätverk**och välj sedan **Ok**.

5. Ett popup-fönster varnar dig för att nätverksanslutningen kan störas. Välj **Ja** om du vill fortsätta.

Om du ser fel när du skapar den nya virtuella växeln kontrollerar du att inga andra växlar använder Ethernet-adaptern och att inga andra växlar använder samma namn.

### <a name="create-virtual-machine"></a>Skapa en virtuell dator

1. Hämta en diskavbildningsfil som ska användas för den virtuella datorn och spara den lokalt. Till exempel [Ubuntu-server](https://www.ubuntu.com/download/server).

2. Välj **Ny** > **virtuell dator** på **Åtgärdsmenyn** i Hyper-V-hanteraren igen.

3. Slutför **guiden Ny virtuell dator** med följande specifika konfigurationer:

   1. **Ange generation:** Välj **Generation 2**. Generation 2 virtuella datorer har kapslad virtualisering aktiverat, vilket krävs för att köra IoT Edge på en virtuell dator.
   2. **Konfigurera nätverk:** Ange värdet **för anslutning** till den virtuella växel som du skapade i föregående avsnitt.
   3. **Installationsalternativ:** Välj **Installera ett operativsystem från en startbar avbildningsfil** och bläddra till den diskavbildningsfil som du sparade lokalt.

4. Välj **Slutför** i guiden om du vill skapa den virtuella datorn.

Det kan ta några minuter att skapa den nya virtuella datorn.

### <a name="enable-virtual-tpm"></a>Aktivera virtuell TPM

När den virtuella datorn har skapats öppnar du dess inställningar för att aktivera tpm (Virtual Trusted Platform Module) som gör att du kan etablera enheten automatiskt.

1. Välj den virtuella datorn och öppna sedan **dess inställningar**.

2. Gå till **Security** (Säkerhet).

3. Avmarkera **Aktivera säker start**.

4. Kontrollera **Aktivera trusted platform-modul**.

5. Klicka på **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Starta den virtuella datorn och samla in TPM-data

Skapa ett verktyg som du kan använda för att hämta enhetens **registrerings-ID** och **bekräftelsenyckel**i den virtuella datorn .

1. Starta den virtuella datorn och anslut till den.

1. Följ anvisningarna i den virtuella datorn för att slutföra installationsprocessen och starta om datorn.

1. Logga in på din virtuella dator och följ sedan stegen i [Konfigurera en Linux-utvecklingsmiljö](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) för att installera och skapa Azure IoT-enheten SDK för C.

   >[!TIP]
   >Under den här artikeln kopierar och klistrar du in från den virtuella datorn, vilket inte är lätt via Hyper-V Manager-anslutningsprogrammet. Du kanske vill ansluta till den virtuella datorn via Hyper-V `ifconfig`Manager en gång för att hämta sin IP-adress: . Sedan kan du använda IP-adressen för `ssh <username>@<ipaddress>`att ansluta via SSH: .

1. Kör följande kommandon för att skapa SDK-verktyget som hämtar enhetens etableringsinformation från TPM-simulatorn.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Från ett kommandofönster navigerar du till katalogen `azure-iot-sdk-c` och kör TPM-simulatorn. Den lyssnar via en socket på portarna 2321 och 2322. Stäng inte det här kommandofönstret. du måste hålla denna simulator igång.

   Kör `azure-iot-sdk-c` följande kommando i katalogen för att starta simulatorn:

   ```bash
   ./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe
   ```

1. Öppna lösningen som genereras i `cmake` katalogen `azure_iot_sdks.sln`med namnet med Visual Studio och skapa den med kommandot **Skapa lösning** på **Bygg-menyn.**

1. I rutan**Solution Explorer** i Visual Studio går du till mappen **Provision (Etablera)\_Verktyg**. Högerklicka på projektet **tpm_device_provision** och markera **Set as Startup Project** (Ange som startprojekt).

1. Kör lösningen med något **Start** av Start-kommandona på **Felsökningsmenyn.** Utdatafönstret visar TPM-simulatorns **registrerings-ID** och **bekräftelsenyckeln**, som du bör kopiera för att användas senare när du skapar en enskild registrering för enheten i Du kan stänga det här fönstret (med registrerings-ID och bekräftelsenyckel), men lämna TPM-simulatorfönstret igång.

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Hämta etableringsinformation från en fysisk enhet

På enheten skapar du ett verktyg som du kan använda för att hämta enhetens etableringsinformation.

1. Följ stegen i [Konfigurera en Linux-utvecklingsmiljö](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) för att installera och bygga Azure IoT-enheten SDK för C.

1. Kör följande kommandon för att skapa SDK-verktyget som hämtar enhetens etableringsinformation från TPM-enheten.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Kopiera värdena för **registrerings-ID** och **bekräftelsenyckel**. Du använder dessa värden för att skapa en enskild registrering för enheten i DPS.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Konfigurera etableringstjänsten för IoT Hub Device Provisioning

Skapa en ny instans av IoT Hub Device Provisioning Service i Azure och länka den till din IoT-hubb. Du kan följa instruktionerna i [Konfigurera IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).

När du har igång enhetsetableringstjänsten kopierar du värdet för **ID-scopet** från översiktssidan. Du använder det här värdet när du konfigurerar IoT Edge-körningen.

## <a name="create-a-dps-enrollment"></a>Skapa en DPS-registrering

Hämta etableringsinformationen från den virtuella datorn och använd den för att skapa en enskild registrering i enhetsetableringstjänsten.

När du skapar en registrering i DPS har du möjlighet att deklarera ett **inledande enhets twin-tillstånd**. I enhetstvillingen kan du ange taggar för att gruppera enheter efter alla mått som du behöver i din lösning, till exempel region, miljö, plats eller enhetstyp. Dessa taggar används för att skapa [automatiska distributioner](how-to-deploy-monitor.md).

> [!TIP]
> I Azure CLI kan du skapa en [registrering](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment) eller en [registreringsgrupp](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment-group) och använda den **kantaktiverade** flaggan för att ange att en enhet, eller grupp av enheter, är en IoT Edge-enhet.

1. I [Azure-portalen](https://portal.azure.com)navigerar du till din instans av IoT Hub Device Provisioning Service.

2. Under **Inställningar**väljer du **Hantera registreringar**.

3. Välj **Lägg till individuell registrering** och utför sedan följande steg för att konfigurera registreringen:  

   1. För **Mekanism**väljer du **TPM**.

   2. Ange **bekräftelsenyckeln** och **registrerings-ID:et** som du kopierade från den virtuella datorn.

      > [!TIP]
      > Om du använder en fysisk TPM-enhet måste du bestämma **bekräftelsenyckeln**, som är unik för varje TPM-chip och erhålls från TPM-chiptillverkaren som är associerad med den. Du kan härleda ett unikt **registrerings-ID** för din TPM-enhet genom att till exempel skapa en SHA-256-hash för godkännandenyckeln.

   3. Välj **Sant** om du vill deklarera att den här virtuella datorn är en IoT Edge-enhet.

   4. Välj den länkade **IoT-hubben** som du vill ansluta enheten till. Du kan välja flera hubbar och enheten tilldelas en av dem enligt den valda fördelningsprincipen.

   5. Ange ett ID till enheten om du vill. Du kan använda enhets-ID:er för att rikta in dig på en enskild enhet för moduldistribution. Om du inte anger ett enhets-ID används registrerings-ID: t.

   6. Lägg till ett taggvärde i det **inledande enhets twin-tillståndet** om du vill. Du kan använda taggar för att rikta grupper av enheter för moduldistribution. Ett exempel:

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

   7. Välj **Spara**.

Nu när det finns en registrering för den här enheten kan IoT Edge-körningen automatiskt etablera enheten under installationen.

## <a name="install-the-iot-edge-runtime"></a>Installera IoT Edge-körningen

IoT Edge-körningen distribueras på alla IoT Edge-enheter. Dess komponenter körs i behållare och gör att du kan distribuera ytterligare behållare till enheten så att du kan köra kod vid kanten. Installera IoT Edge-körningen på den virtuella datorn.

Känn till ditt **DPS-ID-scope** och **enhetsregistrerings-ID** innan du påbörjar artikeln som matchar din enhetstyp. Om du har installerat exemplet Ubuntu-servern använder du **x64-instruktionerna.** Se till att konfigurera IoT Edge-körningen för automatisk, inte manuell etablering.

[Installera Azure IoT Edge-körningen på Linux](how-to-install-iot-edge-linux.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>Ge IoT Edge åtkomst till TPM:en

För att IoT Edge-körningen automatiskt ska kunna etablera enheten behöver den åtkomst till TPM:en.

Du kan ge TPM-åtkomst till IoT Edge-körningen genom att åsidosätta systeminställningarna så att **iotedge-tjänsten** har root-privilegier. Om du inte vill höja tjänstbehörigheterna kan du också använda följande steg för att manuellt ge TPM-åtkomst.

1. Hitta filsökvägen till TPM-maskinvarumodulen på enheten och spara den som en lokal variabel.

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Skapa en ny regel som ger IoT Edge-körningen åtkomst till tpm0.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Öppna regelfilen.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Kopiera följande åtkomstinformation till regelfilen.

   ```input
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", GROUP="iotedge", MODE="0660"
   ```

5. Spara och avsluta filen.

6. Utlösa udev-systemet för att utvärdera den nya regeln.

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Kontrollera att regeln har tillämpats.

   ```bash
   ls -l /dev/tpm0
   ```

   Lyckad utdata ser ut så här:

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Om du inte ser att rätt behörigheter har tillämpats kan du prova att starta om datorn för att uppdatera udev.

## <a name="restart-the-iot-edge-runtime"></a>Starta om IoT Edge-körningen

Starta om IoT Edge-körningen så att alla konfigurationsändringar som du har gjort på enheten hämtas.

   ```bash
   sudo systemctl restart iotedge
   ```

Kontrollera att IoT Edge-körningen körs.

   ```bash
   sudo systemctl status iotedge
   ```

Om du ser etableringsfel kan det bero på att konfigurationsändringarna inte har trätt i kraft ännu. Försök att starta om IoT Edge-demonen igen.

   ```bash
   sudo systemctl daemon-reload
   ```

Du kan också prova att starta om den virtuella datorn för att se om ändringarna börjar gälla på nytt.

## <a name="verify-successful-installation"></a>Verifiera lyckad installation

Om körningen har startats kan du gå in i din IoT Hub och se att den nya enheten har etablerats automatiskt. Nu är enheten redo att köra IoT Edge-moduler.

Kontrollera status för IoT Edge Daemon.

```cmd/sh
systemctl status iotedge
```

Undersök demonloggar.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Lista löpmoduler.

```cmd/sh
iotedge list
```

Du kan kontrollera att den enskilda registreringen som du skapade i enhetsetableringstjänsten har använts. Navigera till instansen för enhetsetableringstjänst i Azure-portalen. Öppna registreringsinformationen för den enskilda registreringen som du skapade. Observera att status för registreringen **har tilldelats** och att enhets-ID:t visas.

## <a name="next-steps"></a>Nästa steg

Med registreringsprocessen för enhetsetableringstjänsten kan du ange enhets-ID- och enhetstvillingtaggarna samtidigt som du etablerar den nya enheten. Du kan använda dessa värden för att rikta in dig på enskilda enheter eller grupper av enheter med hjälp av automatisk enhetshantering. Lär dig hur du [distribuerar och övervakar IoT Edge-moduler i stor skala med Hjälp av Azure-portalen](how-to-deploy-monitor.md) eller [med Azure CLI](how-to-deploy-monitor-cli.md).
