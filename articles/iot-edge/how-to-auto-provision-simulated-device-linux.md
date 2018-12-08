---
title: Automatiskt etablera Linux-enheter med DPS – Azure IoT Edge | Microsoft Docs
description: Använd en simulerad TPM på en Linux VM för att testa Azure Device Provisioning-tjänsten för Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/31/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 0550b1765e36d591a1baf34d3c255a252ca5278b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53101763"
---
# <a name="create-and-provision-an-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Skapa och etablera en Edge-enhet med en virtuell TPM på en Linux-dator

Azure IoT Edge-enheter kan vara autoprovisioned med hjälp av den [Device Provisioning-tjänsten](../iot-dps/index.yml) precis som enheter som inte är Microsoft edge-aktiverade. Om du är bekant med processen för autoprovisioning kan du granska den [autoprovisioning begrepp](../iot-dps/concepts-auto-provisioning.md) innan du fortsätter. 

Den här artikeln visar hur du testar autoprovisioning på en simulerad Edge-enhet med följande steg: 

* Skapa en Linux-dator (VM) i Hyper-V med en simulerad Trusted Platform Module (TPM) för maskinvara säkerhet.
* Skapa en instans av IoT Hub enheten Provisioning Service (DPS).
* Skapa en enskild registrering för enheten
* Installera IoT Edge-körningen och ansluta enheten till IoT Hub

Stegen i den här artikeln är avsedd för testning.

## <a name="prerequisites"></a>Förutsättningar

* En Windows-utvecklingsdator med [Hyper-V aktiverat](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). Den här artikeln använder Windows 10 som körs på en dator med Ubuntu Server. 
* En aktiv IoT-hubb. 

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Skapa en Linux-dator med en virtuell TPM

I det här avsnittet skapar du en ny Linux virtuell dator på Hyper-V med en simulerad TPM så att du kan använda den för att testa hur autoprovisioning fungerar med IoT Edge. 

### <a name="create-a-virtual-switch"></a>Skapa en virtuell växel

En virtuell växel kan den virtuella datorn ska ansluta till ett fysiskt nätverk.

1. Öppna Hyper-V på windows-datorn. 

2. I den **åtgärder** menyn och välj **hanteraren för virtuella växlar**. 

3. Välj en **externa** virtuella växlar och välj sedan **Skapa virtuell växel**. 

4. Namnge din nya virtuella växeln, till exempel **EdgeSwitch**. Se till att en typ har angetts till **externt nätverk**och välj sedan **Ok**.

5. Ett popup-fönster varnar dig om att nätverksanslutningen kan avbrytas. Välj **Ja** att fortsätta. 

Om du får felmeddelanden när du skapar den nya virtuella växeln, kontrollerar du att inga andra växlar använder ethernet-adaptern och att inga andra växlar använder samma namn. 

### <a name="create-virtual-machine"></a>Skapa en virtuell dator

1. Ladda ned en disk image-filen om du vill använda för den virtuella datorn och spara den lokalt. Till exempel [Ubuntu server](https://www.ubuntu.com/download/server). 

2. Öppna Hyper-V igen. I den **åtgärder** menyn och välj **New** > **VM**.

3. Slutför den **guiden Ny virtuell dator** med följande specifika konfigurationer:

   1. **Ange Generation**: Välj **Generation 2**.
   2. **Konfigurera nätverk**: Ange värdet för **anslutning** till den virtuella växeln som du skapade i föregående avsnitt. 
   3. **Installationsalternativ**: Välj **installera ett operativsystem från en startbar avbildningsfil** och bläddra till disk image-filen som du sparade lokalt.

Det kan ta några minuter att skapa den nya virtuella datorn. 

### <a name="enable-virtual-tpm"></a>Aktivera virtuell TPM

1. När den virtuella datorn har skapats öppnar du dess inställningar. 
2. Gå till **Security**. 
3. Avmarkera **aktivera säker Start**.
4. Kontrollera **aktivera Trusted Platform Module**. 
5. Klicka på **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Starta den virtuella datorn och samla in data för TPM

I den virtuella datorn, skapar du ett C SDK-verktyg som du kan använda för att hämta enhetens **registrerings-ID** och **bekräftelsenyckeln**. 

1. Starta den virtuella datorn och ansluta till den för att slutföra installationsprocessen. 

2. I den virtuella datorn följer du stegen i [ställa in en Linux-utvecklingsmiljö](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) att installera och skapa Azure IoT-enhetens SDK för C. 

3. Kör följande kommandon för att skapa ett C SDK-verktyg som hämtar din etablering enhetsinformationen. 

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

3. Kopiera värdena för **registrerings-ID** och **bekräftelsenyckeln**. Du kan använda dessa värden för att skapa en enskild registrering för din enhet i DPS. 

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Konfigurera IoT Hub Device Provisioning-tjänsten

Skapa en ny instans av IoT Hub Device Provisioning-tjänsten i Azure och länka det till din IoT hub. Du kan följa anvisningarna i [konfigurera IoT Hub-DPS](../iot-dps/quick-setup-auto-provision.md).

När du har Device Provisioning-tjänsten körs, kopierar du värdet för **ID-omfång** från översiktssidan. Du kan använda det här värdet när du konfigurerar IoT Edge-körningen. 

## <a name="create-a-dps-enrollment"></a>Skapa en DPS-registrering

Hämta etableringsinformationen från din virtuella dator och använda den för att skapa en enskild registrering i Device Provisioning-tjänsten. 

När du skapar en registrering i DPS har möjlighet att deklarera en **starttillstånd för Enhetstvilling**. I enhetstvillingen, kan du ställa in etiketter att gruppera enheter efter valfritt mått som du behöver i din lösning som region, miljö, plats eller enhet. Dessa taggar som används för att skapa [automatiska distributioner](how-to-deploy-monitor.md). 


1. I den [Azure-portalen](https://portal.azure.com), och navigera till din instans av IoT Hub Device Provisioning-tjänsten. 

2. Under **inställningar**väljer **hantera registreringar**. 

3. Välj **Lägg till enskild registrering** utför följande steg om du vill konfigurera registreringen:  

   1. För **mekanism**väljer **TPM**. 
   2. Infoga den **bekräftelsenyckeln** och **registrerings-ID** som du kopierade från den virtuella datorn.
   3. Välj **aktivera** att deklarera att den här virtuella datorn är en IoT Edge-enhet. 
   4. Välj den länkade **IoT Hub** som du vill ansluta enheten till. 
   5. Ange ett ID för din enhet om du vill ha. Du kan använda enhets-ID för att rikta en enskild enhet för distribution av principmodul. 
   6. Lägg till ett visst Taggvärde till den **starttillstånd för Enhetstvilling** om du vill ha. Du kan använda taggar för målgrupper för enheter för distribution av principmodul. 
   7. Välj **Spara**. 


## <a name="install-the-iot-edge-runtime"></a>Installera IoT Edge-körningen

IoT Edge-körningen distribueras på alla IoT Edge-enheter. Dess komponenter körs i behållare och gör att du kan distribuera ytterligare behållare till enheten så att du kan köra kod på gränsen. Installera IoT Edge-körningen på den virtuella datorn. 

Vet DPS **ID-omfång** och **registrerings-ID** innan du påbörjar den artikel som matchar din enhetstyp. Om du har installerat exempel Ubuntu server, använder den **x64** instruktioner. Se till att konfigurera IoT Edge-körningen för automatisk och manuell och etablering. 

* [Linux (x64)](how-to-install-iot-edge-linux.md)
* [Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>Ge IoT Edge-åtkomst till TPM

För IoT Edge-körningen att automatiskt etablera din enhet, måste den ha åtkomst till TPM. 

Du kan ge TPM-åtkomst till IoT Edge-körningen genom att åsidosätta systemd inställningar så att den *iotedge* tjänsten har rotprivilegier. Om du inte vill att upphöja behörighet för tjänsten, kan du också använda följande steg manuellt klientåtkomst TPM. 

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

8. Öppna IoT Edge-körningen åsidosätter fil. 

   ```bash
   sudo systemctl edit iotedge.service
   ```

9. Lägg till följande kod för att upprätta en TPM-miljövariabeln.

   ```input
   [Service]
   Environment=IOTEDGE_USE_TPM_DEVICE=ON
   ```

9. Kontrollera att åsidosättningen har lyckats.

   ```bash
   sudo systemctl cat iotedge.service
   ```

   Lyckad utdata visar den **iotedge** standard service variabler och visar sedan miljövariabeln att du ställer in **override.conf**. 

12. Läsa in inställningarna.

   ```bash
   sudo systemctl daemon-reload
   ```

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


## <a name="next-steps"></a>Nästa steg

Registreringen Device Provisioning-tjänsten kan du ange enhets-ID och device twin taggar samtidigt som du etablerar den nya enheten. Du kan använda dessa värden för att rikta enskilda enheter eller grupper av enheter med hjälp av automatisk enheter. Lär dig hur du [distribuera och övervaka IoT Edge-moduler i skala med Azure portal](how-to-deploy-monitor.md) eller [med Azure CLI](how-to-deploy-monitor-cli.md).
