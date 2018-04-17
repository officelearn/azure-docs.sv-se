---
title: Använda en Azure Marketplace-avbildning för att skapa en Terraform Linux-dator med hanterade tjänstidentiteten
description: Använd Marketplace-avbildning för att skapa Terraform Linux-dator med hanterade tjänstidentiteten och fjärrhantering för tillstånd för att enkelt distribuera resurser till Azure.
keywords: terraform, devops, MSI, virtuell dator, remote tillstånd, azure
author: VaijanathB
manager: rloutlaw
ms.author: tarcher
ms.date: 3/12/2018
ms.topic: article
ms.openlocfilehash: 5f0ee2904c1072a5ad8c5f7ae1c90e649cc4813c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="use-an-azure-marketplace-image-to-create-a-terraform-linux-virtual-machine-with-managed-service-identity"></a>Använda en Azure Marketplace-avbildning för att skapa en Terraform Linux-dator med hanterade tjänstidentiteten

Den här artikeln visar hur du använder en [Terraform Marketplace-avbildning](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview) att skapa en virtuell Ubuntu Linux-dator (16.04 LTS) med senast [Terraform](https://www.terraform.io/intro/index.html) version installeras och konfigureras med hjälp av [hanteras Tjänsten identitet (MSI)](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Den här avbildningen konfigurerar också en fjärransluten serverdel så att [remote tillstånd](https://www.terraform.io/docs/state/remote.html) hantering med hjälp av Terraform. 

Terraform Marketplace-avbildning gör det enkelt att komma igång med Terraform på Azure, utan att behöva installera och konfigurera Terraform manuellt. 

Det finns inga avgifter för programvara för den här Terraform VM-avbildning. Du betalar endast Azure maskinvara användning avgifter som utvärderas baserat på storleken på den virtuella dator som har etablerats. Mer information om beräkning avgifter finns i [Linux virtuella datorer sida med priser](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="prerequisites"></a>Förutsättningar
Innan du kan skapa en virtuell dator för Linux Terraform, måste du ha en Azure-prenumeration. Om du inte redan har ett, se [skapa din kostnadsfria Azure-konto idag](https://azure.microsoft.com/free/).  

## <a name="create-your-terraform-virtual-machine"></a>Skapa den virtuella datorn Terraform 

Här följer stegen för att skapa en instans av en virtuell dator för Linux Terraform: 

1. I Azure-portalen går du till den [skapar du en resurs](https://ms.portal.azure.com/#create/hub) lista.

2. I den **söka Marketplace** sökfältet, söka efter **Terraform**. Välj den **Terraform** mall. 

3. På fliken Terraform information längst ned till höger väljer du den **skapa** knappen.

    ![Skapa en virtuell dator för Terraform](media\terraformmsi.png)

4. Följande avsnitt innehåller indata för varje steg i guiden för att skapa Terraform Linux-dator. I följande avsnitt visas indata som behövs för att konfigurera var och en av de här stegen.

## <a name="details-on-the-create-terraform-tab"></a>Informationen på fliken Skapa Terraform

Ange följande information den **skapa Terraform** fliken:

1. **Grundläggande inställningar**
    
   * **Namnet**: namnet på den virtuella datorn Terraform.
   * **Användarnamnet**: det första kontot inloggning ID.
   * **Lösenordet**: första kontolösenord. (Du kan använda en offentlig SSH-nyckel i stället för ett lösenord.)
   * **Prenumerationen**: prenumerationen som datorn ska skapas och debiteras. Du måste ha behörighet för resursen skapas för den här prenumerationen.
   * **Resursgruppen**: en ny eller befintlig resursgrupp.
   * **Plats**: datacenter som är mest lämplig. Vanligtvis är det datacenter som har de flesta av dina data, eller en som ligger närmast din fysiska platsen för snabbaste nätverksåtkomst.

2. **Ytterligare inställningar**

   * **Storlek**: storleken på den virtuella datorn. 
   * **VM disktyp**: SSD och Hårddisk.

3. **Översikt över Terraform**

   * Kontrollera att all information som du angett är korrekt. 

4. **Köpa**

   * Om du vill starta etableringsprocessen, Välj **köpa**. En länk som villkoren i transaktionen. Den virtuella datorn har inte några ytterligare kostnader utöver beräkning för servern som du angav i steg storlek.

Terraform VM-avbildning utför följande steg:

* Skapar en virtuell dator med system som tilldelats identitet som baseras på Ubuntu 16.04 LTS bilden.
* Installerar MSI-tillägget på den virtuella datorn så att OAuth-token som utfärdas för Azure-resurser.
* Tilldelar RBAC-behörighet till hanterade identitet, bevilja ägarrättigheter för resursgruppen.
* Skapar en Terraform mall-mapp (tfTemplate).
* Före konfigurerar tillståndet Terraform remote Azure säkerhetskopiera slutet.

## <a name="access-and-configure-a-linux-terraform-virtual-machine"></a>Åtkomst till och konfigurera en virtuell dator för Linux Terraform

När du har skapat den virtuella datorn kan logga du in till den med hjälp av SSH. Använd de kontoautentiseringsuppgifter som du skapade i avsnittet ”grunderna” i steg 3 för gränssnittet text shell. På Windows, kan du ladda ned en SSH-klientverktyg som [Putty](http://www.putty.org/).

När du använder SSH för att ansluta till den virtuella datorn, måste du ge hanterade tjänstidentiteten bidragsgivarbehörighet för hela prenumerationen på den virtuella datorn. 

Deltagare behörighet hjälper MSI på den virtuella datorn att använda Terraform för att skapa resurser utanför VM resursgruppen. Du kan lätt få den här åtgärden genom att köra ett skript på en gång. Ange följande kommando:

`. ~/tfEnv.sh`

Föregående skript använder den [AZ CLI v 2.0 interaktiv inloggning](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest#interactive-log-in) mekanism för att autentisera med Azure och tilldela den virtuella datorn hanterade tjänstidentiteten deltagare behörighet för hela prenumerationen. 

 Den virtuella datorn har en Terraform remote tillstånd serverdel. Om du vill aktivera det på Terraform distributionen, du kopiera filen remoteState.tf från tfTemplate directory till roten i Terraform-skript.  

 `cp  ~/tfTemplate/remoteState.tf .`

 Läs mer om hantering av tillstånd, [den här sidan om Terraform remote tillståndet](https://www.terraform.io/docs/state/remote.html). Lagringsåtkomstnyckel exponeras i den här filen och måste exkluderas innan du aktiverar Terraform konfigurationsfilerna till källkontroll.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du ställer in en Terraform Linux-dator på Azure. Nedan följer några ytterligare resurser för att lära dig mer om Terraform i Azure: 

 [Terraform hubben i Microsoft.com](https://docs.microsoft.com/azure/terraform/)  
 [Terraform Azure provider-dokumentationen](http://aka.ms/terraform)  
 [Terraform Azure provider källa](http://aka.ms/tfgit)  
 [Terraform Azure-moduler](http://aka.ms/tfmodules)
 

















