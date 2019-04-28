---
title: Verifiera ett nav och ekrar nätverk med Terraform i Azure
description: Självstudie för att verifiera NAV och ekrar nätverkets topologi med alla virtuella nätverk är anslutna till varandra.
services: terraform
ms.service: azure
keywords: terraform, nav och ekrar, nätverk, hybrid-nätverk, devops, VM, azure, vnet-peering,
author: VaijanathB
manager: jeconnoc
ms.author: vaangadi
ms.topic: tutorial
ms.date: 03/01/2019
ms.openlocfilehash: 157be65a19a1f790b911aa9d861c5f18fc8c0813
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128271"
---
# <a name="tutorial-validate-a-hub-and-spoke-network-with-terraform-in-azure"></a>Självstudier: Verifiera ett nav och ekrar nätverk med Terraform i Azure

I den här artikeln kan du köra terraform-filerna som skapades i föregående artikel i den här serien. Resultatet är en verifiering av anslutning mellan de virtuella nätverken för demonstration.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Använd HCL (HashiCorp Language) för att implementera den Hubbnätverk i topologin av typen hub-spoke
> * Använd Terraform prenumerationen för att verifiera resurserna som ska distribueras
> * Använd Terraform ansöka om för att skapa resurser i Azure
> * Kontrollera anslutningen mellan olika nätverk
> * Använd Terraform till att ta bort alla resurser

## <a name="prerequisites"></a>Nödvändiga komponenter

1. [Skapa ett nav och ekrar hybrid nätverkets topologi med Terraform i Azure](./terraform-hub-spoke-introduction.md).
1. [Skapa en lokal virtuellt nätverk med Terraform i Azure](./terraform-hub-spoke-on-prem.md).
1. [Skapa ett virtuellt nätverk hub med Terraform i Azure](./terraform-hub-spoke-hub-network.md).
1. [Skapa en virtuell nätverksinstallation hub med Terraform i Azure](./terraform-hub-spoke-hub-nva.md).
1. [Skapa en eker virtuella nätverk med Terraform i Azure](./terraform-hub-spoke-spoke-network.md).

## <a name="verify-your-configuration"></a>Kontrollera konfigurationen

När du har slutfört den [krav](#prerequisites), kontrollera att rätt konfigurationsfiler finns.

1. Bläddra till [Azure-portalen](https://portal.azure.com).

1. Öppna [Azure Cloud Shell](/azure/cloud-shell/overview). Om du inte har valt en miljö tidigare väljer du **Bash** som miljö.

    ![Cloud Shell-kommandotolk](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. Ändra sökvägen till katalogen `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Ändra sökvägen till den nya katalogen:

    ```bash
    cd hub-spoke
    ```

1. Kör den `ls` kommando för att kontrollera att den `.tf` konfigurationsfiler som skapats i de föregående självstudierna visas:

    ![Konfigurationsfiler för Terraform-demo](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-config-files.png)

## <a name="deploy-the-resources"></a>Distribuera resurserna

1. Initiera Terraform-providern:
    
    ```bash
    terraform init
    ```
    
    ![Exempel på resultat av kommandot ”terraform init”](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-init.png)
    
1. Kör den `terraform plan` kommando för att se effekten av distributionen innan körningen:

    ```bash
    terraform plan
    ```
    
    ![Exempel på resultat av kommandot ”terraform plan”](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-plan.png)

1. Distribuera lösningen:

    ```bash
    terraform apply
    ```
    
    Ange `yes` när du uppmanas att bekräfta distribution.

    ![Exempel på resultat från ”terraform gäller” kommandot](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-apply.png)
    
## <a name="test-the-hub-vnet-and-spoke-vnets"></a>Testa det virtuella hubbnätverket och virtuella ekernätverk

Det här avsnittet visar hur du testa anslutningen från den simulerade lokala miljön till det virtuella hubbnätverket.

1. I Azure-portalen bläddrar du till den **onprem-vnet-rg** resursgrupp.

1. I den **onprem-vnet-rg** väljer du den virtuella datorn med namnet **onprem-vm**.

1. Välj **Anslut**.

1. Bredvid texten **logga in med lokalt konto för virtuell dator**, kopiera den **ssh** kommandot till Urklipp.

1. Från Kommandotolken i Linux, kör `ssh` att ansluta till den simulerade lokala miljön. Använd lösenordet som angavs i den `on-prem.tf` parameterfilen.

1. Kör den `ping` kommando för att testa anslutningen till jumpbox-datorn i det virtuella hubbnätverket:

   ```bash
   ping 10.0.0.68
   ```

1. Kör den `ping` kommando för att testa anslutningen till jumpboxen virtuella datorer i varje eker:

   ```bash
   ping 10.1.0.68
   ping 10.2.0.68
   ```

1. Avsluta den ssh-session på den **onprem-vm** virtuell dator, ange `exit` och tryck på &lt;RETUR >.

## <a name="troubleshoot-vpn-issues"></a>Felsöka problem med VPN

Information om hur du löser fel VPN finns i artikeln [felsöka hybrid VPN-anslutning](/azure/architecture/reference-architectures/hybrid-networking/troubleshoot-vpn).

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs kan du ta bort resurserna som skapas i den här självstudieserien.

1. Ta bort de resurser som har deklarerats i planen:

    ```bash
    terraform destroy
    ```

    Ange `yes` när du uppmanas att bekräfta borttagningen av resurser.

1. Ändra sökvägen till den överordnade katalogen:

    ```bash
    cd ..
    ```

1. Ta bort den `hub-scope` katalogen (inklusive alla dess filer):

    ```bash
    rm -r hub-spoke
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Läs mer om hur du använder Terraform i Azure](/azure/terraform)