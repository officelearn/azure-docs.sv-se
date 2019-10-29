---
title: Självstudie – validera ett nav-och eker-nätverk i Azure med terraform
description: Självstudie för att verifiera nätverk sto pol Ogin för nav och ekrar med alla virtuella nätverk som är anslutna till varandra.
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: b0b761fcd79f7129befefa37ce11d9c70cf7cb96
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969342"
---
# <a name="tutorial-validate-a-hub-and-spoke-network-in-azure-using-terraform"></a>Självstudie: validera ett nav-och eker-nätverk i Azure med terraform

I den här artikeln kör du terraform-filerna som skapades i föregående artikel i den här serien. Resultatet är en validering av anslutningen mellan virtuella demo nätverk.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Använd HCL (HashiCorp Language) för att implementera hubbens VNet i hubben för nav-eker
> * Använd terraform plan för att kontrol lera vilka resurser som ska distribueras
> * Använd terraform Använd för att skapa resurser i Azure
> * Kontrol lera anslutningen mellan olika nätverk
> * Använd terraform för att förstöra alla resurser

## <a name="prerequisites"></a>Krav

1. [Skapa en nav-och eker hybrid nätverkstopologi med terraform i Azure](./terraform-hub-spoke-introduction.md).
1. [Skapa lokalt virtuellt nätverk med terraform i Azure](./terraform-hub-spoke-on-prem.md).
1. [Skapa ett virtuellt hubb nätverk med terraform i Azure](./terraform-hub-spoke-hub-network.md).
1. [Skapa en hubb för virtuella nätverk med terraform i Azure](./terraform-hub-spoke-hub-nva.md).
1. [Skapa ett eker-virtuellt nätverk med terraform i Azure](./terraform-hub-spoke-spoke-network.md).

## <a name="verify-your-configuration"></a>Verifiera konfigurationen

När du har slutfört [kraven](#prerequisites)kontrollerar du att rätt konfigurationsfiler finns.

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

1. Kör kommandot `ls` för att kontrol lera att de `.tf` config-filerna som skapades i de föregående självstudierna visas:

    ![Konfigurationsfiler för terraform-demonstration](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-config-files.png)

## <a name="deploy-the-resources"></a>Distribuera resurserna

1. Initiera terraform-providern:
    
    ```bash
    terraform init
    ```
    
    ![Exempel på resultat av kommandot "terraform init"](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-init.png)
    
1. Kör kommandot `terraform plan` för att se resultatet av distributionen före körningen:

    ```bash
    terraform plan
    ```
    
    ![Exempel på resultat av kommandot "terraform plan"](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-plan.png)

1. Distribuera lösningen:

    ```bash
    terraform apply
    ```
    
    Ange `yes` när du uppmanas att bekräfta distributionen.

    ![Exempel på resultat av kommandot "terraform Apply"](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-apply.png)
    
## <a name="test-the-hub-vnet-and-spoke-vnets"></a>Testa hubben VNet och ekrar virtuella nätverk

Det här avsnittet visar hur du testar anslutningen från den simulerade lokala miljön till hubbens VNet.

1. I Azure Portal bläddrar du till resurs gruppen **OnPrem-VNet-RG** .

1. På fliken **OnPrem-VNet-RG** väljer du den virtuella datorn med namnet **OnPrem-VM**.

1. Välj **Anslut**.

1. Kopiera **SSH** -kommandot till Urklipp bredvid text **inloggningen med hjälp av lokalt konto för virtuell dator**.

1. Kör `ssh` för att ansluta till den simulerade lokala miljön från en Linux-prompt. Använd lösen ordet som anges i `on-prem.tf` parameter filen.

1. Kör kommandot `ping` för att testa anslutningen till den virtuella hopp datorn i hubbens VNet:

   ```bash
   ping 10.0.0.68
   ```

1. Kör kommandot `ping` för att testa anslutningen till de virtuella datorerna i det virtuella nätverket i varje ekrar:

   ```bash
   ping 10.1.0.68
   ping 10.2.0.68
   ```

1. Om du vill avsluta SSH-sessionen på den virtuella datorn **OnPrem** anger du `exit` och trycker på &lt;RETUR >.

## <a name="troubleshoot-vpn-issues"></a>Felsöka VPN-problem

Information om hur du löser VPN-fel finns i artikeln [Felsöka en hybrid VPN-anslutning](/azure/architecture/reference-architectures/hybrid-networking/troubleshoot-vpn).

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort de resurser som skapats i själv studie serien när de inte längre behövs.

1. Ta bort de resurser som har deklarerats i planen:

    ```bash
    terraform destroy
    ```

    Ange `yes` när du uppmanas att bekräfta borttagningen av resurserna.

1. Ändra kataloger till den överordnade katalogen:

    ```bash
    cd ..
    ```

1. Ta bort `hub-scope`-katalogen (inklusive alla filer):

    ```bash
    rm -r hub-spoke
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Lär dig mer om hur du använder terraform i Azure](/azure/terraform)