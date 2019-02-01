---
title: Distribuera Avere vFXT för Azure
description: Steg för att distribuera Avere vFXT kluster i Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: f6d847e9042341f47a06fde0f9aa4a70f2549a07
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55512167"
---
# <a name="deploy-the-vfxt-cluster"></a>Distribuera vFXT-klustret

Den här proceduren beskriver hur du använder distributionsguiden från Azure Marketplace. Guiden distribuerar automatiskt klustret med hjälp av en Azure Resource Manager-mall. När du har angett parametrarna i formuläret och klicka på **skapa**, Azure automatiskt utföra dessa steg: 

* Skapa kluster-styrenhet, vilket är en grundläggande virtuell dator som innehåller den programvara som krävs för att distribuera och hantera klustret.
* Ställ in resursgrupp och virtuell nätverksinfrastruktur, inklusive att skapa nya element om det behövs.
* Skapa klustret noden virtuella datorer och konfigurera dem som Avere-klustret.
* Vid begäran, skapa en ny Azure Blob-behållare och konfigurera det som ett kluster core-filer.

När du har följt anvisningarna i det här dokumentet har du ett virtuellt nätverk, ett undernät, en domänkontrollant och ett vFXT kluster som du ser i följande diagram:

![diagram över virtuellt nätverk som innehåller valfri blob-lagring och ett undernät som innehåller tre grupperade virtuella datorer med etiketten vFXT noder/vFXT kluster och en virtuell dator taggade kluster kontrollenhet](media/avere-vfxt-deployment.png)

När du har skapat klustret bör du [skapa en slutpunkt för lagring](#create-a-storage-endpoint-if-using-azure-blob) i det virtuella nätverket om du använder Blob storage. 

Kontrollera att du har åtgärdat förutsättningarna innan du använder mallen skapas:  

1. [Ny prenumeration](avere-vfxt-prereqs.md#create-a-new-subscription)
1. [Ägarbehörighet för prenumeration](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
1. [Kvoten för vFXT klustret](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
1. [Anpassad åtkomst roller](avere-vfxt-prereqs.md#create-access-roles) -du måste skapa en rollbaserad åtkomstkontroll roll att tilldela till klusternoderna. Har du möjlighet att också skapa en anpassad roll för kontrollanten kluster, men de flesta användare tar ägarrollen standard, vilket ger controller privilegier som motsvarar till en resursgruppägare. Läs [inbyggda roller för Azure-resurser](../role-based-access-control/built-in-roles.md#owner) för mer information.

Mer information om distributionssteg för klustret och planera [planera datorn Avere vFXT](avere-vfxt-deploy-plan.md) och [distributionsöversikt](avere-vfxt-deploy-overview.md).

## <a name="create-the-avere-vfxt-for-azure"></a>Skapa Avere vFXT för Azure

Åt mallen som skapas i Azure portal genom att söka efter Avere och välja ”Avere vFXT ARM distribution”. 

![Webbläsarfönster som visar Azure-portalen med bröd smulor ”Ny > Marketplace > allt”. I allt sidan, sökfältet har termen ”avere” och det andra resultatet ”Avere vFXT ARM distribution” markeras i rött att markera den.](media/avere-vfxt-template-choose.png)

När du har läst informationen på sidan Avere vFXT ARM-distributionen, klickar du på **skapa** att börja. 

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

* **Roll-ID att skapa ett kluster för Avere** – Använd det här fältet för att ange rollen åtkomstkontroll för kluster-styrenheten. Standardvärdet är den inbyggda rollen [ägare](../role-based-access-control/built-in-roles.md#owner). Ägarprivilegier för kluster-styrenheten är begränsade till klustrets resursgrupp. 

  Du måste använda globalt unik identifierare som motsvarar rollen. För standardvärdet (ägare) är GUID 8e3af657-a8ff-443c-a75c-2fe8c4bcb635. Använd följande kommando för att hitta GUID för en anpassad roll: 

  ```azurecli
  az role definition list --query '[*].{roleName:roleName, name:name}' -o table --name 'YOUR ROLE NAME'
  ```

* **Prenumeration** – Välj prenumerationen för Avere vFXT. 

* **Resursgrupp** – väljer du resursgruppen för Avere vFXT klustret, eller klicka på ”Skapa ny” och ange ett nytt resursgruppnamn. 

* **Plats** – Välj Azure-plats för ditt kluster och resurser.

Klicka på **OK** när du är klar. 

> [!NOTE]
> Skapa ett nytt virtuellt nätverk för klustret i stället för att välja ett befintligt nätverk om du vill att kontrollanten kluster har en offentlig IP-adress. Den här inställningen finns på sidan.

## <a name="page-two-parameters---vfxt-cluster-information"></a>Sidan två parametrar – vFXT klusterinformation

Den andra sidan i mallen för distribution kan du ange klusterstorleken, nodtyp, cachestorlek och storage-parametrar, bland annat inställningar. 

![Andra sidan i mallen för distribution](media/avere-vfxt-deploy-2.png)

* **Avere vFXT antalet klusternoder** – Välj antalet noder i klustret. Minimum är tre noder och högsta tolv. 

* **Lösenord för administration av klustret** -lösenord för administration av klustret. Det här lösenordet används tillsammans med användarnamnet ```admin``` att logga in på Kontrollpanelen för klustret att övervaka klustret och konfigurera inställningarna.

* **Avere klusterrollen operations** – ange namnet på rollen åtkomstkontroll för klusternoderna. Det här är en anpassad roll som har skapats som en innan du börjar. 

  I exemplet som beskrivs i [skapa klusterrollen noden åtkomst](avere-vfxt-prereqs.md#create-the-cluster-node-access-role) sparar filen som ```avere-operator.json``` och motsvarande rollnamnet är ```avere-operator```.

* **Avere vFXT klusternamnet** -ge ett unikt namn för klustret. 

* **Storlek** – ange den typ av virtuell dator att använda när du skapar klusternoderna. 

* **Cachestorlek per nod** -kluster-cache fördelas mellan noderna i klustret så cacheminnets totala storlek i Avere vFXT klustret blir cachestorlek per nod multiplicerat med antalet noder. 

  Den rekommenderade konfigurationen är att använda 1 TB per nod om du använder Standard_D16s_v3 klusternoderna och du använder 4 TB per nod om du använder Standard_E32s_v3 noder.

* **Virtuellt nätverk** – Välj ett befintligt vnet för klustret eller definiera ett nytt virtuellt nätverk ska skapa. 

  > [!NOTE]
  > Om du skapar ett nytt vnet har kontrollanten kluster en offentlig IP-adress så att du kan använda det nya privata nätverket. Om du väljer ett befintligt vnet har kontrollanten kluster konfigurerats utan en offentlig IP-adress. 
  > 
  > En offentligt IP-adress på kontrollanten kluster ger enklare åtkomst till klustret vFXT, men skapar en liten säkerhetsrisk. 
  >  * En offentlig IP-adress på kontrollanten kluster kan du använda den som en jump-värd för att ansluta till Avere vFXT klustret från utanför privat undernät.
  >  * Om du inte ställer in en offentlig IP-adress på kontrollanten, måste du använda en annan jump-värd, en VPN-anslutning eller ExpressRoute för åtkomst till klustret. Till exempel skapa kontrollanten inom ett virtuellt nätverk som redan har en VPN-anslutning som har konfigurerats.
  >  * Om du skapar en domänkontrollant med en offentlig IP-adress, bör du skydda kontrollanten VM med en nätverkssäkerhetsgrupp. Som standard Avere vFXT för Azure-distribution som skapar en nätverkssäkerhetsgrupp och begränsar inkommande åtkomsten till endast port 22 för styrenheter med offentliga IP-adresser. Du kan ytterligare skydda systemet genom att låsa åtkomst till ditt intervall med IP-källadresser – det vill säga Tillåt endast anslutningar från datorer som du planerar att använda för åtkomst till klustret.

* **Undernät** – Välj ett undernät från det befintliga virtuella nätverket eller skapa en ny. 

* **Använda blob storage** -Välj **SANT** att skapa en ny Azure Blob-behållare och konfigurera den som backend-lagring för det nya Avere vFXT klustret. Det här alternativet skapar även ett nytt lagringskonto i samma resursgrupp som klustret. 

  Ange fältet till **FALSKT** om du inte vill skapa en ny behållare. I så fall måste du bifoga och konfigurera lagring när du har skapat klustret. Läs [konfigurerar du lagring](avere-vfxt-add-storage.md) anvisningar. 

* **Storage-konto** – om du skapar en ny Azure Blob-behållare, ange ett namn för det nya lagringskontot. 

## <a name="validation-and-purchase"></a>Validering och köp

Den tredje sidan ger en sammanfattning av konfigurationen och verifierar parametrarna. När verifieringen lyckas klickar du på den **OK** för att gå vidare. 

![Tredje sidan i Distributionsmall - verifiering](media/avere-vfxt-deploy-3.png)

Klicka på sidan fyra den **skapa** knappen för att acceptera villkoren och skapa Avere vFXT för Azure-kluster. 

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


## <a name="create-a-storage-endpoint-if-using-azure-blob"></a>Skapa en slutpunkt för lagring (om du använder Azure Blob)

Om du använder Azure Blob storage för lagring av dina backend-data kan skapa du en slutpunkt för lagring i det virtuella nätverket. Detta [tjänstslutpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) behåller Azure Blob-trafik lokalt i stället för att skicka det utanför det virtuella nätverket.

1. I portalen klickar du på **virtuella nätverk** till vänster.
1. Välj det virtuella nätverket för din kontrollant. 
1. Klicka på **tjänstslutpunkter** till vänster.
1. Klicka på **Lägg till** högst upp.
1. Lämna tjänsten som ``Microsoft.Storage`` och välj den styrenheten undernät.
1. Längst ned på sidan, klickar du på **Lägg till**.

  ![Azure portal skärmbild med anteckningar för stegen för att skapa tjänsteslutpunkt](media/avere-vfxt-service-endpoint.png)

## <a name="next-step"></a>Nästa steg

Nu när klustret körs och du vet att dess IP-adress för hantering, kan du [ansluta till klustret konfigurationsverktyget](avere-vfxt-cluster-gui.md) lägga till lagring för att aktivera stöd för, om det behövs och anpassa inställningarna för andra.
