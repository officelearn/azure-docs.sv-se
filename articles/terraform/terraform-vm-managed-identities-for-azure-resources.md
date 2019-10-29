---
title: Självstudie – Skapa en virtuell Linux-dator med en hanterad identitet från Azure Marketplace-avbildningen med terraform
description: Skapa en virtuell terraform Linux-dator med en hanterad identitets-och fjärrhantering med hjälp av Azure Marketplace-avbildningen
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: e00b674df35516da559339af8028c1ca1845b0db
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969793"
---
# <a name="tutorial-create-a-linux-vm-with-a-managed-identity-from-the-azure-marketplace-image-using-terraform"></a>Självstudie: skapa en virtuell Linux-dator med en hanterad identitet från Azure Marketplace-avbildningen med terraform

I den här artikeln visas hur du använder en [Terraform Marketplace-avbildning](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview) till att skapa en virtuell Ubuntu Linux-dator (16.04 LTS) med den senaste [Terraform](https://www.terraform.io/intro/index.html)-versionen installerad och konfigurerad med [hanterade identiteter för Azure-resurser](/azure/active-directory/managed-service-identity/overview). Den här avbildningen konfigurerar även en fjärrserverdel för att aktivera [Remote State](https://www.terraform.io/docs/state/remote.html)-hantering med Terraform. 

Terraform Marketplace-avbildningen gör det enkelt att komma igång med Terraform på Azure, utan att behöva installera och konfigurera Terraform-manuellt. 

Det tillkommer inga programvaruavgifter för den Terraform VM-avbildningen. Du betalar bara avgifterna för Azure-maskinvaran baserat på den etablerade VM-storleken. 

Mer information om beräknings avgifterna finns på sidan med [pris](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)information för Linux-datorer.

## <a name="prerequisites"></a>Krav
Innan du kan skapa en virtuell Linux terraform-dator måste du ha en Azure-prenumeration. Se [Skapa ett kostnadsfritt Azure-konto i dag](https://azure.microsoft.com/free/) om du inte redan har ett konto.  

## <a name="create-your-terraform-vm"></a>Skapa din virtuella terraform-dator 

Här följer stegen för att skapa en instans av en virtuell Linux terraform-dator: 

1. Gå till listan [Skapa en resurs](https://ms.portal.azure.com/#create/hub) i Azure Portal.

1. I sökfältet **Sök på Marketplace** söker du efter **Terraform**. 

1. Välj **create**. 

1. I följande avsnitt finns indata för vart och ett av stegen i guiden för att skapa den virtuella terraform Linux-datorn. I följande avsnitt listas de indata som behövs för att konfigurera var och en av dessa steg.

## <a name="details-on-the-create-terraform-tab"></a>Information om fliken Skapa Terraform

Ange följande information på fliken **Skapa Terraform**:

1. **Grundläggande inställningar**
    
   * **Namn**: namnet på din virtuella terraform-dator.
   * **Användarnamn**: det första inloggnings-ID:t för kontot.
   * **Lösenord**: det första kontolösenordet. (Du kan använda en offentlig SSH-nyckel istället för ett lösenord.)
   * **Prenumeration**: den prenumeration som datorn ska skapas och faktureras på. Du måste ha behörighet att skapa resurser för prenumerationen.
   * **Resursgrupp**: en ny eller befintlig resursgrupp.
   * **Plats**: Datacentret som är mest lämpligt. Vanligtvis är det datacentret som har det mesta av dina data, eller en som är närmast din fysiska plats för snabbaste nätverksåtkomst.

2. **Ytterligare inställningar**

   * **Storlek**: den virtuella datorns storlek. 
   * **Typ av virtuell datordisk**: SSD eller HDD.

3. **Sammanfattning av Terraform**

   * Kontrollera att all information som du har angett är rätt. 

4. **Köp**

   * Inled etableringsprocessen genom att välja **Köp**. Det finns en länk till villkoren för transaktionen. Den virtuella datorn har inga ytterligare avgifter utöver beräkningen för den server storlek som du valde i steget storlek.

Terraform VM-avbildningen utför följande steg:

* Skapar en virtuell dator med systemtilldelad identitet utifrån Ubuntu 16.04 LTS-avbildningen.
* Installerar Managed identiteter för Azure-resursers tillägg på den virtuella datorn så att OAuth-tokens kan utfärdas för Azure-resurser.
* Tilldelar RBAC-behörigheter till den hanterade identiteten, med ägarbehörighet för resursgruppen.
* Skapar en Terraform-mallmapp (tfTemplate).
* Förkonfigurerar Terraform Remote State med Azure-serverdel.

## <a name="access-and-configure-a-linux-terraform-vm"></a>Få åtkomst till och konfigurera en virtuell Linux terraform-dator

Utför följande steg när du har skapat den virtuella datorn:

1. Logga in på den virtuella datorn med SSH. Använd de kontoautentiseringsuppgifter som du skapade i föregående avsnitt. I Windows kan du ladda ned ett SSH-klientverktyg som [Putty](https://www.putty.org/).

1. Bevilja deltagar behörighet för hela prenumerationen för hanterade identiteter för Azure-resurser på den virtuella datorn. 

    Deltagar behörighet hjälper hanterade identiteter för Azure-resurser på den virtuella datorn att använda terraform för att skapa resurser utanför resurs gruppen för den virtuella datorn. Utför den här åtgärden genom att köra följande skript: 
    
    ```bash
    . ~/tfEnv.sh
    ```

    Det här skriptet använder [Azure CLI interaktiv inloggnings](/cli/azure/authenticate-azure-cli?view=azure-cli-latest#sign-in-interactively) funktion för att autentisera med Azure. Den här processen tilldelar [behörigheten hanterad identitet deltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) för hela prenumerationen. 

1. Den virtuella datorn ha en serverdel med Terraform Remote State. Om du vill aktivera den i din terraform-distribution måste du kopiera `remoteState.tf`-filen till roten av terraform-skripten.

    ```bash
    cp  ~/tfTemplate/remoteState.tf .
    ```

    Mer information om hantering av fjärrlägen finns i [terraform Remote State](https://www.terraform.io/docs/state/remote.html). Lagrings åtkomst nyckeln visas i den här filen. Undanta den innan du genomför terraform-konfigurationsfilerna i käll kontrollen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Terraform på Azure](/azure/ansible/)