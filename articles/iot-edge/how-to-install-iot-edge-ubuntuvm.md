---
title: Kör Azure IoT Edge på virtuella Ubuntu-datorer | Microsoft-dokument
description: Installationsinstruktioner för Azure IoT Edge för Ubuntu 18.04 LTS-virtuella datorer
author: toolboc
manager: veyalla
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: pdecarlo
ms.openlocfilehash: 64e2787aa282e75893fa34e6de1373e6afed09fe
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349601"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Kör Azure IoT Edge på virtuella Ubuntu-datorer

Azure IoT Edge-körningen är det som förvandlar en enhet till en IoT Edge-enhet. Körningen kan distribueras på enheter så små som en Raspberry Pi eller så stor som en industriell server. När en enhet har konfigurerats med IoT Edge-körningen kan du börja distribuera affärslogik till den från molnet.

Mer information om hur IoT Edge-körningen fungerar och vilka komponenter som ingår finns [i Förstå Azure IoT Edge-körningen och dess arkitektur](iot-edge-runtime.md).

I den här artikeln visas stegen för att distribuera en Ubuntu 18.04 LTS-virtuell dator med Azure IoT Edge-körningen installerad och konfigurerad med hjälp av en förhandad enhetsanslutningssträng. Distributionen utförs med hjälp av en [moln-init-baserad](../virtual-machines/linux/using-cloud-init.md
) [Azure Resource Manager-mall](../azure-resource-manager/templates/overview.md) som underhålls i projektdatabasen [iotedge-vm.](https://github.com/Azure/iotedge-vm-deploy)

Vid första start installerar den virtuella Ubuntu 18.04 [LTS-datorn den senaste versionen av Azure IoT Edge-körningen via cloud-init](https://github.com/Azure/iotedge-vm-deploy/blob/master/cloud-init.txt). Den ställer också in en medföljande anslutningssträng innan körningen startar, så att du enkelt kan konfigurera och ansluta IoT Edge-enheten utan att behöva starta en SSH- eller fjärrskrivbordssession. 

## <a name="deploy-using-deploy-to-azure-button"></a>Distribuera med knappen Distribuera till Azure

Med [knappen Distribuera till Azure](../azure-resource-manager/templates/deploy-to-azure-button.md) kan du använda en strömlinjeformad distribution av Azure Resource [Manager-mallar](../azure-resource-manager/templates/overview.md) som underhålls på GitHub.  Det här avsnittet visar användningen av knappen Distribuera till Azure som finns i projektdatabasen [iotedge-vm.](https://github.com/Azure/iotedge-vm-deploy)  


1. Vi distribuerar en Azure IoT Edge-aktiverad Linux-virtuell dator med iotedge-vm-deploy Azure Resource Manager-mallen.  Börja med att klicka på knappen nedan:

    [![Distribuera till Azure-knappen för iotedge-vm-deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

1. I det nyligen lanserade fönstret fyller du i de tillgängliga formulärfälten:

    > [!div class="mx-imgBorder"]
    > [![Skärmbild som visar iotedge-vm-deploy-mallen](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **Prenumeration:** Den aktiva Azure-prenumerationen som ska distribuera den virtuella datorn till.

    **Resursgrupp**: En befintlig eller nyskapade resursgrupp som innehåller den virtuella datorn och den är associerade resurser.

    **DNS-etikettprefix:** Ett obligatoriskt värde som du väljer som används för att prefixa värdnamnet för den virtuella datorn.

    **Administratörsanvändarnamn**: Ett användarnamn som kommer att tillhandahållas rotbehörigheter vid distribution.

    **Device Connection String**: En [enhetsanslutningssträng](how-to-register-device.md) för en enhet som skapades i den avsedda [IoT Hub](../iot-hub/about-iot-hub.md).

    **VM-storlek:** [Storleken på](../cloud-services/cloud-services-sizes-specs.md) den virtuella datorn som ska distribueras

    **Ubuntu OS Version**: Den version av Ubuntu OS som ska installeras på basen virtuell maskin.

    **Plats**: Den [geografiska region](https://azure.microsoft.com/global-infrastructure/locations/) som den virtuella datorn ska distribueras till, det här värdet är som standard platsen för den valda resursgruppen.

    **Autentiseringstyp:** Välj **sshPublicKey** eller **lösenord** beroende på dina önskemål.

    **Admin Lösenord eller nyckel:** Värdet på SSH Public Key eller värdet av lösenordet beroende på valet av autentiseringstyp.

    När alla fält har fyllts i markerar du kryssrutan längst ned på sidan för att acceptera villkoren och väljer **Inköp** för att påbörja distributionen.

1. Kontrollera att distributionen har slutförts.  En resurs för virtuella datorer ska ha distribuerats till den valda resursgruppen.  Notera maskinnamnet, bör detta vara i `vm-0000000000000`formatet . Ta också del av det associerade **DNS-namnet** `<dnsLabelPrefix>`, som ska vara i formatet . `<location>`.cloudapp.azure.com.

    **DNS-namnet** kan erhållas från avsnittet **Översikt på** den nyligen distribuerade virtuella datorn i Azure-portalen.

    > [!div class="mx-imgBorder"]
    > [![Skärmdump som visar dns-namnet på iotedge-vm](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Om du vill SSH i den här virtuella datorn efter installationen använder du det associerade **DNS-namnet** med kommandot:`ssh <adminUsername>@<DNS_Name>`

## <a name="deploy-from-azure-cli"></a>Distribuera från Azure CLI

1. Kontrollera att du har installerat Azure CLI iot-tillägget med:
    ```azurecli-interactive
    az extension add --name azure-iot
    ```

1. Om du använder Azure CLI på skrivbordet börjar du sedan med att logga in:

   ```azurecli-interactive
   az login
   ```

1. Om du har flera prenumerationer väljer du den prenumeration du vill använda:
   1. Visa dina prenumerationer:

      ```azurecli-interactive
      az account list --output table
      ```

   1. Kopiera fältet SubscriptionID för den prenumeration som du vill använda.

   1. Ange din arbetsprenumeration med det ID som du kopierade:

      ```azurecli-interactive
      az account set -s <SubscriptionId>
      ```

1. Skapa en ny resursgrupp (eller ange en befintlig i nästa steg):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Skapa en ny virtuell dator:

    Om du vill `password`använda en **authenticationType** av läser du exemplet nedan:

   ```azurecli-interactive
   az group deployment create \
   --name edgeVm \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity show-connection-string --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_SECRET_PASSWORD>"
   ```

    Om du vill autentisera med en SSH-nyckel kan `sshPublicKey`du göra det genom att ange en **authenticationType** för och sedan ange värdet för SSH-nyckeln i parametern **adminPasswordOrKey.**  Ett exempel på detta visas nedan.

    ```azurecli-interactive
    #Generate the SSH Key
    ssh-keygen -m PEM -t rsa -b 4096 -q -f ~/.ssh/iotedge-vm-key -N ""  

    #Create a VM using the iotedge-vm-deploy script
    az group deployment create \
    --name edgeVm \
    --resource-group IoTEdgeResources \
    --template-uri "https://aka.ms/iotedge-vm-deploy" \
    --parameters dnsLabelPrefix='my-edge-vm1' \
    --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
    --parameters deviceConnectionString=$(az iot hub device-identity show-connection-string --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/iotedge-vm-key.pub)"
     
    ```

1. Kontrollera att distributionen har slutförts.  En resurs för virtuella datorer ska ha distribuerats till den valda resursgruppen.  Notera maskinnamnet, bör detta vara i `vm-0000000000000`formatet . Ta också del av det associerade **DNS-namnet** `<dnsLabelPrefix>`, som ska vara i formatet . `<location>`.cloudapp.azure.com.

    **DNS-namnet** kan erhållas från JSON-formaterade utdata från föregående steg, inom **utdataavsnittet** som en del av den **offentliga SSH-posten.**  Värdet för den här posten kan användas för att SSH till den nyligen distribuerade datorn.

    ```bash
    "outputs": {
      "public SSH": {
        "type": "String",
        "value": "ssh <adminUsername>@<DNS_Name>"
      }
    }
    ```

    **DNS-namnet** kan också hämtas från avsnittet **Översikt på** den nyligen distribuerade virtuella datorn i Azure-portalen.

    > [!div class="mx-imgBorder"]
    > [![Skärmdump som visar dns-namnet på iotedge-vm](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. Om du vill SSH i den här virtuella datorn efter installationen använder du det associerade **DNS-namnet** med kommandot:`ssh <adminUsername>@<DNS_Name>`

## <a name="next-steps"></a>Nästa steg

Nu när du har etablerat en IoT Edge-enhet med körningen installerad kan du [distribuera IoT Edge-moduler](how-to-deploy-modules-portal.md).

Om du har problem med att IoT Edge-körningen installeras korrekt kan du läsa [felsökningssidan.](troubleshoot.md)

Information om hur du uppdaterar en befintlig installation till den senaste versionen av IoT Edge finns i [Uppdatera säkerhetsdemonen och körningen i IoT Edge](how-to-update-iot-edge.md).

Om du vill öppna portar för åtkomst till den virtuella datorn via SSH eller andra inkommande anslutningar läser du azure Virtual Machines-dokumentationen om [hur du öppnar portar och slutpunkter till en Virtuell Linux-dator](../virtual-machines/linux/nsg-quickstart.md)
