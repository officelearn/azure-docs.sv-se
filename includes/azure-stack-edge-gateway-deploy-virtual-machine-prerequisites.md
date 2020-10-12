---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/31/2020
ms.author: alkohli
ms.openlocfilehash: 3a17e73c66c2296cc36b24e3b0a8abfcab00e46a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89419423"
---
Innan du kan distribuera virtuella datorer på din Azure Stack Edge-enhet måste du konfigurera klienten så att den ansluter till enheten via Azure Resource Manager över Azure PowerShell. Detaljerade anvisningar finns i [ansluta till Azure Resource Manager på din Azure Stack Edge-enhet](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md).


Kontrol lera att följande steg kan användas för att komma åt enheten från klienten (du har gjort den här konfigurationen när du ansluter till Azure Resource Manager, verifierar du bara att konfigurationen lyckades.): 

1. Verifiera att Azure Resource Manager kommunikation fungerar. Ange:     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. Anropa lokala enhets-API: er för autentisering. Ange: 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Ange användar namnet *EdgeARMuser* och lösen ordet för att ansluta via Azure Resource Manager.

1. Om du har konfigurerat **Compute** för Kubernetes kan du hoppa över det här steget. Fortsätt till att kontrol lera att du har aktiverat ett nätverks gränssnitt för beräkning. I lokalt användar gränssnitt går du till **beräknings** inställningar. Välj det nätverks gränssnitt som du ska använda för att skapa en virtuell växel. De virtuella datorer som du skapar kommer att kopplas till en virtuell växel som är kopplad till den här porten och det associerade nätverket. Se till att välja ett nätverk som matchar IP-adressen som du vill använda för den virtuella datorn.  

    ![Aktivera beräknings inställningar 1](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

    Aktivera beräkning i nätverks gränssnittet. Azure Stack Edge skapar och hanterar en virtuell växel som motsvarar det nätverks gränssnittet. Ange inte vissa IP-adresser för Kubernetes just nu. Det kan ta flera minuter att aktivera beräkning.

    <!--If you decide to use another network interface for compute, make sure that you:
    
    - Delete all the VMs that you have deployed using Azure Resource Manager.
    
    - Delete all virtual network interfaces and the virtual network associated with this network interface. 
    
    - You can now enable another network interface for compute.-->

<!--1. You may also need to configure TLS 1.2 on your client machine if running older versions of AzCopy.--> 

