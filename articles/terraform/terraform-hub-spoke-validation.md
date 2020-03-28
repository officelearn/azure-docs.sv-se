---
title: Självstudiekurs - Validera ett nav- och ekernätverk i Azure med Terraform
description: Självstudiekurs för att validera hubb- och ekernätverkstopologi med alla virtuella nätverk som är anslutna till varandra.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 9ba4780c59e5e9da4999573abbc08ecd2738a2cd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74159199"
---
# <a name="tutorial-validate-a-hub-and-spoke-network-in-azure-using-terraform"></a>Självstudiekurs: Validera ett nav- och ekernätverk i Azure med Terraform

I den här artikeln kör du terraformfilerna som skapats i föregående artikel i den här serien. Resultatet är en validering av anslutningen mellan de virtuella demonstrationsnätverken.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Använd HCL (HashiCorp Language) för att implementera Hub VNet i hub-spoke-topologi
> * Använd Terraform-planen för att verifiera de resurser som ska distribueras
> * Använd Terraform tillämpa för att skapa resurser i Azure
> * Verifiera anslutningen mellan olika nätverk
> * Använd Terraform för att förstöra alla resurser

## <a name="prerequisites"></a>Krav

1. [Skapa en hubb- och ekerhybridnätverkstopologi med Terraform i Azure](./terraform-hub-spoke-introduction.md).
1. [Skapa lokalt virtuellt nätverk med Terraform i Azure](./terraform-hub-spoke-on-prem.md).
1. [Skapa ett virtuellt navnätverk med Terraform i Azure](./terraform-hub-spoke-hub-network.md).
1. [Skapa en virtuell hubbnätverksinstallation med Terraform i Azure](./terraform-hub-spoke-hub-nva.md).
1. [Skapa ett ekervirtet nätverk med Terraform i Azure](./terraform-hub-spoke-spoke-network.md).

## <a name="verify-your-configuration"></a>Verifiera konfigurationen

När du har slutfört [förutsättningarna](#prerequisites)kontrollerar du att lämpliga konfigurationsfiler finns.

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

1. Kör `ls` kommandot för att `.tf` kontrollera att de konfigurationsfiler som skapats i föregående självstudier visas:

    ![Terraform demo config filer](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-config-files.png)

## <a name="deploy-the-resources"></a>Distribuera resurserna

1. Initiera Terraform-leverantören:
    
    ```bash
    terraform init
    ```
    
    ![Exempel på resultat av kommandot "terraform init"](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-init.png)
    
1. Kör `terraform plan` kommandot för att se effekten av distributionen före körning:

    ```bash
    terraform plan
    ```
    
    ![Exempel på resultat av kommandot "terraform plan"](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-plan.png)

1. Distribuera lösningen:

    ```bash
    terraform apply
    ```
    
    Ange `yes` när du uppmanas att bekräfta distributionen.

    ![Exempel på resultat av kommandot "terraform apply"](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-apply.png)
    
## <a name="test-the-hub-vnet-and-spoke-vnets"></a>Testa hubbnätet och eker-virtuella nätverk

Det här avsnittet visar hur du testar anslutningen från den simulerade lokala miljön till navet VNet.

1. Bläddra till resursgruppen **onprem-vnet-rg** i Azure-portalen.

1. På fliken **onprem-vnet-rg** väljer du den virtuella datorn som heter **onprem-vm**.

1. Välj **Anslut**.

1. Bredvid texten **Logga in med vm-lokalt konto**kopierar du kommandot **ssh** till Urklipp.

1. Från en Linux-prompt kör du `ssh` för att ansluta till den simulerade lokala miljön. Använd det lösenord som `on-prem.tf` anges i parameterfilen.

1. Kör `ping` kommandot för att testa anslutningen till den virtuella jumpbox-datorn i navet VNet:

   ```bash
   ping 10.0.0.68
   ```

1. Kör `ping` kommandot för att testa anslutningen till virtuella jumpbox-datorer i varje eker:

   ```bash
   ping 10.1.0.68
   ping 10.2.0.68
   ```

1. Om du vill avsluta ssh-sessionen på den `exit` virtuella &lt;datorn **onprem-vm** anger du och trycker på Retur>.

## <a name="troubleshoot-vpn-issues"></a>Felsöka VPN-problem

Information om hur du löser VPN-fel finns i artikeln [Felsöka en hybrid-VPN-anslutning](/azure/architecture/reference-architectures/hybrid-networking/troubleshoot-vpn).

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs tar du bort de resurser som skapas i självstudieserien.

1. Ta bort de resurser som deklarerats i planen:

    ```bash
    terraform destroy
    ```

    Ange `yes` när du uppmanas att bekräfta borttagningen av resurserna.

1. Ändra kataloger till den överordnade katalogen:

    ```bash
    cd ..
    ```

1. Ta `hub-scope` bort katalogen (inklusive alla dess filer):

    ```bash
    rm -r hub-spoke
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Läs mer om hur du använder Terraform i Azure](/azure/terraform)