---
title: Distribuera Avere vFXT för Azure
description: Åtgärder för att distribuera Avere vFXT-klustret i Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: e70d1dfebcf25ee8f4e90a062cee6dd72a663e02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252602"
---
# <a name="deploy-the-vfxt-cluster"></a>Distribuera vFXT-klustret

Den här proceduren går igenom distributionsguiden som är tillgänglig från Azure Marketplace. Guiden distribuerar automatiskt klustret med hjälp av en Azure Resource Manager-mall. När du har angett parametrarna i formuläret och klickat på **Skapa**slutför Azure automatiskt dessa uppgifter:

* Skapar klusterstyrenheten, som är en grundläggande virtuell dator som innehåller den programvara som behövs för att distribuera och hantera klustret.
* Ställer in resursgrupp och virtuell nätverksinfrastruktur, inklusive att skapa nya element.
* Skapar virtuella datorer för klusternoder och konfigurerar dem som Avere-klustret.
* Om så önskas skapar du en ny Azure Blob-behållare och konfigurerar den som en klusterkärnfiler.

När du har följt instruktionerna i det här dokumentet får du ett virtuellt nätverk, ett undernät, en klusterstyrenhet och ett vFXT-kluster som visas i följande diagram. Det här diagrammet visar de valfria Azure Blob-kärnfilerna, som innehåller en ny Blob-lagringsbehållare (i ett nytt lagringskonto, visas inte) och en tjänstslutpunkt för Microsoft-lagring i undernätet.

![diagram som visar tre koncentriska rektanglar med Avere-klusterkomponenter. Den yttre rektangeln är märkt "Resursgrupp" och innehåller en hexagon märkt "Blob storage (valfritt)". Nästa rektangel i är märkt "Virtuellt nätverk: 10.0.0.0/16" och innehåller inga unika komponenter. Rektangeln längst är märkt "Undernät:10.0.0/24" och innehåller en vm-märkt "Klusterstyrenhet", en stapel med tre virtuella datorer med etiketten "vFXT-noder (vFXT-kluster)" och en hexagonmärkt "Tjänstslutpunkt". Det finns en pil som förbinder tjänstslutpunkten (som finns inuti undernätet) och blob-lagringen (som ligger utanför undernätet och vnet, i resursgruppen). Pilen passerar genom undernätet och virtuella nätverksgränser.](media/avere-vfxt-deployment.png)

Innan du använder mallen för att skapa måste du åtgärda följande förutsättningar:  

* [Ny prenumeration](avere-vfxt-prereqs.md#create-a-new-subscription)
* [Behörigheter för prenumerationsägare](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
* [Kvot för vFXT-klustret](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
* [Slutpunkt för lagringstjänster (om det behövs)](avere-vfxt-prereqs.md#create-a-storage-service-endpoint-in-your-virtual-network-if-needed) – Krävs för distributioner som använder ett befintligt virtuellt nätverk och skapar blob-lagring

Mer information om steg och planering av klusterdistribution finns i [Planera ditt Avere vFXT-system](avere-vfxt-deploy-plan.md) och [distributionsöversikt](avere-vfxt-deploy-overview.md).

## <a name="create-the-avere-vfxt-for-azure"></a>Skapa Avere vFXT för Azure

Få tillgång till skapandemallen i Azure-portalen genom att söka efter Avere och välja "Avere vFXT for Azure ARM Template".

![Webbläsarfönster som visar Azure-portalen med brödsmulor "New > Marketplace > Everything". På sidan Allt har sökfältet termen "avere" och det andra resultatet, "Avere vFXT for Azure ARM Template" beskrivs i rött för att markera den.](media/avere-vfxt-template-choose.png)

När du har läst informationen på sidan Avere vFXT för Azure ARM-mall klickar du på knappen **Skapa** för att börja.

![Azure-marknadsplats med den första sidan i distributionsmallen som visar](media/avere-vfxt-deploy-first.png)

Mallen är uppdelad i fyra steg - två informationsinsamlingssidor, plus validerings- och bekräftelsesteg.

* Sidan ett samlar in inställningar för klusterstyrenheten VM.
* Sidan två samlar in parametrar för att skapa klustret och ytterligare resurser som undernät och lagring.
* Sidan tre sammanfattar dina val och validerar konfigurationen.
* Sidan fyra förklarar villkoren för programvara och gör att du kan starta processen för att skapa kluster.

## <a name="page-one-parameters---cluster-controller-information"></a>Parametrar för sida ett - information om klusterstyrenhet

Den första sidan i distributionsmallen fokuserar på klusterstyrenheten.

![Första sidan i distributionsmallen](media/avere-vfxt-deploy-1.png)

Fyll i följande information:

* **Namn på klusterstyrenhet** - Ange namnet på den virtuella klusterstyrenheten.

* **Controller användarnamn** - Ange rot användarnamn för kluster controller VM.

* **Autentiseringstyp** - Välj antingen lösenord eller SSH-autentisering med offentliga nyckel för anslutning till styrenheten. Metoden för offentlig nyckel för SSH rekommenderas. läs [Hur du skapar och använder SSH-nycklar](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) om du behöver hjälp.

* **Lösenord** eller **SSH-offentlig nyckel** – Beroende på vilken autentiseringstyp du har valt måste du ange en RSA-offentlig nyckel eller ett lösenord i nästa fält. Den här autentiseringsen används med det användarnamn som angavs tidigare.

* **Prenumeration** - Välj prenumeration för Avere vFXT.

* **Resursgrupp** - Välj en befintlig tom resursgrupp för Avere vFXT-klustret, eller klicka på "Skapa ny" och ange ett nytt resursgruppnamn.

* **Plats** - Välj Azure-plats för ditt kluster och resurser.

Klicka på **OK** när du är klar.

> [!NOTE]
> Om du vill att klusterstyrenheten ska ha en offentlig IP-adress skapar du ett nytt virtuellt nätverk för klustret i stället för att välja ett befintligt nätverk. Den här inställningen finns på sidan två.

## <a name="page-two-parameters---vfxt-cluster-information"></a>Parametrar för sida två - vFXT-klusterinformation

På den andra sidan i distributionsmallen kan du ange klusterstorlek, nodtyp, cachestorlek och lagringsparametrar, bland andra inställningar.

![Andra sidan i distributionsmallen](media/avere-vfxt-deploy-2.png)

* **Antal avere vFXT-klusternoder** - Välj antalet noder i klustret. Minimum är tre noder och den maximala är tolv.

* **Lösenord för klusteradministration** - Skapa lösenordet för klusteradministration. Det här lösenordet används ```admin``` med användarnamnet för att logga in på klusterkontrollpanelen, där du kan övervaka klustret och konfigurera klusterinställningar.

* **Avere vFXT klusternamn** - Ge klustret ett unikt namn.

* **Storlek** - Det här avsnittet visar den VM-typ som ska användas för klusternoderna. Även om det bara finns ett rekommenderat alternativ öppnas en tabell med information om **den** här instanstypen och en länk till en priskalkylator.

* **Cachestorlek per nod** - Klustercachen är spridd över klusternoderna, så den totala cachestorleken på Avere vFXT-klustret kommer att vara den här storleken multiplicerad med antalet noder.

  Rekommenderad konfiguration: Använd 4 TB per nod för Standard_E32s_v3 noder.

* **Virtuellt nätverk** - Definiera ett nytt virtuellt nätverk för att hysa klustret, eller välj ett befintligt nätverk som uppfyller de förutsättningar som beskrivs i [Planera ditt Avere vFXT-system](avere-vfxt-deploy-plan.md#subscription-resource-group-and-network-infrastructure).

  > [!NOTE]
  > Om du skapar ett nytt virtuellt nätverk har klusterstyrenheten en offentlig IP-adress så att du kan komma åt det nya privata nätverket. Om du väljer ett befintligt virtuellt nätverk konfigureras klusterstyrenheten utan en offentlig IP-adress.
  >
  > En offentligt synlig IP-adress på klusterstyrenheten ger enklare åtkomst till vFXT-klustret, men skapar en liten säkerhetsrisk.
  >* Med en offentlig IP-adress på klusterstyrenheten kan du använda den som en hoppvärd för att ansluta till Avere vFXT-klustret utanför det privata undernätet.
  >* Om du inte har en offentlig IP-adress på styrenheten behöver du en annan hoppvärd, en VPN-anslutning eller ExpressRoute för att komma åt klustret. Använd till exempel ett befintligt virtuellt nätverk som redan har en VPN-anslutning konfigurerad.
  >* Om du skapar en styrenhet med en offentlig IP-adress bör du skydda styrenhetens virtuella dator med en nätverkssäkerhetsgrupp. Som standard skapar Avere vFXT för Azure-distributionen en nätverkssäkerhetsgrupp som begränsar inkommande åtkomst till endast port 22 för styrenheter med offentliga IP-adresser. Du kan ytterligare skydda systemet genom att låsa åtkomsten till ditt utbud av IP-källadresser - det vill säga tillåt bara anslutningar från datorer som du tänker använda för klusteråtkomst.

  Ett nytt virtuellt nätverk är också konfigurerat med en slutpunkt för lagringstjänster för Azure Blob-lagring och med nätverksåtkomstkontroll låst så att endast IP-adresser från klustrets undernät tillåts.

* **Undernät** - Välj ett undernät eller skapa ett nytt.

* **Skapa och använda blob-lagring** – Välj **true** för att skapa en ny Azure Blob-behållare och konfigurera den som backend-lagring för det nya Avere vFXT-klustret. Det här alternativet skapar också ett nytt lagringskonto i klustrets resursgrupp och skapar en Slutpunkt för Microsoft-lagringstjänster i klustret.
  
  Om du anger ett befintligt virtuellt nätverk måste den ha en slutpunkt för lagringstjänster innan du skapar klustret. (För mer information, läs [Planera ditt Avere vFXT-system](avere-vfxt-deploy-plan.md).)

  Ange det här fältet till **false** om du inte vill skapa en ny behållare. I det här fallet måste du bifoga och konfigurera lagring när du har skapat klustret. Läs [Konfigurera lagring](avere-vfxt-add-storage.md) för instruktioner.

* **(Nytt) lagringskonto** - Om du skapar en ny Azure Blob-behållare anger du ett namn för det nya lagringskontot.

## <a name="validation-and-purchase"></a>Validering och inköp

Sidan tre sammanfattar konfigurationen och validerar parametrarna. När valideringen har slutförts kontrollerar du sammanfattningen och klickar på **OK.**

> [!TIP]
> Du kan spara inställningarna för att skapa klustret genom att klicka på länken Hämta mall och parametrar bredvid OK.You can save this cluster's creation settings by clicking the **Download template and parameters** link next to the **OK** button. Den här informationen kan vara användbar om du behöver skapa ett liknande kluster senare, till exempel för att skapa ett ersättningskluster i ett katastrofåterställningsscenario. (Läs [vägledning om katastrofåterställning](disaster-recovery.md) om du vill veta mer.)

![Tredje sidan i distributionsmallen - validering](media/avere-vfxt-deploy-3.png)

Sidan fyra ger användarvillkor och länkar till sekretess- och prisinformation.

Ange eventuella kontaktinformation som saknas och klicka sedan på knappen **Skapa** för att acceptera villkoren och skapa Avere vFXT för Azure-kluster.

![Fjärde sidan i distributionsmallen - villkor, skapa knapp](media/avere-vfxt-deploy-4.png)

Klusterdistributionen tar 15-20 minuter.

## <a name="gather-template-output"></a>Samla mallutdata

När Avere vFXT-mallen är klar med att skapa klustret matar den ut viktig information om det nya klustret.

> [!TIP]
> Se till att kopiera **hanterings-IP-adressen** från mallutdata. Du behöver den här adressen för att administrera klustret.

Så här hittar du informationen:

1. Gå till resursgruppen för klusterstyrenheten.

1. På vänster sida klickar du på **Distributioner**och sedan **microsoft-avere.vfxt-mall**.

   ![Portalsida för resursgrupp med distributioner markerade till vänster och microsoft-avere.vfxt-mall som visas i en tabell under Distributionsnamn](media/avere-vfxt-outputs-deployments.png)

1. Klicka på **Utdata**till vänster. Kopiera värdena i vart och ett av fälten.

   ![utdatasida som visar SSHSTRING, RESOURCE_GROUP, PLATS, NETWORK_RESOURCE_GROUP, NÄTVERK, UNDERNÄT, SUBNET_ID, VSERVER_IPs och MGMT_IP värden i fält till höger om etiketterna](media/avere-vfxt-outputs-values.png)

## <a name="next-steps"></a>Nästa steg

Nu när klustret körs och du känner till dess hanterings-IP-adress [ansluter du till klusterkonfigurationsverktyget](avere-vfxt-cluster-gui.md).

Använd konfigurationsgränssnittet för att anpassa klustret, inklusive följande inställningsuppgifter:

* [Aktivera support](avere-vfxt-enable-support.md)
* [Lägg till lagringsutrymme](avere-vfxt-add-storage.md) (om det behövs)
