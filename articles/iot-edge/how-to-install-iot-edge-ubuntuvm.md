---
title: Kör Azure IoT Edge på Ubuntu-datorer | Microsoft Docs
description: Konfigurationsanvisningar för Azure IoT Edge på Ubuntu 16.04 Azure Marketplace-datorer
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: gregman
ms.openlocfilehash: 8275bceca1a18f49eb7eeece66a3866d77c47635
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67796160"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Kör Azure IoT Edge på Ubuntu-datorer

Azure IoT Edge-körningen är vad omvandlar en enhet till en IoT Edge-enhet. Körningen kan distribueras på enheter som är så litet som en Raspberry Pi eller stora som industriella-server. När en enhet konfigureras med IoT Edge-körningen, kan du börja distribuera affärslogik till den från molnet.

Läs mer om hur IoT Edge-körningen fungerar och vilka komponenter som ingår i [förstå Azure IoT Edge-körningen och dess arkitektur](iot-edge-runtime.md).

Den här artikeln visar hur du kör Azure IoT Edge-körningen på en Ubuntu 16.04-dator med hjälp av den förkonfigurerade [Azure IoT Edge på Ubuntu Azure Marketplace-erbjudande](https://aka.ms/azure-iot-edge-ubuntuvm). 

Vid den första starten förinstalleras Azure IoT Edge på Ubuntu VM den senaste versionen av Azure IoT Edge-körningen. Den innehåller också ett skript för att ange anslutningssträngen och starta sedan om körning, som kan utlösas via fjärranslutning via Virtuella Azure-portalen eller Azure-kommandorad, så att du kan enkelt konfigurera och ansluta IoT Edge-enhet utan att starta en SSH- eller fjärranslutna skrivbordssession. Det här skriptet ska vänta med att ange anslutningssträngen tills IoT Edge-klienten har installerats helt så att du inte behöver byggs in ditt automation.

## <a name="deploy-from-the-azure-marketplace"></a>Distribuera från Azure Marketplace
1.  Navigera till den [Azure IoT Edge på Ubuntu](https://aka.ms/azure-iot-edge-ubuntuvm) Marketplace-erbjudandet eller genom att söka ”Azure IoT Edge på Ubuntu” [Azure Marketplace](https://azuremarketplace.microsoft.com/)
2.  Välj **hämta IT nu** och sedan **Fortsätt** på nästa dialogruta.
3.  En gång i Azure-portalen väljer **skapa** och Följ guiden för att distribuera den virtuella datorn. 
    *   Om det är första gången du provar på att använda en virtuell dator, är det enklast att använda ett lösenord och aktivera SSH på menyn offentliga inkommande portar. 
    *   Om du har en resurs-intensiv arbetsbelastning, bör du uppgradera storleken på virtuella datorn genom att lägga till fler processorer eller minne.
4.  När den virtuella datorn distribueras kan du konfigurera den kan ansluta till din IoT-hubb:
    1.  Kopiera enhetsanslutningssträngen från din IoT Edge-enhet som skapats i din IoT-hubb (du kan följa den [registrera en ny Azure IoT Edge-enhet från Azure portal](how-to-register-device-portal.md) här guiden om du inte är bekant med den här processen)
    1.  Välj din resurs för nya virtuella datorn från Azure-portalen och öppna den **Körningskommando** alternativet
    1.  Välj den **RunShellScript** alternativet
    1.  Kör skriptet nedan via kommandofönstret med enhetens anslutningssträng: `/etc/iotedge/configedge.sh “{device_connection_string}”`
    1.  Välj **kör**
    1.  Vänta en stund och skärmen bör ange ett meddelande som anger strängen som angavs.


## <a name="deploy-from-the-azure-portal"></a>Distribuera från Azure portal
Sök efter ”Azure IoT Edge” från Azure-portalen och välj **Ubuntu Server 16.04 LTS + Azure IoT Edge-körningen** att börja skapa arbetsflöde för virtuell dator. Därifrån kan du slutföra steg 3 och 4 i ”Distribuera från the Azure Marketplace” anvisningarna ovan.

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
    
   1. Kopiera prenumerations-ID-fält för den prenumeration som du vill använda.

   1. Ställ in prenumerationen arbeta med det ID som du nyss kopierade:
    
      ```azurecli-interactive 
      az account set -s {SubscriptionId}
      ```
    
1. Skapa en ny resursgrupp (eller ange ett befintligt namn i nästa steg):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Godkänn användningsvillkoren för den virtuella datorn. Om du vill granska villkoren först, följer du stegen i [distribuera från Azure Marketplace](#deploy-from-the-azure-marketplace).

   ```azurecli-interactive
   az vm image accept-terms --urn microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest
   ```

1. Skapa en ny virtuell dator:

   ```azurecli-interactive
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys
   ```

1. Ange anslutningssträngen för enheten (du kan följa den [registrera en ny Azure IoT Edge-enhet med Azure CLI](how-to-register-device-cli.md) här guiden om du inte är bekant med den här processen):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script "/etc/iotedge/configedge.sh '{device_connection_string}'"
   ```

Om du vill att SSH till den här virtuella datorn efter installationen, använder du publicIpAddress med kommandot: `ssh azureuser@{publicIpAddress}`


## <a name="next-steps"></a>Nästa steg

Nu när du har en IoT Edge-enhet med den som är installerad kan du [distribuera IoT Edge-moduler](how-to-deploy-modules-portal.md).

Om du har problem med IoT Edge-körningen installeras korrekt kan du kolla den [felsökning](troubleshoot.md) sidan.

Om du vill uppdatera en befintlig installation till den senaste versionen av IoT Edge, se [uppdatera IoT Edge security daemon och runtime](how-to-update-iot-edge.md).
