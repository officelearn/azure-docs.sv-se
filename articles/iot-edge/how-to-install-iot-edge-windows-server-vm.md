---
title: Kör Azure IoT Edge på Windows Server-datorer | Microsoft Docs
description: Konfigurationsanvisningar för Azure IoT Edge på Windows Server Marketplace-datorer
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/29/2019
ms.author: gregman
ms.openlocfilehash: be7479d3f042d6e64428a07e0509907b78595200
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159787"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>Kör Azure IoT Edge på Windows Server-datorer
Azure IoT Edge-körningen är vad omvandlar en enhet till en IoT Edge-enhet. Körningen kan distribueras på enheter som är så litet som en Raspberry Pi eller stora som industriella-server. När en enhet konfigureras med IoT Edge-körningen, kan du börja distribuera affärslogik till den från molnet.

Läs mer om hur IoT Edge-körningen fungerar och vilka komponenter som ingår i [förstå Azure IoT Edge-körningen och dess arkitektur](iot-edge-runtime.md).

Den här artikeln innehåller steg för att köra Azure IoT Edge-körningen på en Windows Server 2019 virtuell dator med den [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer?tab=Overview) Azure Marketplace-erbjudande. Följ anvisningarna på [installera Azure IoT Edge-körningen](how-to-install-iot-edge-windows.md) på Windows för användning med andra versioner.

> [!NOTE]
> IoT Edge-körningen på Windows Server finns i [förhandsversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="deploy-from-the-azure-marketplace"></a>Distribuera från Azure Marketplace
1.  Navigera till den [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer?tab=Overview) Azure Marketplace-erbjudande eller genom att söka ”Windows Server” [Azure Marketplace](https://azuremarketplace.microsoft.com/)
2.  Välj **Hämta nu** 
3.  I **programvaruplanen**, hitta ”Windows Server 2019 Datacenter Server Core med behållare” och välj sedan **Fortsätt** på nästa dialogruta.
    * Du kan också använda de här instruktionerna för övriga versioner av Windows Server med behållare
4.  En gång i Azure-portalen väljer **skapa** och Följ guiden för att distribuera den virtuella datorn. 
    *   Om det är första gången du provar på att använda en virtuell dator, är det enklast att använda ett lösenord och aktivera RDP och SSH på menyn offentliga inkommande portar. 
    *   Om du har en resurs-intensiv arbetsbelastning, bör du uppgradera storleken på virtuella datorn genom att lägga till fler processorer eller minne.
5.  När den virtuella datorn distribueras kan du konfigurera den kan ansluta till din IoT-hubb:
    1.  Kopiera enhetsanslutningssträngen från din IoT Edge-enhet som skapats i din IoT-hubb (du kan följa den [registrera en ny Azure IoT Edge-enhet från Azure portal](how-to-register-device-portal.md) här guiden om du inte är bekant med den här processen)
    1.  Välj din resurs för nya virtuella datorn från Azure-portalen och öppna den **Körningskommando** alternativet
    1.  Välj den **RunPowerShellScript** alternativet
    1.  Kopiera det här skriptet till kommandofönstret med enhetens anslutningssträng: 
        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```
    1.  Kör skriptet för att installera Edge-körningen och ange din anslutningssträng genom att välja **kör**
    1.  Efter en minut eller två, bör du se ett meddelande att Edge-körning var installerad och har etablerats.


## <a name="deploy-from-the-azure-portal"></a>Distribuera från Azure portal
1. Sök efter ”Windows Server” från Azure-portalen och välj **Windows Server 2019 Datacenter** att börja skapa arbetsflöde för virtuell dator. 
2. Från **Välj en plan för programvara** Välj ”Windows Server 2019 Datacenter Server Core med behållare” och välj **skapa**
3. Slutför steg 5 i ”Distribuera från the Azure Marketplace” anvisningarna ovan.

## <a name="deploy-from-azure-cli"></a>Distribuera från Azure CLI
1. Om du använder Azure CLI på skrivbordet, starta genom att logga in:

   ```azurecli-interactive
   az login
   ```
    
1. Om du har flera prenumerationer väljer du den prenumeration som du vill använda:
   1. Lista över dina prenumerationer:
    
      ```azurecli-interactive
      az account list --output table
      ```
    
   1. Kopiera prenumerations-ID-fält för den prenumeration som du vill använda
   1. Kör det här kommandot med det ID som du kopierade:
    
      ```azurecli-interactive 
      az account set -s {SubscriptionId}
      ```
    
1. Skapa en ny resursgrupp (eller ange ett befintligt namn i nästa steg):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```
    
1. Skapa en ny virtuell dator:

   ```azurecli-interactive
   az vm create -g IoTEdgeResources -n EdgeVM --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter-Core-with-Containers:latest  --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```
   * Det här kommandot uppmanar dig för ett lösenord, men du kan lägga till alternativet `--admin-password` ange den enklare i ett skript
   * Windows Server Core-avbildningen har kommandot rad support endast med fjärrskrivbord, så om du vill ha fullständig Skrivbordsmiljö, ange `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest` som bilden

1. Ange anslutningssträngen för enheten (du kan följa den [registrera en ny Azure IoT Edge-enhet med Azure CLI](how-to-register-device-cli.md) här guiden om du inte är bekant med den här processen):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>Nästa steg

Nu när du har en IoT Edge-enhet med den som är installerad kan du [distribuera IoT Edge-moduler](how-to-deploy-modules-portal.md).

Om du har problem med Edge-körningen installeras korrekt Kolla den [felsökning](troubleshoot.md) sidan.

Om du vill uppdatera en befintlig installation till den senaste versionen av IoT Edge, se [uppdatera IoT Edge security daemon och runtime](how-to-update-iot-edge.md).

Läs mer om hur du använder Windows-datorer på den [dokumentation för Windows-datorer](https://docs.microsoft.com/azure/virtual-machines/windows/).

Om du vill att SSH till den här virtuella datorn efter installationen, följer du de [Installation av OpenSSH för Windows Server](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell) hjälper med fjärr skrivbord eller fjärr-powershell.
