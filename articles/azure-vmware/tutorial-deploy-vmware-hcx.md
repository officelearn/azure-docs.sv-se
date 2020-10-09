---
title: Självstudie – distribuera och konfigurera VMware HCX
description: Lär dig hur du distribuerar och konfigurerar VMware HCX-lösningen för ditt privata moln i Azure VMware-lösningen.
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 69832d1537f0f1be95d3283f543ef6e54187b58d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91583441"
---
# <a name="deploy-and-configure-vmware-hcx"></a>Distribuera och konfigurera VMware HCX

I den här artikeln går vi igenom procedurerna för att distribuera och konfigurera VMware-HCX för ditt privata moln i Azure VMWare-lösningen. VMware HCX möjliggör migrering av VMware-arbetsbelastningar till Azure VMware-lösningen och andra anslutna platser via olika typer av migrering.

VMware HCX Advanced (fördistribuerat i Azure VMware-lösning) har stöd för upp till tre plats anslutningar (lokalt till molnet eller molnet till molnet). Om fler än tre plats anslutningar för VMware HCX Enterprise krävs kan du välja att aktivera [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) -tillägg (som för närvarande finns i för *hands version*) genom att skicka in en [supportbegäran](https://rc.portal.azure.com/#create/Microsoft.Support). VMware HCX Enterprise Edition (EE) är tillgänglig med Azure VMware-lösningen som en *förhands gransknings* funktion/tjänst. Medan VMware HCX EE för Azure VMware-lösningen är i för *hands version*, är det en kostnads fri funktion/tjänst och omfattas av förhands gransknings tjänstens allmänna villkor. När VMware HCX EE-tjänsten går till GA får du ett meddelande om 30 dagar på att faktureringen ska växlas över. Du kan också välja att stänga av eller välja bort tjänsten.

Innan du börjar bör du noga granska [innan du börjar](#before-you-begin), [program versions krav](#software-version-requirements)och [förutsättningar](#prerequisites). 

Sedan kommer vi att gå igenom alla nödvändiga procedurer för att:

> [!div class="checklist"]
> * Distribuera lokala VMware HCX-enheter (Connector)
> * Aktivera VMware-HCX
> * Para ihop din lokala miljö med din Azure VMware-lösnings miljö.
> * Konfigurera Interconnect (beräknings profil, nätverks profil (er) och service nät)
> * Slutför installationen genom att kontrol lera status för enheten.

När du är klar kan du följa de rekommenderade nästa stegen i slutet av den här artikeln.  

## <a name="before-you-begin"></a>Innan du börjar
   
* Läs igenom [självstudien](tutorial-network-checklist.md)om Azure VMware-lösning med programdefinierad Data Center (SDDC).
* Granska och referera till [VMware HCX-dokumentationen](https://docs.vmware.com/en/VMware-HCX/index.html), inklusive användar handboken för HCX.
* Granska VMware-dokument [migrera Virtual Machines med VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g).
* Du kan också gå igenom de [överväganden för distribution av VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html).
* Du kan också granska relaterade VMware-material på HCX, t. ex. VMware vSphere [blogg Serien](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) på HCX. 
* Du kan också begära en Azure VMware-lösning HCX Enterprise Activation via Azure VMware Solution support Channels.
* För att distribuera WAN Interconnect-installationen har specifika CIDR-intervall redan allokerats från kunden `\22` för det privata molnet som skapar.

Att ändra storlek på arbets belastningar mot beräknings-och lagrings resurser är ett viktigt planerings steg. Adressera storleks steget som en del av den ursprungliga planeringen av molnet i den ursprungliga miljön. 

Du kan också ändra arbets belastningar genom att slutföra en [utvärdering av Azure VMware-lösningen](../migrate/how-to-create-azure-vmware-solution-assessment.md) i Azure Migrate portalen.

## <a name="software-version-requirements"></a>Program versions krav

Infrastruktur komponenter måste köra den lägsta version som krävs. 
                                                         
| Komponent typ    | Käll miljö krav    | Mål miljö krav   |
| --- | --- | --- |
| vCenter Server   | 5,1<br/><br/>Om du använder 5,5 U1 eller tidigare använder du det fristående HCX-användargränssnittet för HCX-åtgärder.  | 6,0 U2 och senare   |
| ESXi   | 5,0    | ESXi 6,0 och senare   |
| NSX    | För HCX nätverks tillägg för logiska växlar vid källan: NSXv 6.2 + eller NSX-T 2.4 +   | NSXv 6.2 + eller NSX-T 2.4 +<br/><br/>För HCX närhet: NSXv 6.4 + (närhets dirigering stöds inte med NSX-T) |
| vCloud-regissör   | Krävs inte – ingen interoperabilitet med vCloud Director på käll platsen | När du integrerar mål miljön med vCloud Director är minimivärdet 9.1.0.2.  |

## <a name="prerequisites"></a>Krav

### <a name="network-and-ports"></a>Nätverk och portar

* [ExpressRoute Global Reach](tutorial-expressroute-global-reach-private-cloud.md) konfigurerade mellan lokala och Azure VMware-lösningar SDDC ExpressRoute-kretsar.

* Alla portar som krävs måste vara öppna för kommunikation mellan lokala komponenter och lokal till Azure VMware-SDDC (se [VMware HCX-dokumentation](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)).

* Lokala HCX IX-och NE-enheter ska kunna uppnå vCenter-och ESXi-infrastruktur.

### <a name="ip-addresses"></a>IP-adresser

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>Distribuera VMware HCX-ägg lokalt

>[!NOTE]
>Innan du distribuerar den virtuella installationen till din lokala vCenter måste du ladda ned VMware HCX-enheterna. 

1. Logga in på Azure VMware Solution HCX Manager på `https://x.x.x.9` port 443 med **cloudadmin** -användarautentiseringsuppgifter och gå sedan till **support**.

   >[!TIP]
   >Om du vill identifiera IP-adressen för HCX Manager går du till **Hantera**anslutning i bladet Azure VMware-lösning  >  **Connectivity** och väljer sedan fliken **HCX** . 
   >
   >VCenter-lösenordet definierades när du konfigurerade det privata molnet.

1. Välj **nedladdnings** länken för att ladda ned VMware HCX-filen med embryon.

1. Gå till den lokala vCenter och välj en OVF-mall, som är den ägg filen som du laddade ned, för att distribuera till din lokala vCenter.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="Gå till den lokala vCenter och välj en OVF-mall för att distribuera till din lokala vCenter." lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. Välj ett namn och en plats, en resurs/ett kluster där du distribuerar HCX och granska informationen och de resurser som krävs.  

   :::image type="content" source="media/tutorial-vmware-hcx/configure-template.png" alt-text="Gå till den lokala vCenter och välj en OVF-mall för att distribuera till din lokala vCenter." lightbox="media/tutorial-vmware-hcx/configure-template.png":::

1. Granska licens villkoren, och om du samtycker, väljer du lagring och nätverk som krävs och väljer sedan **Nästa**.

1. I **Anpassa mall**anger du all information som krävs. 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="Gå till den lokala vCenter och välj en OVF-mall för att distribuera till din lokala vCenter." lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. Välj **Nästa**, verifiera konfigurationen och välj sedan **Slutför** för att distribuera HCX-ägg.
     
   >[!NOTE]
   >I allmänhet distribueras VMware HCX Manager som du distribuerar nu till klustrets hanterings nätverk.  
   
   > [!IMPORTANT]
   > När distributionen är klar kan du behöva sätta på den virtuella enheten manuellt.
   > Vänta 10-15 minuter efter att HCX-installationen har startats för att gå vidare till nästa steg.

En översikt över slut punkt till slut punkt av det här steget finns i [Azure VMware-lösningen – VMware HCX-installation av utrustning](https://www.youtube.com/embed/BwSnQeefnso) . 


## <a name="activate-vmware-hcx"></a>Aktivera VMware-HCX

När du har distribuerat VMware HCX-enheter lokalt är du redo att aktivera VMware HCX. Innan du kan aktivera VMware HCX måste du hämta en licens.

1. I Azure VMware-lösningen går du till **Hantera**  >  **anslutning**, väljer fliken **HCX** och väljer sedan **Lägg till**.

1. Logga in på den lokala VMware HCX Manager på `https://HCXManagerIP:9443` och logga in **Administratörs** användarens autentiseringsuppgifter. 

   > [!IMPORTANT]
   > Se till att ta `9443` med port numret med VMware HCX Manager-IP-adressen.

1. Ange din **HCX Advanced-nyckel**i **licensiering**.  
   
    > [!NOTE]
    > VMware HCX Manager måste ha öppen Internet åtkomst eller en proxy konfigurerad.

1. På **Data Center plats**anger du den närmaste platsen där du installerar VMware HCX Manager lokalt.

1. Ändra **system namnet** eller acceptera standardvärdet.
   
1. Du kan antingen avsluta eller fortsätta genom att välja alternativet **Ja. Fortsätt** sedan.
    
1. I **Anslut din vCenter**, anger du FQDN-namnet eller IP-adressen för vCenter-servern och rätt autentiseringsuppgifter och väljer sedan **Fortsätt**.
   
1. I **Konfigurera SSO/PSC**anger du FQDN eller IP-adressen för din PSC och väljer sedan **Fortsätt**.
   
   >[!NOTE]
   >Vanligt vis samma som vCenter FQDN/IP.

1. Kontrol lera att alla indata är korrekta och välj **starta om**.
    
   > [!NOTE]
   > Du får en fördröjning efter omstarten innan du uppmanas att ange nästa steg.
   >
   >När tjänsterna har startats om är det viktigt att du ser vCenter som visas som grönt på skärmen som visas. Både vCenter och SSO har lämpliga konfigurations parametrar, vilket bör vara samma som föregående skärm.

   :::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="Gå till den lokala vCenter och välj en OVF-mall för att distribuera till din lokala vCenter." lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

En översikt över slut punkt till slut punkt av det här steget finns i [Azure VMware-lösningen – VMware HCX Activation](https://www.youtube.com/embed/BkAV_TNYxdE) video.


## <a name="configure-vmware-hcx"></a>Konfigurera VMware-HCX

Nu är du redo att lägga till en webbplats koppling, skapa en nätverks-och beräknings profil och aktivera tjänster, t. ex. migrering, nätverks tillägg eller haveri beredskap. 

### <a name="add-a-site-pairing"></a>Lägg till en webbplats ihopparning

Du kan ansluta (para ihop) VMware HCX Manager i Azure VMware-lösningen med VMware HCX Manager i ditt data Center. 

1. Logga in på din lokala vCenter och välj **HCX**under **Start**.

   :::image type="content" source="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png" alt-text="Gå till den lokala vCenter och välj en OVF-mall för att distribuera till din lokala vCenter." lightbox="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png":::

1. Under **infrastruktur**väljer du **plats par**och väljer sedan alternativet **Anslut till fjärrplats** (i mitten av skärmen). 

   :::image type="content" source="media/tutorial-vmware-hcx/connect-remote-site.png" alt-text="Gå till den lokala vCenter och välj en OVF-mall för att distribuera till din lokala vCenter." lightbox="media/tutorial-vmware-hcx/connect-remote-site.png":::

1. Ange den **fjärranslutna HCX-URL: en eller IP-adressen**, cloudadmin@vsphere.local användar namnet och **lösen ordet**för Azure VMware-lösningen och välj sedan **Anslut**.

   > [!NOTE]
   > Den **fjärranslutna HCX-URL** : en är din Azure VMware-lösning privat Cloud HCX Manager, vanligt vis adressen ". 9" för hanterings nätverket.  Om din vCenter till exempel är 192.168.4.2, blir din HCX-URL 192.168.4.9.
   >
   > **Lösen ordet** är samma lösen ord som du använde för att logga in på vCenter. Du definierade det här lösen ordet på den första distributions skärmen.

   Du ser en skärm som visar din HCX Manager i Azure VMware-lösningen och din HCX Manager lokalt ansluten (länkad).

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Gå till den lokala vCenter och välj en OVF-mall för att distribuera till din lokala vCenter.":::

En översikt över slut punkt till slut punkt av det här steget finns i [Azure VMware-lösningen – VMware HCX site ihopparning](https://www.youtube.com/embed/sKizDCRHOko) video.



### <a name="create-network-profiles"></a>Skapa nätverks profiler

VMware HCX distribuerar ett par virtuella apparater (automatiserade) men behöver flera IP-segment.  När du skapar nätverks profiler definierar du de IP-segment som du identifierade under [förberedelse och planering av VMware HCX-nätverks segment för distribution](production-ready-deployment-steps.md#vmware-hcx-network-segments).

Du kommer att skapa fyra nätverks profiler:

   - Hantering
   - vMotion
   - Replikering
   - Ordna

1. Under **infrastruktur**väljer du **Interconnect**  >  nätverks profiler för**multipl-site service nät**  >  **Network Profiles**  >  **skapa nätverks profil**.

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="Gå till den lokala vCenter och välj en OVF-mall för att distribuera till din lokala vCenter." lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. För varje nätverks profil väljer du nätverket, Port grupp, anger ett namn, skapar IP-poolen för det specifika segmentet och väljer sedan **skapa**. 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="Gå till den lokala vCenter och välj en OVF-mall för att distribuera till din lokala vCenter.":::

En översikt över slut punkt till slut punkt av det här steget finns i [Azure VMware-lösningen – VMware HCX Create Network Profile](https://www.youtube.com/embed/NhyEcLco4JY) video.


### <a name="create-compute-profile"></a>Skapa beräknings profil

1. Välj **beräknings profiler**  >  **skapa beräknings profil**.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="Gå till den lokala vCenter och välj en OVF-mall för att distribuera till din lokala vCenter." lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. Ange ett namn för profilen och välj **Fortsätt**.  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="Gå till den lokala vCenter och välj en OVF-mall för att distribuera till din lokala vCenter." lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. Välj de tjänster som ska aktive ras, till exempel migrering, nätverks tillägg eller haveri beredskap, och välj sedan **Fortsätt**.
  
   > [!NOTE]
   > Normalt kommer inga ändringar att göras här.

1. I **Välj tjänst resurser**väljer du en eller flera tjänst resurser (kluster) som ska aktive ras för de valda VMware HCX-tjänsterna.  

1. När du ser kluster i ditt lokala data Center väljer du **Fortsätt**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="Gå till den lokala vCenter och välj en OVF-mall för att distribuera till din lokala vCenter." lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. I **Välj data lager**väljer du lagrings resursen lagring för att distribuera VMware HCX Interconnect-apparater och väljer sedan **Fortsätt**.

   När flera resurser är markerade använder VMware HCX den första resursen som väljs tills dess kapacitet är slut.   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="Gå till den lokala vCenter och välj en OVF-mall för att distribuera till din lokala vCenter." lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. Välj **hanterings nätverks profil**, hanterings nätverks profilen som du skapade i föregående steg och välj sedan **Fortsätt**.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="Gå till den lokala vCenter och välj en OVF-mall för att distribuera till din lokala vCenter." lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

   > [!NOTE]
   > Med hanterings nätverkets profil kan VMware HCX-installationer kommunicera med vCenter och ESXi-värdarna kan nås.

1. Välj **nätverks profil för överordnad**länk, nätverks profilen för överordnad länk som du skapade i föregående steg och välj sedan **Fortsätt**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="Gå till den lokala vCenter och välj en OVF-mall för att distribuera till din lokala vCenter." lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. Välj **vMotion nätverks profil**, nätverks profilen vMotion som du skapade i föregående steg och välj sedan **Fortsätt**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="Gå till den lokala vCenter och välj en OVF-mall för att distribuera till din lokala vCenter." lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. Välj **nätverks profil för vSphere-replikering**, den nätverks profil för replikering som du skapade i föregående steg och välj sedan **Fortsätt**.

   I de flesta fall är nätverks profilen för replikering samma som nätverks profilen för hantering.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="Gå till den lokala vCenter och välj en OVF-mall för att distribuera till din lokala vCenter." lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. Välj **distribuerade växlar för nätverks tillägg**, de distribuerade virtuella växlar som innehåller de virtuella datorer som ska migreras till Azure VMware-lösningen på ett utökat nätverk i Layer 2 och välj sedan **Fortsätt**.

   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="Gå till den lokala vCenter och välj en OVF-mall för att distribuera till din lokala vCenter." lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. Granska anslutnings reglerna och välj **Fortsätt**.  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="Gå till den lokala vCenter och välj en OVF-mall för att distribuera till din lokala vCenter." lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. Klicka på **Slutför** för att skapa beräknings profilen.

   Du ser en skärm som liknar den som visas nedan.

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="Gå till den lokala vCenter och välj en OVF-mall för att distribuera till din lokala vCenter." lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

En översikt över slut punkt till slut punkt av det här steget finns i [Azure VMware-lösningen – VMware HCX Create Compute Profile](https://www.youtube.com/embed/qASXi5xrFzM) video.




### <a name="create-service-mesh"></a>Skapa ett tjänst nät

Nu är det dags att konfigurera service nät mellan lokala och Azure VMware-SDDC.

1. Under **infrastruktur**väljer du **Interconnect**  >  **service-nät**  >  **skapa tjänst nät**.    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Gå till den lokala vCenter och välj en OVF-mall för att distribuera till din lokala vCenter." lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Granska de platser som är ifyllda och välj sedan **Fortsätt**. 

   >[!NOTE]
   >Om det här är din första tjänst nät konfiguration behöver du inte ändra den här skärmen.  

1. Välj List rutan för källans och fjär beräknings profilerna och välj sedan **Fortsätt**.  

   Valen definierar de resurser där de virtuella datorerna kan använda VMware HCX-tjänster.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="Gå till den lokala vCenter och välj en OVF-mall för att distribuera till din lokala vCenter." lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="Gå till den lokala vCenter och välj en OVF-mall för att distribuera till din lokala vCenter." lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Granska tjänster som ska aktive ras och välj sedan **Fortsätt**.  

1. I **Avancerad konfiguration – Åsidosätt överordnad överordnad nätverks profil** Välj **Fortsätt**.  Nätverks profiler för överordnad länk ansluter till nätverket via vilket fjärrplatsens sammanlänknings utrustning kan nås.  
  
1. I **Avancerad konfiguration – nätverks tilläggets utrustning skala ut**, granska och välj **Fortsätt**. 

1. I **Avancerad konfiguration – trafik teknik**, granska och gör de ändringar du behöver och välj sedan **Fortsätt**.

1. Granska förhands granskning av topologi och välj **Fortsätt**.

1. Ange ett användarvänligt namn för det här Service nätet och välj **Slutför** för att slutföra.  

1. Välj **Visa aktiviteter** för att övervaka distributionen. 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="Gå till den lokala vCenter och välj en OVF-mall för att distribuera till din lokala vCenter.":::

   När service nät distributionen har slutförts visas tjänsterna som gröna.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="Gå till den lokala vCenter och välj en OVF-mall för att distribuera till din lokala vCenter." lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. Kontrol lera tjänstens nät hälsa genom att kontrol lera status för enheten, Välj **Interconnect**-  >  **enheter**.

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="Gå till den lokala vCenter och välj en OVF-mall för att distribuera till din lokala vCenter." lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

En översikt över slut punkt till slut punkt av det här steget finns i [Azure VMware-lösningen – VMware HCX Create service nät](https://www.youtube.com/embed/FyZ0d3P_T24) video.



### <a name="optional-create-a-network-extension"></a>Valfritt Skapa ett nätverks tillägg

Om du vill utöka alla nätverk från din lokala miljö till Azure VMware-lösningen kan du utöka nätverken genom att följa dessa steg.

1. Under **tjänster**väljer du **nätverks tillägg**och väljer sedan **skapa ett nätverks tillägg**.

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="Gå till den lokala vCenter och välj en OVF-mall för att distribuera till din lokala vCenter." lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Välj vart och ett av de nätverk som du vill utöka till Azure VMware-lösningen och välj sedan **Nästa**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="Gå till den lokala vCenter och välj en OVF-mall för att distribuera till din lokala vCenter.":::

1. Ange den lokala gateway-IP: en för vart och ett av de nätverk som du vill utöka och välj sedan **Skicka**. 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="Gå till den lokala vCenter och välj en OVF-mall för att distribuera till din lokala vCenter.":::

   Det tar några minuter för nätverks tillägget att slutföras. När du gör det visas status ändringen till **tillägget slutförd**.

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="Gå till den lokala vCenter och välj en OVF-mall för att distribuera till din lokala vCenter." lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

En översikt över slut punkt till slut punkt av det här steget finns i [Azure VMware-lösningen – VMware HCX Network Extension](https://www.youtube.com/embed/cNlp0f_tTr0) video.


## <a name="next-steps"></a>Nästa steg

Om du har nått den här punkten och statusen för apparatens Interconnect-tunnel är **upp** och grön, kan du migrera och skydda virtuella datorer i Azure VMware-lösningen med VMware HCX.  Azure VMware-lösningen stöder migrering av arbets belastningar (med eller med ett nätverks tillägg).  Du kan fortfarande utföra migrering av arbets belastningar i din vSphere-miljö lokalt och med att skapa nätverk och distribuera virtuella datorer till dessa nätverk.  Mer information finns i [VMware HCX-dokumentationen](https://docs.vmware.com/en/VMware-HCX/index.html) och [migrera Virtual Machines med VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) i den tekniska dokumentationen för VMware för mer information om hur du använder HCX.
