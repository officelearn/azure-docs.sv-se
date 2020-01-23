---
title: Kör Azure IoT Edge på Windows Server Virtual Machines | Microsoft Docs
description: Azure IoT Edge installations anvisningar på Windows Server Marketplace Virtual Machines
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: gregman
ms.openlocfilehash: 99474246bf1ff5cbcc39861d56f05aa38f177f31
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510047"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>Köra Azure IoT Edge på Windows Server Virtual Machines

Azure IoT Edge-körningen är vad omvandlar en enhet till en IoT Edge-enhet. Körningen kan distribueras på enheter som är så litet som en Raspberry Pi eller stora som industriella-server. När en enhet konfigureras med IoT Edge-körningen, kan du börja distribuera affärslogik till den från molnet.

Läs mer om hur IoT Edge-körningen fungerar och vilka komponenter som ingår i [förstå Azure IoT Edge-körningen och dess arkitektur](iot-edge-runtime.md).

Den här artikeln innehåller anvisningar för att köra Azure IoT Edge runtime på en virtuell Windows Server 2019-dator med hjälp av [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) Azure Marketplace-erbjudandet. Följ anvisningarna i [installera Azure IoT Edge runtime](how-to-install-iot-edge-windows.md) i Windows för användning med andra versioner.

## <a name="deploy-from-the-azure-marketplace"></a>Distribuera från Azure Marketplace

1. Navigera till [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) Azure Marketplace-erbjudandet eller genom att söka i Windows Server på [Azure Marketplace](https://azuremarketplace.microsoft.com/)
2. Välj **Hämta nu**
3. I **Software plan**letar du upp "Windows Server 2019 Data Center Server Core with Containers" och väljer sedan **Fortsätt** i nästa dialog ruta.
    * Du kan också använda dessa instruktioner för andra versioner av Windows Server med behållare
4. När du är i Azure Portal väljer du **skapa** och följer guiden för att distribuera den virtuella datorn.
    * Om det är första gången du försöker ta bort en virtuell dator är det enklast att använda ett lösen ord och att aktivera RDP och SSH på menyn för offentlig inkommande port.
    * Om du har en resurs intensiv arbets belastning bör du uppgradera storleken på den virtuella datorn genom att lägga till fler processorer och/eller minne.
5. När den virtuella datorn har distribuerats konfigurerar du den så att den ansluter till din IoT Hub:
    1. Kopiera enhets anslutnings strängen från IoT Edge enhet som skapats i IoT Hub. Se proceduren [Hämta anslutnings strängen i Azure Portal](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal).
    1. Välj den virtuella dator resurs som du skapade nyligen från Azure Portal och öppna **kommando alternativet Kör**
    1. Välj alternativet **RunPowerShellScript**
    1. Kopiera det här skriptet till kommando fönstret med enhets anslutnings strängen:

        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```

    1. Kör skriptet för att installera IoT Edge Runtime och ange anslutnings strängen genom att välja **Kör**
    1. Efter en minut eller två bör du se ett meddelande om att Edge Runtime har installerats och kon figurer ATS korrekt.

## <a name="deploy-from-the-azure-portal"></a>Distribuera från Azure Portal

1. Från Azure Portal söker du efter "Windows Server" och väljer **Windows server 2019 Data Center** för att starta arbets flödet för skapande av virtuell dator.
2. Välj **en program plan** Välj "Windows Server 2019 Data Center Server Core with Containers" och välj sedan **skapa**
3. Slutför steg 5 i anvisningarna för att distribuera från Azure Marketplace ovan.

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

   1. Kopiera SubscriptionID-fältet för den prenumeration du vill använda
   1. Kör det här kommandot med det ID som du kopierade:

      ```azurecli-interactive
      az account set -s {SubscriptionId}
      ```

1. Skapa en ny resurs grupp (eller ange en befintlig i nästa steg):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Skapa en ny virtuell dator:

   ```azurecli-interactive
   az vm create -g IoTEdgeResources -n EdgeVM --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter-Core-with-Containers:latest  --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```

   * Med det här kommandot uppmanas du att ange ett lösen ord, men du kan lägga till alternativet `--admin-password` så att det blir enklare i ett skript
   * Windows Server Core-avbildningen har endast stöd för kommando rad med fjärr skrivbord, så om du vill ha en fullständig Skriv bords upplevelse anger du `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest` som avbildning

1. Ange anslutnings strängen för enheten (du kan följa proceduren [Hämta anslutnings strängen med Azure CLI](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli) om du inte är bekant med den här processen):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>Nästa steg

Nu när du har en IoT Edge-enhet med den som är installerad kan du [distribuera IoT Edge-moduler](how-to-deploy-modules-portal.md).

Om du har problem med att Edge runtime installeras på rätt sätt kan du läsa [fel söknings](troubleshoot.md) sidan.

Om du vill uppdatera en befintlig installation till den senaste versionen av IoT Edge, se [uppdatera IoT Edge Security daemon och runtime](how-to-update-iot-edge.md).

Läs mer om hur du använder virtuella Windows-datorer i [virtuella Windows-datorer-dokumentationen](https://docs.microsoft.com/azure/virtual-machines/windows/).

Om du vill använda SSH i den här virtuella datorn efter installationen följer du [installationen av openssh för Windows Server](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell) -guide med fjärr skrivbord eller fjärr-PowerShell.
