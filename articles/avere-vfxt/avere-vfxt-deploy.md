---
title: Distribuera Avere vFXT för Azure
description: Steg för att distribuera Avere vFXT kluster i Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 47773f9375927e4d8dfbfec922e4cf11e42ade04
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634538"
---
# <a name="deploy-the-vfxt-cluster"></a>Distribuera vFXT-kluster

Om du vill skapa ett vFXT kluster är det enklaste sättet att använda en kluster-controller som en virtuell dator som har de nödvändiga skripten, mallar och programvaruinfrastruktur för att skapa och hantera vFXT-kluster.

Distribuera ett nytt vFXT kluster omfattar de här stegen:

1. [Skapa kluster-kontrollant](#create-the-cluster-controller-vm).
1. Om du använder Azure Blob storage, [skapa en slutpunkt för lagring](#create-a-storage-endpoint-if-using-azure-blob) i det virtuella nätverket.
1. [Ansluta till klustret styrenheten](#access-the-controller). Resten av dessa steg utförs från klustret kontrollanten VM. 
1. [Skapa åtkomstrollen](#create-the-cluster-node-access-role) för klusternoderna. En prototyp tillhandahålls.
1. [Anpassa kluster Skapandeskriptet](#edit-the-deployment-script) för typ av vFXT kluster som du vill skapa.
1. [Kör skriptet som skapar klustret](#run-the-script).

Mer information om distributionssteg för klustret och planera [planera datorn Avere vFXT](avere-vfxt-deploy-plan.md) och [distributionsöversikt](avere-vfxt-deploy-overview.md). 

När du har följt anvisningarna i det här dokumentet har du ett virtuellt nätverk, ett undernät, en domänkontrollant och ett vFXT kluster som du ser i följande diagram:

![diagram över virtuellt nätverk som innehåller valfri blob-lagring och ett undernät som innehåller tre grupperade virtuella datorer med etiketten vFXT noder/vFXT kluster och en virtuell dator taggade kluster kontrollenhet](media/avere-vfxt-deployment-500px.png)

Kontrollera att du har åtgärdat förutsättningarna innan du börjar:  

1. [Ny prenumeration](avere-vfxt-prereqs.md#create-a-new-subscription)
1. [Ägarbehörighet för prenumeration](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
1. [Kvoten för vFXT klustret](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)

Du kan skapa nod klusterrollen [innan](avere-vfxt-pre-role.md) skapar kontrollanten kluster, men det är enklare att göra det efteråt.

## <a name="create-the-cluster-controller-vm"></a>Skapa kluster kontrollant VM

Det första steget är att skapa den virtuella datorn som skapar och konfigurerar vFXT klusternoderna. 

Kluster-styrenheten är en Linux VM med Avere vFXT klusterprogramvaran skapande och skript som förinstallerad. Det måste inte betydande bearbetning kapacitets- eller mellanslag, så att du kan välja prisvärt alternativ. Den här virtuella datorn används ibland under hela livslängden för vFXT-klustret.

Det finns två metoder för att skapa klustret kontrollant VM. En [Azure Resource Manager-mall](#create-controller---arm-template) tillhandahålls [nedan](#create-controller---arm-template) att förenkla processen, men du kan också skapa kontrollanten från den [Azure Marketplace-avbildning](#create-controller---azure-marketplace-image). 

Du kan skapa en ny resursgrupp som en del av kontrollanten.

> [!TIP]
>
> Bestäm om du använder en offentlig IP-adress på kontrollanten kluster eller inte. En offentlig IP-adress ger enklare åtkomst till klustret vFXT, men skapar en liten säkerhetsrisk.
>
>  * En offentlig IP-adress på kontrollanten kluster kan du använda den som en jump-värd för att ansluta till Avere vFXT klustret från utanför privat undernät.
>  * Om du inte ställer in en offentlig IP-adress på kontrollanten, måste du använda en annan jump-värd, en VPN-anslutning eller ExpressRoute för åtkomst till klustret. Till exempel skapa kontrollanten inom ett virtuellt nätverk som har en VPN-anslutning som har konfigurerats.
>  * Om du skapar en domänkontrollant med en offentlig IP-adress, bör du skydda kontrollanten VM med en nätverkssäkerhetsgrupp. Tillåt åtkomst endast via port 22 ansluter till internet.

### <a name="create-controller---resource-manager-template"></a>Skapa kontrollenhet – Resource Manager-mall

Skapa controller klusternod från portalen, klicka på knappen ”distribuera till Azure” nedan. Detta distribuera mallen skapar den virtuella datorn som skapar och hanterar Avere vFXT klustret.

[![för att skapa domänkontrollanten](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fazuredeploy.json)

Ange följande information.

I den **BASIC** avsnittet:  

* **Prenumerationen** för klustret
* **Resursgrupp** för klustret 
* **Plats** 

I den **inställningar** avsnittet:

* Om du ska skapa ett nytt virtuellt nätverk eller inte

  * Om du skapar ett nytt vnet kommer kontrollanten klustret att tilldelas en offentlig IP-adress så att du kan nå den. En nätverkssäkerhetsgrupp har skapats för det här virtuella nätverket som begränsar inkommande trafik till endast port 22.
  * Om du vill använda ExpressRoute eller VPN för att ansluta till klustret styrenhet, ange värdet `false` och ange ett befintligt vnet i återstående fält. Kontrollanten klustret använder det virtuella nätverket för nätverkskommunikation. 

* Resursgrupp för virtuellt nätverk, namn och namn på undernät - skriver namnen på befintliga resurser (om du använder ett befintligt virtuellt nätverk) eller ange nytt namn om du skapar ett nytt virtuellt nätverk
* **Kontrollnamn** -ange ett namn för VM-styrenhet
* Administratörsanvändarnamn för domänkontrollant – standardvärdet är `azureuser`
* SSH-nyckel - klistra in den offentliga nyckeln för att koppla till administratörens användarnamn. Läs [hur du skapar och använder SSH-nycklar](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) om du behöver hjälp.

Under **villkor**: 

* Läs villkoren och klicka på kryssrutan för att godkänna dem. 

  > [!NOTE] 
  > Om du inte är en prenumerationsägare kan ha en ägare som accepterar villkoren för du genom att följa nödvändiga stegen i [acceptera programvara villkoren i förväg](avere-vfxt-prereqs.md#accept-software-terms-in-advance). 

Klicka på **köp** när du är klar. När du har fem eller sex minuter kommer controller noden vara igång.

Du bör gå till utdata-sidan för att samla in information som behövs för klustret. Läs [indata behövs för Skapa kluster](#inputs-needed-for-cluster-creation) vill veta mer.

### <a name="create-controller---azure-marketplace-image"></a>Skapa kontrollenhet – Azure Marketplace-avbildning

Hitta controller mallen genom att söka på Azure Marketplace efter namnet ``Avere``. Välj den **Avere vFXT för Azure Controller** mall. 

Om du inte redan har gjort det, acceptera villkoren och aktivera Programmeringsåtkomst för Marketplace-avbildning genom att klicka på ”vill du distribuera via programmering”? länka under den **skapa** knappen.

> [!NOTE] 
> Under den första veckan i allmän tillgänglighet (31 oktober – 7 November 2018), måste du använda kommandoradsalternativet accepterar villkoren för två Programvaruavbildningar i stället för att följa den här proceduren. Följ instruktionerna i [acceptera programvara villkoren i förväg](avere-vfxt-prereqs.md#accept-software-terms-in-advance). 

![Skärmbild av en länk till programmässig åtkomst, vilket ligger under knappen Skapa](media/avere-vfxt-deploy-programmatically.png)

Klicka på den **aktivera** knappen och spara inställningen.

![Skärmbild som visar mus Klicka om du vill aktivera programmässig åtkomst](media/avere-vfxt-enable-program.png)

Återgå till huvudsidan för den **Avere vFXT för Azure Controller** mallen och klicka på **skapa**. 

Fyll i första panelen eller bekräfta dessa grundläggande alternativ:

* **Prenumeration**
* **Resursgrupp** (Ange ett nytt namn om du vill skapa en ny grupp.)
* **Namn på virtuell dator** -de kontrollnamn
* **Region**
* **Alternativ för tillgänglighet** -redundans är inte obligatoriskt
* **Bild** -Avere vFXT controller noden bild
* **Storlek** – Låt standardvärdet eller välj en annan typ av prisvärd
* **Administratörskontot** – ange hur att logga in på kontrollanten klustret: 
  * Välj användarnamn/lösenord eller offentlig SSH-nyckel (rekommenderas).
  
    > [!TIP] 
    > En SSH-nyckel är säkrare. Läs [hur du skapar och använder SSH-nycklar](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) om du behöver hjälp. 
  * Ange användarnamnet 
  * Klistra in SSH-nyckel eller ange och bekräfta lösenordet
* **Regler för inkommande portar** – om du använder en offentlig IP-adress, öppna port 22 (SSH)

Klicka på **nästa** och ställer in diskalternativ:

* **OS-disktyp** -HDD standardvärdet är tillräckligt
* **Använda ohanterade diskar** – det är inte nödvändigt
* **Datadiskar** – Använd inte

Klicka på **nästa** för nätverksalternativ:

* **Virtuellt nätverk** – Välj det virtuella nätverket för styrenheten eller ange ett namn för att skapa ett nytt vnet. Överväg att hitta inom ett virtuellt nätverk med ExpressRoute eller en annan åtkomstmetod som redan har konfigurerat om du inte vill använda en offentlig IP-adress på kontrollanten.
* **Undernät** -Välj ett undernät i det virtuella nätverket (valfritt). Om du skapar ett nytt virtuellt nätverk, kan du skapa ett nytt undernät på samma gång.
* **Offentlig IP-adress** -om du vill använda en offentlig IP-adress kan du ange den här. 
* **Nätverkssäkerhetsgrupp** -lämnar du standardinställningen (**grundläggande**) 
* **Offentliga inkommande portar** – om med en offentlig IP-adress, använder den här kontrollen för att tillåta åtkomst från SSH-trafik. 
* **Nätverksaccelerering** är inte tillgänglig för den här virtuella datorn.

Klicka på **nästa** att ange alternativ för hantering av:

* **Startdiagnostik** -ändra till **av**
* **OS gästen diagnostik** -lämna inaktiverad
* **Diagnostiklagringskonto** – du kan också markera eller ange ett nytt konto för att lagra diagnostikinformation.
* **Hanterad tjänstidentitet** – ändra det här alternativet om **på**, vilket skapar en Azure AD-tjänstens huvudnamn för kluster-styrenheten.
* **Automatisk avstängning** -lämna 

Nu kan du klicka på **granska + skapa** om du inte vill använda instanstaggar. Annars klickar du på **nästa** två gånger för att hoppa över den **gäst config** sidan och gå till sidan taggar. När du är klar det klickar du på **granska + skapa**. 

När dina val valideras, klickar du på den **skapa** knappen.  

Det tar fem eller sex minuter att skapa.

## <a name="create-a-storage-endpoint-if-using-azure-blob"></a>Skapa en slutpunkt för lagring (om du använder Azure Blob)

Om du använder Azure Blob storage för lagring av dina backend-data kan skapa du en slutpunkt för lagring i det virtuella nätverket. Detta [tjänstslutpunkt](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) behåller Azure Blob-trafik lokalt i stället för att dirigeras via internet.

1. I portalen klickar du på **virtuella nätverk** till vänster.
1. Välj det virtuella nätverket för din kontrollant. 
1. Klicka på **tjänstslutpunkter** till vänster.
1. Klicka på **Lägg till** högst upp.
1. Lämna tjänsten som ``Microsoft.Storage`` och välj den styrenheten undernät.
1. Längst ned på sidan, klickar du på **Lägg till**.

  ![Azure portal skärmbild med anteckningar för stegen för att skapa tjänsteslutpunkt](media/avere-vfxt-service-endpoint.png)

## <a name="gather-needed-inputs"></a>Samla in nödvändiga indata

Du behöver följande information för att skapa klustret. 

Om du har skapat noden domänkontrollant med hjälp av Resource Manager-mall kan du [hämta information från mallutdata](#finding-template-output). 

Krävs för att ansluta till hanteringsstyrenheten: 

* Controller användarnamn och SSH-nyckel eller lösenord
* IP-adress för kontrollenhet eller annan metod för att ansluta till VM-styrenhet

Krävs för att skapa kluster: 

* Resursgruppsnamn
* Azure-plats 
* Namn på virtuellt nätverk
* Namn på undernät
* Rollen för klusternodnamnet
* Lagringskontonamn om du skapar en Blob-behållare

Du kan också hitta saknas information genom att gå till sidan controller VM information. Klicka till exempel **alla resurser** och Sök efter namnet på och klicka sedan på Kontrollnamn att se detaljerna.

### <a name="finding-template-output"></a>Hitta mallutdata

Du hittar den här informationen från Resource Manager mallutdata genom att följa den här proceduren:

1. Klicka på meddelandeikonen i det översta fältet i Azure Portal, **går du till resursgruppen**. Detta visar den nya resursgruppen som innehåller din domänkontrollant och vnet.

   ![Meddelanden på portal med ”distribueringen lyckades” meddelande och ”gå till resursgruppen” och ”fäst på instrumentpanelen” knappar](media/avere-vfxt-browse-to-rg.png)

1. På vänster sida klickar du på **distributioner**, och sedan **Microsoft.Template**.

   ![Portalen sidan med resursgrupper med valt till vänster och Microsoft.Template som visar i en tabell under distributionsnamn-distributioner](media/avere-vfxt-deployment-template.png)

1. På vänster sida klickar du på **utdata**. Kopiera värdena i varje fält. 

   ![utdata-sida med SSHSTRING, RESOURCE_GROUP, plats, nätverk, UNDERNÄT och SUBNET_ID som visas i fält till höger om etiketter](media/avere-vfxt-template-outputs.png)

## <a name="access-the-controller"></a>Få åtkomst till kontrollanten

Om du vill göra resten av stegen för distributionen, måste du ansluta till kluster-styrenhet.

1. Metod för att ansluta till kluster-styrenhet beror på din konfiguration.

   * Om styrenheten har en offentlig IP-adress, SSH till den styrenheten IP-adress som administratörens användarnamn som du anger (till exempel ``ssh azureuser@40.117.136.91``).
   * Om den inte har en offentlig IP-adress, använder du en [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) eller en VPN-anslutning till ditt virtuella nätverk.

1. När du loggar in till en styrenhet, autentisera genom att köra `az login`. Kopiera den Autentiseringskod som angetts i gränssnittet och sedan använda en webbläsare för att läsa in [ https://microsoft.com/devicelogin ](https://microsoft.com/devicelogin) och autentisera med Microsoft-system. Gå tillbaka till gränssnitt för bekräftelse.

   ![Kommandoraden resultatet av kommandot ”AZ-inloggning” visa webbläsarkoden länk och autentisering](media/avere-vfxt-azlogin.png)

1. Lägg till din prenumeration genom att köra det här kommandot med ditt prenumerations-ID:  ```az account set --subscription YOUR_SUBSCRIPTION_ID```

## <a name="create-the-cluster-node-access-role"></a>Skapa klusterrollen noden åtkomst

> [!NOTE] 
> Om du inte är en prenumerationsägare och rollen inte redan har skapats, har en prenumerant gör så här eller använda proceduren i [skapa Avere vFXT runtime åtkomst klusterrollen utan en kontrollant](avere-vfxt-pre-role.md).

[Rollbaserad åtkomstkontroll](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC) ger klusternoderna vFXT behörighet att utföra åtgärderna.  

Som en del av normal vFXT klusteråtgärden, enskilda vFXT noder måste till exempel läsa Azure resursegenskaper, hantera lagring och styra inställningar för nätverksgränssnittet andra noder. 

1. Öppna på domänkontrollanten, den ``/avere-cluster.json`` filen i en textredigerare.

   ![konsolen visar en lista över kommando och sedan ”vi /avere-cluster.json”](media/avere-vfxt-open-role.png)

1. Redigera filen för att inkludera ditt prenumerations-ID och ta bort rad ovanför den. Spara filen som ``avere-cluster.json``.

   ![Konsolen textredigerare som visar prenumerations-ID och den ”ta bort den här raden” markeras för borttagning](media/avere-vfxt-edit-role.png)

1. Använd följande kommando för att skapa rollen:  

   ```bash
   az role definition create --role-definition /avere-cluster.json
   ```

Du skickar namnet på rollen till skriptet som skapar klustret i nästa steg. 

## <a name="create-nodes-and-configure-the-cluster"></a>Skapa noder och konfigurera klustret

Redigera ett exempelskript som ingår på styrenheten för att skapa Avere vFXT kluster, och kör den det. Exempel på skript finns i rotkatalogen (`/`) på klustret-styrenheten.

* Om du vill skapa en Blob-behållare om du vill använda som Avere-vFXT backend-lagringssystemet använder den ``create-cloudbacked-cluster`` skript.

* Om du ska lägga till lagring senare, använder du den ``create-minimal-cluster`` skript.

> [!TIP]
> Skapa prototyper skript för att lägga till noder och förstöra klustret vFXT ingår också i den `/` katalogen för klustret kontrollenheten VM.

### <a name="edit-the-deployment-script"></a>Redigera skriptet för distribution

Öppna exempelskriptet i en textredigerare. Du kanske vill spara det anpassade skriptet med ett annat namn för att undvika att skriva över den ursprungliga exemplet.

Ange värden för dessa skriptvariabler.

* Resursgruppsnamn

  * Om du använder nätverks- eller komponenter som finns i olika resursgrupper, ta bort kommentarerna variablerna och ange namnen också. 

```python
# Resource groups
# At a minimum specify the resource group.  If the network resources live in a
# different group, specify the network resource group.  Likewise for the storage
# account resource group.
RESOURCE_GROUP=
#NETWORK_RESOURCE_GROUP=
#STORAGE_RESOURCE_GROUP=
```

* Platsnamn
* Namn på virtuellt nätverk
* Namn på undernät
* Azure AD runtime rollnamn - om du har följt exemplet i [skapa klusterrollen noden åtkomst](#create-the-cluster-node-access-role), använda ``avere-cluster``. 
* Lagringskontonamn (om du skapar en ny blobbehållare)
* Klusternamnet – du kan inte ha två vFXT kluster med samma namn i samma resursgrupp. 
* Lösenordet för administratörer – Välj ett säkert lösenord för att övervaka och administration av klustret. Det här lösenordet tilldelas till användaren ``admin``. 
* Noden instanstyp - Se [vFXT nodstorlekar](avere-vfxt-deploy-plan.md#vfxt-node-sizes) information
* Nodstorlek för cache - Se [vFXT nodstorlekar](avere-vfxt-deploy-plan.md#vfxt-node-sizes) information

Spara filen och avsluta.

### <a name="run-the-script"></a>Kör skriptet
Kör skript genom att skriva det filnamn som du skapade. (Exempel: `./create-cloudbacked-cluster-west1`)  

Överväg att köra det här kommandot i en [terminal multiplexor](http://linuxcommand.org/lc3_adv_termmux.php) som `screen` eller `tmux` om du tappar bort din anslutning.  
Utdata loggas också i `~/vfxt.log`.

När skriptet har körts kan du kopiera hantering av IP-adress, vilket krävs för administration av klustret.

![Kommandoraden utdata från skriptet visar IP-adress för hantering slutet](media/avere-vfxt-mgmt-ip.png)

### <a name="next-step"></a>Nästa steg

Nu när klustret körs och du vet att dess IP-adress för hantering, kan du [ansluta till klustret konfigurationsverktyget](avere-vfxt-cluster-gui.md) att aktivera stöd för och lägga till lagring om det behövs.
