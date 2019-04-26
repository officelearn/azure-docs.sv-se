---
title: Distribuera Avere vFXT för Azure
description: Steg för att distribuera Avere vFXT kluster i Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: v-erkell
ms.openlocfilehash: 7ded66c29f12b8f68746726ca6c126bffbc51f0d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60410331"
---
# <a name="deploy-the-vfxt-cluster"></a>Distribuera vFXT-klustret

Den här proceduren beskriver hur du använder distributionsguiden från Azure Marketplace. Guiden distribuerar automatiskt klustret med hjälp av en Azure Resource Manager-mall. När du har angett parametrarna i formuläret och klicka på **skapa**, Azure automatiskt utföra dessa steg:

* Skapar klustret styrenhet, vilket är en grundläggande virtuell dator som innehåller den programvara som krävs för att distribuera och hantera klustret.
* Ställer in resursgrupp och virtuell nätverksinfrastruktur, inklusive att skapa nya element.
* Skapar klustret noden virtuella datorer och konfigurerar dem som Avere-klustret.
* Vid begäran, skapar en ny Azure Blob-behållare och konfigurerar det som ett kluster core-filer.

När du har följt anvisningarna i det här dokumentet har du ett virtuellt nätverk, ett undernät, en domänkontrollant och ett vFXT kluster som du ser i följande diagram. Det här diagrammet visar valfritt Azure Blob core filservern, som innehåller en ny Blob storage-behållare (i ett nytt lagringskonto, som inte visas) och en tjänstslutpunkt för Microsoft storage i undernätet. 

![diagram som visar tre koncentriska rektanglar med Avere klusterkomponenter. Yttre rektangeln är märkt ”resursgrupp” och innehåller en sexhörning som är märkt ”Blob storage (valfritt)”. Nästa rektangeln i är märkt ”virtuellt nätverk: 10.0.0.0/16 ”och innehåller inte några unika komponenter. Den innersta rektangeln är märkt ”Subnet:10.0.0.0/24” och innehåller en virtuell dator som är märkt kluster-styrenhet, en stack för tre virtuella datorer som är märkt ”vFXT noder (vFXT kluster)” och en sexhörning märkta Service-slutpunkt. Det finns en pil som ansluter till tjänsteslutpunkt (som är i undernätet) och blob-lagringen (som är utanför undernät och vnet, i resursgruppen). Pilen passerar genom undernät och virtuella nätverksgränser.](media/avere-vfxt-deployment.png)  

Kontrollera att du har åtgärdat förutsättningarna innan du använder mallen skapas:  

1. [Ny prenumeration](avere-vfxt-prereqs.md#create-a-new-subscription)
1. [Ägarbehörighet för prenumeration](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
1. [Kvoten för vFXT klustret](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
1. [Slutpunkt för lagring av tjänsten (vid behov)](avere-vfxt-prereqs.md#create-a-storage-service-endpoint-in-your-virtual-network-if-needed) – krävs för distribuerar med hjälp av ett befintligt virtuellt nätverk och skapa blob-lagring

Mer information om distributionssteg för klustret och planera [planera datorn Avere vFXT](avere-vfxt-deploy-plan.md) och [distributionsöversikt](avere-vfxt-deploy-overview.md).

## <a name="create-the-avere-vfxt-for-azure"></a>Skapa Avere vFXT för Azure

Komma åt mallen skapas i Azure portal genom att söka efter Avere och välja ”Avere vFXT för Azure ARM-mall”. 

![Webbläsarfönster som visar Azure-portalen med bröd smulor ”Ny > Marketplace > allt”. Allt har sidan sökfältet markeras termen ”avere” och det andra resultatet ”Avere vFXT för Azure ARM-mall” i rött att markera den.](media/avere-vfxt-template-choose.png)

När du har läst om Avere vFXT för ARM-mall för Azure-sidan, klickar du på **skapa** att börja. 

![Azure marketplace med den första sidan i distributionen mall som visar](media/avere-vfxt-deploy-first.png)

Mallen är uppdelad i fyra steg - två informationsinsamlingen sidor, plus validering och bekräftelse steg. 

* Sidan fokuserar på inställningarna för kluster-styrenhet VM. 
* Sidan två samlar in parametrar för att skapa klustret och associerade resurser som undernät och lagring. 
* Den tredje sidan sammanfattas inställningarna och verifierar konfigurationen. 
* Sidan fyra förklarar licensvillkor för programvara och villkor och låter dig starta klustret skapas. 

## <a name="page-one-parameters---cluster-controller-information"></a>Sidan en parametrar – domänkontrollant klusterinformation

Den första sidan i distributionsmallen samlar in information om kluster-domänkontrollant. 

![Första sidan i mallen för distribution](media/avere-vfxt-deploy-1.png)

Fyll i följande information:

* **Klustret Kontrollnamn** – ange namnet för kontrollanten kluster VM.

* **Controller användarnamn** – Fyll i rot-användarnamnet för kluster-styrenheten VM. 

* **Autentiseringstyp** -Välj lösenord eller SSH autentisering med offentlig nyckel för att ansluta till hanteringsstyrenheten. SSH offentlig nyckel metoden rekommenderas; läsa [hur du skapar och använder SSH-nycklar](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) om du behöver hjälp.

* **Lösenordet** eller **offentlig SSH-nyckel** -beroende på den autentiseringstyp som du har valt, måste du ange en offentlig RSA-nyckel eller ett lösenord i fälten nästa. Den här autentiseringsuppgiften används med det användarnamn som angavs tidigare.

* **Prenumeration** – Välj prenumerationen för Avere vFXT. 

* **Resursgrupp** – Välj en befintlig tom resursgrupp för Avere vFXT klustret, eller klicka på ”Skapa ny” och ange ett nytt resursgruppnamn. 

* **Plats** – Välj Azure-plats för ditt kluster och resurser.

Klicka på **OK** när du är klar. 

> [!NOTE]
> Skapa ett nytt virtuellt nätverk för klustret i stället för att välja ett befintligt nätverk om du vill att kontrollanten kluster har en offentlig IP-adress. Den här inställningen finns på sidan.

## <a name="page-two-parameters---vfxt-cluster-information"></a>Sidan två parametrar – vFXT klusterinformation

Den andra sidan i mallen för distribution kan du ange klusterstorleken, nodtyp, cachestorlek och storage-parametrar, bland annat inställningar. 

![Andra sidan i mallen för distribution](media/avere-vfxt-deploy-2.png)

* **Avere vFXT antalet klusternoder** – Välj antalet noder i klustret. Minimum är tre noder och högsta tolv. 

* **Lösenord för administration av klustret** -lösenord för administration av klustret. Det här lösenordet används tillsammans med användarnamnet ```admin``` att logga in på Kontrollpanelen för klustret att övervaka klustret och konfigurera inställningarna.

* **Avere vFXT klusternamnet** -ge ett unikt namn för klustret. 

* **Storlek** – det här avsnittet visas den typ av virtuell dator som ska användas för klusternoderna. Även om det finns bara ett rekommenderade alternativ, den **ändra storleken på** länken öppnas en tabell med information om den här Instanstypen och en länk till en priskalkylator.  

* **Cachestorlek per nod** -kluster-cache fördelas mellan noderna i klustret så cacheminnets totala storlek i Avere vFXT klustret blir cachestorlek per nod multiplicerat med antalet noder. 

  Den rekommenderade konfigurationen är att använda 4 TB per nod för Standard_E32s_v3 noder.

* **Virtuellt nätverk** – definiera ett nytt virtuellt nätverk för att rymma både klustret eller välj ett befintligt vnet som uppfyller de krav som beskrivs i [planera Avere vFXT systemet](avere-vfxt-deploy-plan.md#resource-group-and-network-infrastructure). 

  > [!NOTE]
  > Om du skapar ett nytt vnet har kontrollanten kluster en offentlig IP-adress så att du kan använda det nya privata nätverket. Om du väljer ett befintligt vnet har kontrollanten kluster konfigurerats utan en offentlig IP-adress. 
  > 
  > En offentligt IP-adress på kontrollanten kluster ger enklare åtkomst till klustret vFXT, men skapar en liten säkerhetsrisk. 
  >  * En offentlig IP-adress på kontrollanten kluster kan du använda den som en jump-värd för att ansluta till Avere vFXT klustret från utanför privat undernät.
  >  * Om du inte ställer in en offentlig IP-adress på kontrollanten, måste du använda en annan jump-värd, en VPN-anslutning eller ExpressRoute för åtkomst till klustret. Till exempel skapa kontrollanten inom ett virtuellt nätverk som redan har en VPN-anslutning som har konfigurerats.
  >  * Om du skapar en domänkontrollant med en offentlig IP-adress, bör du skydda kontrollanten VM med en nätverkssäkerhetsgrupp. Som standard Avere vFXT för Azure-distribution som skapar en nätverkssäkerhetsgrupp och begränsar inkommande åtkomsten till endast port 22 för styrenheter med offentliga IP-adresser. Du kan ytterligare skydda systemet genom att låsa åtkomst till ditt intervall med IP-källadresser – det vill säga Tillåt endast anslutningar från datorer som du planerar att använda för åtkomst till klustret.

  Distribuera mallen konfigurerar också det nya vnet med en tjänstslutpunkt för lagring för Azure Blob storage och åtkomstkontroll för nätverk är låst till endast IP-adresser från undernätet på klustret. 

* **Undernät** – Välj ett undernät från det befintliga virtuella nätverket eller skapa en ny. 

* **Skapa och använda blob storage** -Välj **SANT** att skapa en ny Azure Blob-behållare och konfigurera den som backend-lagring för det nya Avere vFXT klustret. Det här alternativet skapar även ett nytt lagringskonto i samma resursgrupp som klustret och en Microsoft storage tjänstslutpunkt i undernät för klustret. 
  
  Om du anger ett befintligt virtuellt nätverk måste den ha en slutpunkt för lagring innan du skapar klustret. (Mer information finns i [planera Avere vFXT systemet](avere-vfxt-deploy-plan.md).)

  Ange fältet till **FALSKT** om du inte vill skapa en ny behållare. I så fall måste du bifoga och konfigurera lagring när du har skapat klustret. Läs [konfigurerar du lagring](avere-vfxt-add-storage.md) anvisningar. 

* **(Ny) Storage-konto** – om du skapar en ny Azure Blob-behållare, ange ett namn för det nya lagringskontot. 

## <a name="validation-and-purchase"></a>Validering och köp

Den tredje sidan sammanfattas konfigurationen och kontroll av parametrarna. När verifieringen lyckas klickar du på den **OK** för att gå vidare. 

![Tredje sidan i Distributionsmall - verifiering](media/avere-vfxt-deploy-3.png)

På sidan fyra kan ange all nödvändig kontaktinformation och klicka på den **skapa** knappen för att acceptera villkoren och skapa Avere vFXT för Azure-kluster. 

![Fjärde sidan i Distributionsmall - villkor och bestämmelser, skapa knapp](media/avere-vfxt-deploy-4.png)

Klusterdistribution tar 15-20 minuter.

## <a name="gather-template-output"></a>Samla in mallutdata

När mallen Avere vFXT är klar skapar klustret utdata viss information om det nya klustret. 

> [!TIP]
> Se till att kopiera IP-adress för hantering av mallen. Du behöver den här adressen för att administrera klustret.

Följ den här proceduren för att hitta den här informationen:

1. Gå till resursgruppen för din kluster-styrenhet.

1. På vänster sida klickar du på **distributioner**, och sedan **microsoft-avere.vfxt-template**.

   ![Resursgrupp portalsidan med distributioner som har valts till vänster och microsoft-avere.vfxt-mallar som visar i en tabell under distributionsnamn](media/avere-vfxt-outputs-deployments.png)

1. På vänster sida klickar du på **utdata**. Kopiera värdena i varje fält. 

   ![matar ut sidan som visar SSHSTRING, RESOURCE_GROUP, plats, NETWORK_RESOURCE_GROUP, nätverk, UNDERNÄT, SUBNET_ID, VSERVER_IPs och MGMT_IP värden i fälten till höger om etiketter](media/avere-vfxt-outputs-values.png)

## <a name="next-step"></a>Nästa steg

Nu när klustret körs och du vet att dess IP-adress för hantering, kan du [ansluta till klustret konfigurationsverktyget](avere-vfxt-cluster-gui.md) lägga till lagring för att aktivera stöd för, om det behövs och anpassa inställningarna för andra.
