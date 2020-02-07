---
title: Kör Azure IoT Edge på Ubuntu Virtual Machines | Microsoft Docs
description: Azure IoT Edge installations anvisningar på Ubuntu 16,04 Azure Marketplace Virtual Machines
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: philmea
ms.openlocfilehash: 49a783e1360aeddc8eeaadba442acf578d9d6f7f
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2020
ms.locfileid: "77046050"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Kör Azure IoT Edge på Ubuntu Virtual Machines

Azure IoT Edge-körningen är vad omvandlar en enhet till en IoT Edge-enhet. Körningen kan distribueras på enheter som är så litet som en Raspberry Pi eller stora som industriella-server. När en enhet konfigureras med IoT Edge-körningen, kan du börja distribuera affärslogik till den från molnet.

Mer information om hur IoT Edge runtime fungerar och vilka komponenter som ingår finns i [förstå Azure IoT Edge Runtime och dess arkitektur](iot-edge-runtime.md).

Den här artikeln innehåller anvisningar för att köra Azure IoT Edge runtime på en virtuell Ubuntu 16,04-dator med hjälp av den förkonfigurerade [Azure IoT Edge på Azure Marketplace-erbjudandet](https://aka.ms/azure-iot-edge-ubuntuvm).

Vid den första starten förinstallerar Azure IoT Edge Ubuntu VM den senaste versionen av Azure IoT Edge Runtime. Det innehåller också ett skript för att ange anslutnings strängen och sedan starta om körningen, som kan utlösas via en fjärr anslutning via Azures virtuella dator portal eller Azure-kommandoraden, så att du enkelt kan konfigurera och ansluta IoT Edge-enheten utan att starta en SSH eller fjärr anslutning fjärrskrivbordssession. Det här skriptet väntar på att ange anslutnings strängen tills IoT Edge-klienten är helt installerad så att du inte behöver skapa den i din automatisering.

## <a name="deploy-from-the-azure-marketplace"></a>Distribuera från Azure Marketplace

1. Navigera till Azure IoT Edge Marketplace-erbjudande för [Ubuntu](https://aka.ms/azure-iot-edge-ubuntuvm) eller genom att söka Azure IoT Edge på Ubuntu på [Azure Marketplace](https://azuremarketplace.microsoft.com/)
2. Välj **Hämta nu** och **Fortsätt** sedan i nästa dialog ruta.
3. När du är i Azure Portal väljer du **skapa** och följer guiden för att distribuera den virtuella datorn.
    * Om det är första gången du försöker ta bort en virtuell dator är det enklast att använda ett lösen ord och att aktivera SSH på den offentliga menyn för inkommande port.
    * Om du har en resurs intensiv arbets belastning bör du uppgradera storleken på den virtuella datorn genom att lägga till fler processorer och/eller minne.
4. När den virtuella datorn har distribuerats konfigurerar du den så att den ansluter till din IoT Hub:
    1. Kopiera enhets anslutnings strängen från din IoT Edge enhet som skapats i din IoT Hub (du kan följa anvisningarna för att [Hämta anslutnings strängen i Azure Portal](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal) om du inte är bekant med den här processen)
    1. Välj den virtuella dator resurs som du skapade nyligen från Azure Portal och öppna **kommando alternativet Kör**
    1. Välj alternativet **RunShellScript**
    1. Kör skriptet nedan via kommando fönstret med enhets anslutnings strängen: `/etc/iotedge/configedge.sh "{device_connection_string}"`
    1. Välj **Kör**
    1. Vänta en stund och skärmen bör sedan ange ett meddelande som anger att anslutnings strängen har angetts.

## <a name="deploy-from-the-azure-portal"></a>Distribuera från Azure Portal

Från Azure Portal söker du efter "Azure IoT Edge" och väljer **Ubuntu Server 16,04 LTS + Azure IoT Edge runtime** för att starta arbets flödet för skapande av virtuella datorer. Därifrån slutför du steg 3 och 4 i anvisningarna för att distribuera från Azure Marketplace ovan.

## <a name="deploy-from-azure-cli"></a>Distribuera från Azure CLI

1. Om du använder Azure CLI på Skriv bordet startar du genom att logga in:

   ```azurecli-interactive
   az login
   ```

1. Om du har flera prenumerationer väljer du den prenumeration som du vill använda:
   1. Lista dina prenumerationer:

      ```azurecli-interactive
      az account list --output table
      ```

   1. Kopiera SubscriptionID-fältet för den prenumeration du vill använda.

   1. Ange din arbets prenumeration med det ID som du precis har kopierat:

      ```azurecli-interactive
      az account set -s {SubscriptionId}
      ```

1. Skapa en ny resurs grupp (eller ange en befintlig i nästa steg):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Godkänn användnings villkoren för den virtuella datorn. Om du vill granska villkoren först följer du stegen i [distribuera från Azure Marketplace](#deploy-from-the-azure-marketplace).

   ```azurecli-interactive
   az vm image terms accept --urn microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest
   ```

1. Skapa en ny virtuell dator:

   ```azurecli-interactive
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys
   ```

1. Ange anslutnings strängen för enheten (du kan följa anvisningarna för att [Hämta anslutnings strängen med Azure CLI](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli) om du inte är bekant med den här processen):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script "/etc/iotedge/configedge.sh '{device_connection_string}'"
   ```

Om du vill använda SSH i den här virtuella datorn efter installationen använder du publicIpAddress med kommandot: `ssh azureuser@{publicIpAddress}`

## <a name="next-steps"></a>Nästa steg

Nu när du har en IoT Edge enhet som har installerats med körnings miljön kan du [distribuera IoT Edge moduler](how-to-deploy-modules-portal.md).

Om du har problem med IoT Edge runtime-installationen kan du kolla in [fel söknings](troubleshoot.md) sidan.

Om du vill uppdatera en befintlig installation till den senaste versionen av IoT Edge, se [uppdatera IoT Edge Security daemon och runtime](how-to-update-iot-edge.md).

Om du vill öppna portarna för att få åtkomst till den virtuella datorn via SSH eller andra inkommande anslutningar läser du dokumentationen om att [öppna portar och slut punkter till en virtuell Linux](../virtual-machines/linux/nsg-quickstart.md) -dator.
