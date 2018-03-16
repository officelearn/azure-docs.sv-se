---
title: "Använda en Azure Marketplace-avbildning för att skapa en Terraform Linux-dator med hanterade tjänstidentiteten"
description: "Använd Marketplace-avbildning för att skapa Terraform virtuell Linux-dator med hantering av hanterade tjänstidentiteten och fjärr-tillstånd för att enkelt distribuera resurser till Azure."
keywords: terraform, devops, MSI, virtual machine, remote state, azure
author: VaijanathB
manager: rloutlaw
ms.author: tarcher
ms.date: 3/12/2018
ms.topic: article
ms.openlocfilehash: ce8a3e8b813bf4224a1fd77d60ec30f2f8e080a7
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="use-an-azure-marketplace-image-to-create-a-terraform-linux-virtual-machine-with-managed-service-identity"></a>Använda en Azure Marketplace-avbildning för att skapa en Terraform Linux-dator med hanterade tjänstidentiteten

Den här artikeln visar hur du använder en [Terraform Marketplace-avbildning](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview) att skapa en `Ubuntu Linux VM (16.04 LTS)` med senaste [Terraform](https://www.terraform.io/intro/index.html) version installeras och konfigureras med hjälp av [hanterade tjänstidentiteten ( MSI)](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Den här avbildningen konfigurerar också en fjärransluten serverdel för att aktivera [Remote tillstånd](https://www.terraform.io/docs/state/remote.html) hantering med hjälp av Terraform. Terraform Marketplace-avbildning gör det enkelt att komma igång med Terraform på Azure i minuter, utan att behöva installera Terraform och konfigurera autentisering manuellt. 

Det finns inga avgifter för programvara för den här Terraform VM-avbildning. Du betalar endast Azure maskinvara användning avgifter som utvärderas baserat på storleken på den virtuella datorn som har etablerats. Mer information om beräkning avgifter kan hittas på den [prissättning för Linux virtuella datorer](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="prerequisites"></a>Förutsättningar
Innan du kan skapa en virtuell Linux Terraform-dator, måste du ha en Azure-prenumeration. Om du inte redan har en, se [skapa din kostnadsfria Azure-konto idag](https://azure.microsoft.com/free/).  

## <a name="create-your-terraform-virtual-machine"></a>Skapa den virtuella datorn Terraform 

Här följer stegen för att skapa en instans av Linux Terraform Virtual Machine. 

1. Gå till [skapar du en resurs](https://ms.portal.azure.com/#create/hub) lista på Azure-portalen.
2. Sök efter `Terraform` i den `Search the Marketplace` sökfältet. Välj den `Terraform` mall. Välj den **skapa** knappen fliken Terraform information till nedre höger.
![alternativ text](media\terraformmsi.png)
3. Följande avsnitt innehåller indata för varje steg i guiden (**räknas upp till höger**) att skapa Terraform Linux virtuella dator.  Här följer de indata som behövs för att konfigurera var och en av de här stegen

## <a name="details-in-create-terraform-tab"></a>Mer information i Skapa Terraform fliken

Här är den information som måste anges i Skapa Terraform fliken.

a. **Grundläggande inställningar**
    
* **Namnet**: namnet på den virtuella datorn Terraform.
* **Användarnamnet**: första kontot inloggning ID.
* **Lösenordet**: första kontolösenord (du kan använda offentliga SSH-nyckeln i stället för lösenord).
* **Prenumerationen**: Om du har mer än en prenumeration väljer du en som datorn ska skapas och debiteras. Du måste ha behörighet för resursen skapas för den här prenumerationen.
* **Resursgruppen**: du kan skapa en ny eller Använd en befintlig grupp.
* **Plats**: Välj datacenter som är mest lämplig. Vanligtvis är det datacenter som har de flesta av dina data, eller så ligger närmast din fysiska platsen för snabbaste nätverksåtkomst.

b. **Ytterligare inställningar**

* Storlek: Storleken på den virtuella datorn.
* Typ av VM-disk: Välj mellan SSD och HDD

c. **Översikt över Terraform**

* Kontrollera att all information du angett är korrekt. 

d. **Köpa**

* Starta etableringen, klicka på köp. En länk som villkoren i transaktionen. Den virtuella datorn har inte några ytterligare kostnader utöver beräkning för server-storlek som du valde i steg storlek.

Utför följande steg för Terraform VM-avbildning

* Skapar en virtuell dator med system som tilldelats identitet, baserat på bilden Ubuntu 16.04 LTS
* MSI-tillägget installeras på den virtuella datorn så att OAuth-token som utfärdas för Azure-resurser
* Tilldela RBAC behörigheter till hanterade identitet, bevilja ägarrättigheter för resursgruppen
* Skapar en Terraform mall-mapp (tfTemplate)
* Före konfigurerar Terraform remote tillstånd med den Azure-serverdelen

## <a name="how-to-access-and-configure-linux-terraform-virtual-machine"></a>Åtkomst till och konfigurera Terraform virtuell Linux-dator

När den virtuella datorn har skapats kan logga du in till den med hjälp av SSH. Använd de kontoautentiseringsuppgifter som du skapade i avsnittet grunderna i steg 3 för gränssnittet text shell. På Windows, kan du ladda ned en SSH-klientverktyg som [Putty](http://www.putty.org/)

När du har använt `SSH` för att ansluta till den virtuella datorn, måste du ge bidragsgivarbehörighet för hela prenumerationen för hanterade tjänstidentiteten på den virtuella datorn. Deltagare behörighet hjälper MSI på den virtuella datorn att använda Terraform för att skapa resurser utanför VM resursgruppen. Du kan lätt få den här åtgärden genom att köra ett skript på en gång. Här är kommandot för att göra det.

`. ~/tfEnv.sh`

Föregående skript använder [AZ CLI v 2.0 interaktiv inloggning](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest#interactive-log-in) mekanism för att autentisera med Azure och tilldela tjänstidentiteten för virtuell dator hanteras deltagare behörighet för hela prenumerationen. 

 Den virtuella datorn har Terraform Remote tillstånd backend skapas och om du vill aktivera det på Terraform distributionen, måste du kopiera remoteState.tf fil från tfTemplate katalog till roten i Terraform-skript.  

 `cp  ~/tfTemplate/remoteState.tf .`

 Du kan läsa mer om hantering av tillstånd [här](https://www.terraform.io/docs/state/remote.html). Lagringsåtkomstnyckel exponeras i den här filen och behöver kontrolleras noga till källkontroll.  

## <a name="next-steps"></a>Nästa steg
I den här artikeln du har lärt dig hur du ställer in en Terraform Linux-dator på Azure. Här följer några ytterligare resurser för att lära dig mer om Terraform på Azure. 

 [Terraform hubben i Microsoft.com](https://docs.microsoft.com/azure/terraform/)  
 [Terraform Azure Provider-dokumentationen](http://aka.ms/terraform)  
 [Terraform Azure Provider källa](http://aka.ms/tfgit)  
 [Terraform Azure-moduler](http://aka.ms/tfmodules)
 

















