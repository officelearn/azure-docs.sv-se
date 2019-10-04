---
title: Etablera Linux-enheter automatiskt med DPS-Azure IoT Edge | Microsoft Docs
description: Använd en simulerad TPM på en Linux VM för att testa Azure Device Provisioning-tjänsten för Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 228851a0d528bfb222e5aa19880f856424e95ad1
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828130"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Skapa och etablera en IoT Edge enhet med en virtuell TPM på en virtuell Linux-dator

Azure IoT Edge enheter kan tillhandahållas automatiskt med [enhets etablerings tjänsten](../iot-dps/index.yml). Om du är bekant med processen för automatisk etablering kan du granska den [begrepp inom Automatisk etablering](../iot-dps/concepts-auto-provisioning.md) innan du fortsätter.

Den här artikeln visar hur du testar automatisk etablering på en simulerad IoT Edge enhet med följande steg:

* Skapa en Linux-dator (VM) i Hyper-V med en simulerad Trusted Platform Module (TPM) för maskinvara säkerhet.
* Skapa en instans av IoT Hub enheten Provisioning Service (DPS).
* Skapa en enskild registrering för enheten
* Installera IoT Edge-körningen och ansluta enheten till IoT Hub

> [!NOTE]
> TPM 2,0 krävs när du använder TPM-attestering med DPS och kan endast användas för att skapa enskilda, inte grupper, registreringar.

> [!TIP]
> Den här artikeln beskriver hur du testar DPS-etablering med hjälp av en TPM-simulator, men det är mycket som gäller för fysisk TPM-maskinvara som [INFINEON OPTIGA @ no__t-1 TPM](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board), en Azure-certifierad för IoT-enhet.
>
> Om du använder en fysisk enhet kan du gå vidare till avsnittet [Hämta etablerings information från en fysisk enhet](#retrieve-provisioning-information-from-a-physical-device) i den här artikeln.

## <a name="prerequisites"></a>Förutsättningar

* En Windows-utvecklingsdator med [Hyper-V aktiverat](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). Den här artikeln använder Windows 10 som körs på en dator med Ubuntu Server.
* En aktiv IoT-hubb.
* Om du använder en simulerad TPM, [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 eller senare med arbets belastningen [" C++Skriv bords utveckling med"](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) aktiverat.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Skapa en Linux-dator med en virtuell TPM

I det här avsnittet skapar du en ny virtuell Linux-dator på Hyper-V. Du har konfigurerat den här virtuella datorn med en simulerad TPM så att du kan använda den för att testa hur automatisk etablering fungerar med IoT Edge. 

### <a name="create-a-virtual-switch"></a>Skapa en virtuell växel

En virtuell växel kan den virtuella datorn ska ansluta till ett fysiskt nätverk.

1. Öppna Hyper-V Manager på Windows-datorn. 

2. I den **åtgärder** menyn och välj **hanteraren för virtuella växlar**. 

3. Välj en **externa** virtuella växlar och välj sedan **Skapa virtuell växel**. 

4. Namnge din nya virtuella växeln, till exempel **EdgeSwitch**. Se till att en typ har angetts till **externt nätverk**och välj sedan **Ok**.

5. Ett popup-fönster varnar dig om att nätverksanslutningen kan avbrytas. Välj **Ja** att fortsätta. 

Om du får felmeddelanden när du skapar den nya virtuella växeln, kontrollerar du att inga andra växlar använder ethernet-adaptern och att inga andra växlar använder samma namn. 

### <a name="create-virtual-machine"></a>Skapa en virtuell dator

1. Ladda ned en disk image-filen om du vill använda för den virtuella datorn och spara den lokalt. Till exempel [Ubuntu server](https://www.ubuntu.com/download/server). 

2. I Hyper-V Manager igen väljer du **ny** > **virtuell dator** i menyn **åtgärder** .

3. Slutför den **guiden Ny virtuell dator** med följande specifika konfigurationer:

   1. **Ange generation**: Välj **generation 2**. Virtuella datorer i generation 2 har kapslad virtualisering aktiverat, vilket krävs för att köra IoT Edge på en virtuell dator.
   2. **Konfigurera nätverk**: Ange värdet för **anslutning** till den virtuella växel som du skapade i föregående avsnitt. 
   3. **Installations alternativ**: Välj **Installera ett operativ system från en startbar avbildnings fil** och bläddra till disk avbildnings filen som du sparade lokalt.

4. Klicka på **Slutför** i guiden för att skapa den virtuella datorn.

Det kan ta några minuter att skapa den nya virtuella datorn. 

### <a name="enable-virtual-tpm"></a>Aktivera virtuell TPM

När den virtuella datorn har skapats öppnar du dess inställningar för att aktivera den virtuella Trusted Platform Module (TPM) som gör att du kan etablera enheten automatiskt.

1. Välj den virtuella datorn och öppna dess **Inställningar**.

2. Gå till **Security**. 

3. Avmarkera **aktivera säker Start**.

4. Kontrollera **aktivera Trusted Platform Module**. 

5. Klicka på **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Starta den virtuella datorn och samla in data för TPM

Bygg ett verktyg som du kan använda för att hämta enhetens **registrerings-ID** och **bekräftelse nyckel**på den virtuella datorn.

1. Starta den virtuella datorn och Anslut till den.

1. Slutför installations processen genom att följa anvisningarna i den virtuella datorn och starta om datorn.

1. Logga in på den virtuella datorn och följ stegen i [Konfigurera en Linux-utvecklings miljö](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) för att installera och bygga Azure IoT-enhetens SDK för C.

   >[!TIP]
   >I den här artikeln ska du kopiera till och klistra in från den virtuella datorn, vilket inte är enkelt via anslutnings programmet för Hyper-V Manager. Du kanske vill ansluta till den virtuella datorn via Hyper-V Manager en gång för att hämta dess IP-adress: `ifconfig`. Sedan kan du använda IP-adressen för att ansluta via SSH: `ssh <username>@<ipaddress>`.

1. Kör följande kommandon för att skapa SDK-verktyget som hämtar din enhets etablerings information från TPM-simulatorn.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Från ett kommando fönster navigerar du till katalogen `azure-iot-sdk-c` och kör TPM-simulatorn. Den lyssnar via en socket på portarna 2321 och 2322. Stäng inte det här kommando fönstret. den här simulatorn måste vara igång.

   Kör följande kommando från katalogen `azure-iot-sdk-c` för att starta simulatorn:

   ```bash
   ./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe
   ```

1. Med Visual Studio öppnar du lösningen som genererats i `cmake`-katalogen med namnet `azure_iot_sdks.sln` och skapar den med hjälp av kommandot **skapa lösning** på menyn **skapa** .

1. I rutan**Solution Explorer** i Visual Studio går du till mappen **Provision (Etablera)\_Verktyg**. Högerklicka på projektet **tpm_device_provision** och markera **Set as Startup Project** (Ange som startprojekt).

1. Kör lösningen med något av **Start** kommandona på **fel söknings** menyn. I fönstret Utdata visas TPM-Simulatorns **registrerings-ID** och **bekräftelse nyckeln**, som du bör kopiera för att använda senare när du skapar en enskild registrering för enheten i du kan stänga det här fönstret (med registrerings-ID och Bekräftelse nyckel), men lämna fönstret för TPM-simulatorn igång.

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Hämta etablerings information från en fysisk enhet

Bygg ett verktyg som du kan använda för att hämta enhetens etablerings information på enheten.

1. Följ stegen i [Konfigurera en Linux-utvecklings miljö](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) för att installera och bygga Azure IoT-enhetens SDK för C.

1. Kör följande kommandon för att skapa SDK-verktyget som hämtar din enhets etablerings information från TPM-enheten.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Kopiera värdena för **registrerings-ID** och **bekräftelse nyckel**. Du kan använda dessa värden för att skapa en enskild registrering för din enhet i DPS.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Konfigurera IoT Hub Device Provisioning-tjänsten

Skapa en ny instans av IoT Hub Device Provisioning-tjänsten i Azure och länka det till din IoT hub. Du kan följa anvisningarna i [konfigurera IoT Hub-DPS](../iot-dps/quick-setup-auto-provision.md).

När du har Device Provisioning-tjänsten körs, kopierar du värdet för **ID-omfång** från översiktssidan. Du kan använda det här värdet när du konfigurerar IoT Edge-körningen. 

## <a name="create-a-dps-enrollment"></a>Skapa en DPS-registrering

Hämta etableringsinformationen från din virtuella dator och använda den för att skapa en enskild registrering i Device Provisioning-tjänsten. 

När du skapar en registrering i DPS har möjlighet att deklarera en **starttillstånd för Enhetstvilling**. I enhetstvillingen, kan du ställa in etiketter att gruppera enheter efter valfritt mått som du behöver i din lösning som region, miljö, plats eller enhet. Dessa taggar som används för att skapa [automatiska distributioner](how-to-deploy-monitor.md). 

1. I [Azure Portal](https://portal.azure.com)navigerar du till din instans av IoT Hub Device Provisioning service. 

2. Under **inställningar**väljer **hantera registreringar**. 

3. Välj **Lägg till enskild registrering** utför följande steg om du vill konfigurera registreringen:  

   1. För **mekanism**väljer **TPM**. 

   2. Ange **bekräftelse nyckeln** och **registrerings-ID: t** som du kopierade från den virtuella datorn.

      > [!TIP]
      > Om du använder en fysisk TPM-enhet måste du bestämma **bekräftelse nyckeln**, som är unik för varje TPM-chip och hämtas från TPM-kretsen som är kopplad till den. Du kan härleda ett unikt **registrerings-ID** för din TPM-enhet genom att till exempel skapa en SHA-256-hash för bekräftelse nyckeln.

   3. Välj **Sant** om du vill deklarera att den virtuella datorn är en IoT Edge enhet. 

   4. Välj den länkade **IoT Hub** som du vill ansluta enheten till. Du kan välja flera hubbar och enheten tilldelas en av dem enligt den valda allokeringsregeln. 

   5. Ange ett ID för din enhet om du vill ha. Du kan använda enhets-ID för att rikta en enskild enhet för distribution av principmodul. Om du inte anger något enhets-ID används registrerings-ID: t.

   6. Lägg till ett visst Taggvärde till den **starttillstånd för Enhetstvilling** om du vill ha. Du kan använda taggar för målgrupper för enheter för distribution av principmodul. Exempel: 

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

Nu när en registrering finns för den här enheten kan IoT Edge runtime automatiskt etablera enheten under installationen. 

## <a name="install-the-iot-edge-runtime"></a>Installera IoT Edge-körningen

IoT Edge-körningen distribueras på alla IoT Edge-enheter. Dess komponenter körs i behållare och gör att du kan distribuera ytterligare behållare till enheten så att du kan köra kod på gränsen. Installera IoT Edge-körningen på den virtuella datorn. 

Vet DPS **ID-omfång** och **registrerings-ID** innan du påbörjar den artikel som matchar din enhetstyp. Om du har installerat exempel Ubuntu server, använder den **x64** instruktioner. Se till att konfigurera IoT Edge-körningen för automatisk och manuell och etablering. 

[Installera Azure IoT Edge runtime på Linux](how-to-install-iot-edge-linux.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>Ge IoT Edge-åtkomst till TPM

För IoT Edge-körningen att automatiskt etablera din enhet, måste den ha åtkomst till TPM. 

Du kan ge TPM-åtkomst till IoT Edge-körningen genom att åsidosätta systemd inställningar så att den **iotedge** tjänsten har rotprivilegier. Om du inte vill att upphöja behörighet för tjänsten, kan du också använda följande steg manuellt klientåtkomst TPM. 

1. Hitta sökvägen till maskinvara modulen på din enhet och spara den som en lokal variabel. 

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Skapa en ny regel som ger IoT Edge runtime åtkomst till tpm0. 

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Öppna filen regler. 

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Kopiera följande information i regelfilen. 

   ```input 
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", GROUP="iotedge", MODE="0660"
   ```

5. Spara och stänga filen. 

6. Utlös udev-systemet för att utvärdera den nya regeln. 

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

   Om du inte ser att rätt behörigheter har tillämpats, försök att starta om datorn för att uppdatera udev. 

## <a name="restart-the-iot-edge-runtime"></a>Starta om IoT Edge-körningen

Starta om IoT Edge-körningen så att det använder alla konfigurationsändringar du gjort på enheten. 

   ```bash
   sudo systemctl restart iotedge
   ```

Kontrollera att IoT Edge-körningen körs. 

   ```bash
   sudo systemctl status iotedge
   ```

Om du ser etablering fel kan vara det att ändringar i konfigurationen inte har börjat gälla ännu. Försök att starta om IoT Edge-daemon igen. 

   ```bash
   sudo systemctl daemon-reload
   ```
   
Eller försök att starta om den virtuella datorn för att se om ändringarna börjar gälla på från början. 

## <a name="verify-successful-installation"></a>Verifiera installationen

Om körningen har startats, kan du gå till din IoT Hub och visa att den nya enheten etablerades automatiskt. Enheten är nu redo att köra IoT Edge-moduler. 

Kontrollera status för IoT Edge-Daemon.

```cmd/sh
systemctl status iotedge
```

Granska loggarna för daemon.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Lista med moduler.

```cmd/sh
iotedge list
```

Du kan kontrol lera att den enskilda registrering som du skapade i enhets etablerings tjänsten användes. Navigera till din enhets etablerings tjänst instans i Azure Portal. Öppna registrerings informationen för den enskilda registrering som du har skapat. Observera att statusen för registreringen är tilldelad och att enhets-ID visas. 

## <a name="next-steps"></a>Nästa steg

Registreringen Device Provisioning-tjänsten kan du ange enhets-ID och device twin taggar samtidigt som du etablerar den nya enheten. Du kan använda dessa värden för att rikta enskilda enheter eller grupper av enheter med hjälp av automatisk enheter. Lär dig hur du [distribuera och övervaka IoT Edge-moduler i skala med Azure portal](how-to-deploy-monitor.md) eller [med Azure CLI](how-to-deploy-monitor-cli.md).
