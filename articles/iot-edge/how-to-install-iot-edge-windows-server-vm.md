---
title: Kör Azure IoT Edge på virtuella Datorer med Windows Server | Microsoft-dokument
description: Installationsinstruktioner för Azure IoT Edge på virtuella datorer i Windows Server Marketplace
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: philmea
ms.openlocfilehash: 5f88a21efd04c9dd24fe31e925a3b911b5ec9df2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77045907"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>Köra Azure IoT Edge på virtuella Windows Server-datorer

Azure IoT Edge-körningen är det som förvandlar en enhet till en IoT Edge-enhet. Körningen kan distribueras på enheter så små som en Raspberry Pi eller så stor som en industriell server. När en enhet har konfigurerats med IoT Edge-körningen kan du börja distribuera affärslogik till den från molnet.

Mer information om hur IoT Edge-körningen fungerar och vilka komponenter som ingår finns [i Förstå Azure IoT Edge-körningen och dess arkitektur](iot-edge-runtime.md).

I den här artikeln visas stegen för att köra Azure IoT Edge-körningen på en virtuell Windows Server 2019-dator med hjälp av Erbjudandet för [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) Azure Marketplace. Följ instruktionerna på [Installera Azure IoT Edge-körningen](how-to-install-iot-edge-windows.md) i Windows för användning med andra versioner.

## <a name="deploy-from-the-azure-marketplace"></a>Distribuera från Azure Marketplace

1. Navigera till [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) Azure Marketplace-erbjudandet eller genom att söka i "Windows Server" på Azure [Marketplace](https://azuremarketplace.microsoft.com/)
2. Välj **HÄMTA DEN NU**
3. I **Software plan**hittar du "Windows Server 2019 Datacenter Server Core med behållare" och väljer sedan **Fortsätt** i nästa dialogruta.
    * Du kan också använda dessa instruktioner för andra versioner av Windows Server med behållare
4. En gång i Azure-portalen väljer du **Skapa** och följer guiden för att distribuera den virtuella datorn.
    * Om det är första gången du provar en virtuell dator är det enklast att använda ett lösenord och aktivera RDP och SSH på menyn för inkommande inkommande användare.
    * Om du har en resursintensiv arbetsbelastning bör du uppgradera storleken på den virtuella datorn genom att lägga till fler processorer och/eller minne.
5. När den virtuella datorn har distribuerats konfigurerar du den så att den ansluter till din IoT Hub:
    1. Kopiera enhetens anslutningssträng från IoT Edge-enheten som skapats i IoT Hub. Se proceduren [Hämta anslutningssträngen i Azure-portalen](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal).
    1. Välj din nyskapade resurs för virtuella datorer från Azure-portalen och öppna **kommandoalternativet kör**
    1. Välj alternativet **RunPowerShellScript**
    1. Kopiera skriptet till kommandofönstret med enhetsanslutningssträngen:

        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```

    1. Kör skriptet för att installera IoT Edge-körningen och ange anslutningssträngen genom att välja **Kör**
    1. Efter en minut eller två bör du se ett meddelande om att Edge-körningen har installerats och etablerats.

## <a name="deploy-from-the-azure-portal"></a>Distribuera från Azure-portalen

1. Sök efter "Windows Server" från Azure-portalen och välj **Windows Server 2019 Datacenter** för att starta arbetsflödet för att skapa virtuella datorer.
2. Välj "Windows Server 2019 Datacenter Server Core med behållare" från **Välj en programvaruplan** och välj sedan **Skapa**
3. Fyll i steg 5 i instruktionerna "Distribuera från Azure Marketplace" ovan.

## <a name="deploy-from-azure-cli"></a>Distribuera från Azure CLI

1. Om du använder Azure CLI på skrivbordet börjar du med att logga in:

   ```azurecli-interactive
   az login
   ```

1. Om du har flera prenumerationer väljer du den prenumeration du vill använda:
   1. Visa dina prenumerationer:

      ```azurecli-interactive
      az account list --output table
      ```

   1. Kopiera fältet SubscriptionID för den prenumeration du vill använda
   1. Kör det här kommandot med det ID som du kopierade:

      ```azurecli-interactive
      az account set -s {SubscriptionId}
      ```

1. Skapa en ny resursgrupp (eller ange en befintlig i nästa steg):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Skapa en ny virtuell dator:

   ```azurecli-interactive
   az vm create -g IoTEdgeResources -n EdgeVM --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter-Core-with-Containers:latest  --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```

   * Det här kommandot kommer att fråga dig om `--admin-password` ett lösenord, men du kan lägga till alternativet för att ställa in det lättare i ett skript
   * Windows Server Core-avbildningen har endast stöd för kommandoraden med fjärrskrivbord, så om du vill ha hela skrivbordsupplevelsen anger du `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest` som avbildning

1. Ange enhetsanslutningssträngen (Du kan följa [anslutningssträngen med Azure CLI-proceduren](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli) om du inte är bekant med den här processen):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>Nästa steg

Nu när du har etablerat en IoT Edge-enhet med körningen installerad kan du [distribuera IoT Edge-moduler](how-to-deploy-modules-portal.md).

Om du har problem med att Edge-körningen installeras korrekt kan du läsa [felsökningssidan.](troubleshoot.md)

Information om hur du uppdaterar en befintlig installation till den senaste versionen av IoT Edge finns i [Uppdatera säkerhetsdemonen och körningen i IoT Edge](how-to-update-iot-edge.md).

Läs mer om hur du använder virtuella Windows-datorer i [dokumentationen till Windows virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/windows/).

Om du vill SSH i den här virtuella datorn efter installationen följer du [guiden Installera OpenSSH för Windows Server](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell) med fjärrskrivbord eller fjärrkraftskal.
