---
title: Använda en Azure Marketplace-avbildning till att skapa en virtuell Terraform Linux-dator med hanterad identitet
description: Använd Marketplace-avbildning till att skapa virtuell Terraform Linux-dator med en hanterad identitet och Remote State-hantering för att enkelt distribuera resurser till Azure.
services: terraform
ms.service: terraform
keywords: terraform, devops, MSI, virtual machine, remote state, azure
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 3/12/2018
ms.openlocfilehash: 6c9bef108c2f272c678879124ae2cd4f9ae093ba
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54076237"
---
# <a name="use-an-azure-marketplace-image-to-create-a-terraform-linux-virtual-machine-with-managed-identities-for-azure-resources"></a>Använda en Azure Marketplace-avbildning till att skapa en virtuell Terraform Linux-dator med hanterade identiteter för Azure-resurser

I den här artikeln visas hur du använder en [Terraform Marketplace-avbildning](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview) till att skapa en virtuell Ubuntu Linux-dator (16.04 LTS) med den senaste [Terraform](https://www.terraform.io/intro/index.html)-versionen installerad och konfigurerad med [hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Den här avbildningen konfigurerar även en fjärrserverdel för att aktivera [Remote State](https://www.terraform.io/docs/state/remote.html)-hantering med Terraform. 

Terraform Marketplace-avbildningen gör det enkelt att komma igång med Terraform på Azure, utan att behöva installera och konfigurera Terraform-manuellt. 

Det tillkommer inga programvaruavgifter för den Terraform VM-avbildningen. Du betalar bara för användningen av Azure-maskinvara som utvärderas utifrån storleken på den virtuella dator som etableras. Mer information om beräkningsavgifter finns på [sidan med priser för virtuella Linux-datorer](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="prerequisites"></a>Nödvändiga komponenter
Innan du kan skapa en virtuell Linux Terraform-dator måste du ha en Azure-prenumeration. Se [Skapa ett kostnadsfritt Azure-konto i dag](https://azure.microsoft.com/free/) om du inte redan har ett konto.  

## <a name="create-your-terraform-virtual-machine"></a>Skapa den virtuella Terraform-datorn 

Så här skapar du en instans av en virtuell Linux Terraform-dator: 

1. Gå till listan [Skapa en resurs](https://ms.portal.azure.com/#create/hub) i Azure Portal.

2. I sökfältet **Sök på Marketplace** söker du efter **Terraform**. Välj **Terraform**-mallen. 

3. På fliken med Terraform-information längst ned till höger väljer du knappen **Skapa**.

    ![Skapa en virtuell Terraform-dator](media/terraformmsi.png)

4. Följande avsnitt innehåller indata för varje steg i guiden till att skapa den virtuella Terraform Linux-datorn. I följande avsnitt listas de indata som behövs för att konfigurera var och en av dessa steg.

## <a name="details-on-the-create-terraform-tab"></a>Information om fliken Skapa Terraform

Ange följande information på fliken **Skapa Terraform**:

1. **Grundläggande inställningar**
    
   * **Namn**: Namnet på den virtuella Terraform-datorn.
   * **Användarnamn**: Kontots första inloggnings-ID.
   * **Lösenord**: Kontots första lösenord. (Du kan använda en offentlig SSH-nyckel istället för ett lösenord.)
   * **Prenumeration**: Den prenumeration som datorn ska skapas och faktureras på. Du måste ha behörighet att skapa resurser för prenumerationen.
   * **Resursgrupp**: En ny eller befintlig resursgrupp.
   * **Plats**: Det mest lämpliga datacentret. Vanligtvis är det datacentret som har det mesta av dina data, eller en som är närmast din fysiska plats för snabbaste nätverksåtkomst.

2. **Ytterligare inställningar**

   * **Storlek**: Storleken på den virtuella datorn. 
   * **Typ av virtuell datordisk**: SSD eller HDD.

3. **Sammanfattning av Terraform**

   * Kontrollera att all information som du har angett är rätt. 

4. **Köp**

   * Inled etableringsprocessen genom att välja **Köp**. Det finns en länk till villkoren för transaktionen. Den virtuella datorn har inga ytterligare avgifter utöver beräkningen för serverstorleken som du har valt i storlekssteget.

Den virtuella Terraform-datorn utför följande:

* Skapar en virtuell dator med systemtilldelad identitet utifrån Ubuntu 16.04 LTS-avbildningen.
* Installerar MSI-tillägget på den virtuella datorn så att OAuth-token kan utfärdas för Azure-resurser.
* Tilldelar RBAC-behörigheter till den hanterade identiteten, med ägarbehörighet för resursgruppen.
* Skapar en Terraform-mallmapp (tfTemplate).
* Förkonfigurerar Terraform Remote State med Azure-serverdel.

## <a name="access-and-configure-a-linux-terraform-virtual-machine"></a>Få åtkomst till och konfigurera en virtuell Linux Terraform-dator

När du har skapat den virtuella datorn kan du logga in på den med SSH. Använd de autentiseringsuppgifter för kontot som du skapade i avsnittet Grundläggande inställningar i steg 3 för textgränssnittet. I Windows kan du ladda ned ett SSH-klientverktyg som [Putty](http://www.putty.org/).

När du har använt SSH till att anslut till den virtuella datorn måste du ge deltagarbehörighet för hela prenumerationen till hanterade identiteter för Azure-resurser på den virtuella datorn. 

Med deltagarbehörighet kan Hanterad tjänstidentitet på den virtuella datorn använda Terraform till att skapa resurser utanför VM-resursgruppen. Du kan enkelt uppnå den här åtgärden genom att köra skriptet en gång. Ange följande kommando:

`. ~/tfEnv.sh`

Det föregående skriptet använder mekanismen [interaktiv inloggning med AZ CLI v 2.0](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest#interactive-log-in) för att autentisera med Azure och tilldela den virtuella datorn deltagarbehörighet för hanterad identitet för hela prenumerationen. 

 Den virtuella datorn ha en serverdel med Terraform Remote State. Om du vill aktivera den på Terraform-distributionen kopierar du filen remoteState.tf från katalogen tfTemplate till roten för Terraform-skripten.  

 `cp  ~/tfTemplate/remoteState.tf .`

 Mer information om Remote State-hantering finns på [den här sidan om Terraform Remote State](https://www.terraform.io/docs/state/remote.html). Lagringsåtkomstnyckel exponeras i den här filen och måste exkluderas innan Terraform-konfigurationsfiler checkas in i källkontrollen.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du konfigurerar en virtuell Terraform Linux-dator i Azure. Här är några ytterligare resurser som du kan använda om du vill lära dig mer om Terraform på Azure: 

 [Terraform Hub på Microsoft.com](https://docs.microsoft.com/azure/terraform/)  
 [Terraform-dokumentation för Azure-providrar](https://aka.ms/terraform)  
 [Terraform-källa för Azure-providrar](https://aka.ms/tfgit)  
 [Terraform-moduler för Azure](https://aka.ms/tfmodules)
 

















