---
title: Självstudie – distribuera och konfigurera VMware HCX
description: Lär dig hur du distribuerar och konfigurerar en VMware HCX-lösning för ditt privata moln i Azure VMware-lösningen.
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: 424abeef567d88f7de37f7a7a4ab7a7a8b6ef3bc
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791417"
---
# <a name="deploy-and-configure-vmware-hcx"></a>Distribuera och konfigurera VMware HCX

I den här artikeln går vi igenom procedurerna för att distribuera och konfigurera den lokala VMware HCX-anslutningen för ditt privata moln i Azure VMware-lösningen. Med VMware HCX kan du migrera dina VMware-arbetsbelastningar till Azure VMware-lösningen och andra anslutna platser via olika typer av migrering. Eftersom Azure VMware-lösningen distribuerar och konfigurerar HCX Cloud Manager måste du ladda ned, aktivera och konfigurera HCX-anslutningen i ditt lokala VMware-datacenter.

VMware HCX Advanced Connector förinstalleras i Azure VMware-lösningen. Det har stöd för upp till tre plats anslutningar (lokalt till molnet eller molnet till molnet). Om du behöver fler än tre plats anslutningar skickar du en [supportbegäran](https://rc.portal.azure.com/#create/Microsoft.Support) för att aktivera [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) -tillägget. Tillägget är för närvarande en för hands version. 

>[!NOTE]
>VMware HCX Enterprise Edition (EE) är tillgänglig med Azure VMware-lösningen som en för hands versions tjänst. Det är kostnads fritt och omfattas av villkor för en för hands versions tjänst. När VMware HCX EE-tjänsten är allmänt tillgänglig får du ett meddelande om 30 dagar på att faktureringen ska växlas över. Du kan också välja att stänga av eller avanmäla tjänsten. Pease Obs! det finns för närvarande ingen enkel nedgradering från HCX Enterprise till HCX Advanced, och kunder som väljer att nedgradera måste distribueras om efter stillestånds tiden.

Börja med att läsa igenom [innan du börjar](#before-you-begin), [program varu versions krav och krav](#software-version-requirements) [avsnitt i](#prerequisites) den här artikeln. 

Sedan kommer vi att gå igenom alla nödvändiga procedurer för att:

> [!div class="checklist"]
> * Distribuera lokal VMware HCX-EMBRYOn (HCX-anslutningsprogram).
> * Aktivera VMware HCX-anslutningen.
> * Para ihop din lokala HCX-anslutning med din Azure VMware-lösning HCX Cloud Manager.
> * Konfigurera Interconnect (nätverks profil, beräknings profil och service nät).
> * Slutför installationen genom att kontrol lera statusen för installationen och verifiera att migreringen är möjlig.

När du är klar kan du följa de rekommenderade nästa stegen i slutet av den här artikeln.  

## <a name="before-you-begin"></a>Innan du börjar
   
* Läs igenom [självstudien](tutorial-network-checklist.md)om Azure VMware Solution Software-Defined Data Center (SDDC).
* Granska och referera till [VMware HCX-dokumentationen](https://docs.vmware.com/en/VMware-HCX/index.html), inklusive användar handboken för HCX.
* Granska [migrering Virtual Machines med VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) i VMware-dokument.
* Du kan också gå igenom de [överväganden för distribution av VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html).
* Du kan också granska relaterade VMware-material på HCX, t. ex. VMware vSphere [blogg Serien](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html). 
* Du kan också begära en Azure VMware-lösning HCX Enterprise Activation via Azure VMware Solution support Channels.
* Du kan också [Granska nätverks portar som krävs för HCX](https://ports.vmware.com/home/VMware-HCX).
* Även om Azure VMware-lösningen HCX Cloud Manager kommer att förkonfigureras från det/22-nätverk som tillhandahålls för Azure VMware-lösningens privata moln, kräver den lokala HCX-anslutningen att du allokerar nätverks intervall från ditt lokala nätverk. Dessa nätverk och intervall beskrivs längre fram i den här artikeln.

Att ändra storlek på arbets belastningar mot beräknings-och lagrings resurser är ett viktigt planerings steg. Adressera storleks steget som en del av den första planeringen för en privat moln miljö. 

Du kan ändra storlek på arbets belastningar genom att slutföra en [Azure VMware-lösning](../migrate/how-to-create-azure-vmware-solution-assessment.md) i Azure Migrate portalen.

## <a name="software-version-requirements"></a>Program versions krav

Infrastruktur komponenter måste köra den lägsta version som krävs. 
                                                         
| Komponent typ    | Käll miljö krav    | Mål miljö krav   |
| --- | --- | --- |
| vCenter Server   | 5,1<br/><br/>Om du använder 5,5 U1 eller tidigare använder du det fristående HCX-användargränssnittet för HCX-åtgärder.  | 6,0 U2 och senare   |
| ESXi   | 5,0    | ESXi 6,0 och senare   |
| NSX    | För HCX nätverks tillägg för logiska växlar vid källan: NSXv 6.2 + eller NSX-T 2.4 +.   | NSXv 6.2 + eller NSX-T 2.4 +<br/><br/>För HCX närhet: NSXv 6.4 +. (Närhet routning stöds inte med NSX-T.) |
| vCloud-regissör   | Krävs inte. Det finns ingen samverkan med vCloud Director på käll platsen. | När du integrerar mål miljön med vCloud Director är minimivärdet 9.1.0.2.  |

## <a name="prerequisites"></a>Förutsättningar

### <a name="network-and-ports"></a>Nätverk och portar

* Konfigurera [Azure ExpressRoute-Global Reach](tutorial-expressroute-global-reach-private-cloud.md) mellan lokala och Azure VMware-lösningar SDDC ExpressRoute-kretsar.

* [Alla portar som krävs](https://ports.vmware.com/home/VMware-HCX) måste vara öppna för kommunikation mellan lokala komponenter och Azure VMware-SDDC.

Mer information finns i [VMware HCX-dokumentationen](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html).


### <a name="ip-addresses"></a>IP-adresser

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="deploy-the-vmware-hcx-connector-ova-on-premises"></a>Distribuera VMware HCX Connector-ägg lokalt

> [!NOTE]
> Innan du distribuerar den virtuella installationen till din lokala vCenter måste du ladda ned VMware HCX Connector-ägg. 

1. Öppna ett webbläsarfönster och logga in på Azure VMware Solution HCX Manager på `https://x.x.x.9` port 443 med **cloudadmin** -användarautentiseringsuppgifter och gå sedan till **support** .

   > [!TIP]
   > Observera IP-adressen för HCX Cloud Manager i Azure VMware-lösningen. Identifiera IP-adressen genom att gå till **Hantera** anslutningar i fönstret Azure VMware-lösning  >  **Connectivity** och sedan välja fliken **HCX** . 
   >
   >VCenter-lösenordet definierades när du konfigurerade det privata molnet.

1. Välj länken **Hämta** för att ladda ned VMware HCX Connector-ägg filen.

1. Gå till din lokala vCenter. Välj en OVF-mall, som är den ägg filen som du laddade ned, för att distribuera HCX-anslutningen till din lokala vCenter.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="Skärm bild av surfning till en OVF-mall." lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. Välj ett namn och en plats och välj en resurs/ett kluster där du distribuerar HCX-anslutningen. Granska sedan informationen och de resurser som krävs.  

   :::image type="content" source="media/tutorial-vmware-hcx/configure-template.png" alt-text="Skärm bild av surfning till en OVF-mall." lightbox="media/tutorial-vmware-hcx/configure-template.png":::

1. Läs igenom licens villkoren. Om du samtycker väljer du önskad lagring och nätverk och väljer sedan **Nästa** .

1. I **Anpassa mall** anger du all information som krävs. 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="Skärm bild av surfning till en OVF-mall." lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. Välj **Nästa** , verifiera konfigurationen och välj sedan **Slutför** för att distribuera HCX Connector-ägg.
     
   > [!NOTE]
   > I allmänhet distribueras den VMware HCX-anslutning som du distribuerar nu till klustrets hanterings nätverk.  
   
   > [!IMPORTANT]
   > Du kan behöva aktivera den virtuella enheten manuellt.  Om så är fallet väntar du 10-15 minuter innan du fortsätter till nästa steg.

En översikt över slut punkt till slut punkt av den här proceduren finns i [Azure VMware-lösningen: HCX installation](https://www.youtube.com/embed/BwSnQeefnso) av enhets distribution. 


## <a name="activate-vmware-hcx"></a>Aktivera VMware-HCX

När du har distribuerat VMware HCX Connector-dataäggen lokalt och startar installationen, är du redo att aktivera. Först måste du skaffa en licens nyckel från Azure VMware Solution Portal.

1. I Azure VMware Solution portal går du till **Hantera**  >  **anslutning** , väljer fliken **HCX** och väljer sedan **Lägg till** .

1. Använd **Administratörs** behörighet för att logga in på den lokala VMware HCX Manager på `https://HCXManagerIP:9443` . 

   > [!IMPORTANT]
   > Se till att ta `9443` med port numret med VMware HCX Manager-IP-adressen.

1. I **licensiering** anger du nyckeln för **HCX Advanced Key** .  
   
    > [!NOTE]
    > VMware HCX Manager måste ha öppen Internet åtkomst eller en proxy konfigurerad.

1. På **Data Center plats** anger du den närmaste platsen för att installera VMware HCX Manager lokalt.

1. I **system namn** ändrar du namnet eller accepterar standardvärdet.
   
1. Välj **Ja, Fortsätt** .
    
1. I **Anslut din vCenter** , anger du FQDN-namnet eller IP-adressen för vCenter-servern och rätt autentiseringsuppgifter och väljer sedan **Fortsätt** .
   
1. I **Konfigurera SSO/PSC** anger du FQDN eller IP-adressen för din plattforms tjänst kontroll och väljer sedan **Fortsätt** .
   
   >[!NOTE]
   >Den här posten är vanligt vis samma som vCenter-FQDN eller IP-adress.

1. Kontrol lera att alla indata är korrekta och välj **starta om** .
    
   > [!NOTE]
   > Du får en fördröjning efter omstarten innan du uppmanas att ange nästa steg.

När tjänsterna har startats om är det viktigt att du ser vCenter som visas som grönt på skärmen som visas. Både vCenter och SSO måste ha lämpliga konfigurations parametrar, vilket bör vara samma som föregående skärm.

:::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="Skärm bild av surfning till en OVF-mall." lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

En översikt över slut punkt till slut punkt för den här proceduren finns i [Azure VMware-lösningen: Aktivera HCX](https://www.youtube.com/embed/BkAV_TNYxdE) video.


## <a name="configure-the-vmware-hcx-connector"></a>Konfigurera VMware HCX-anslutningen

Nu är du redo att lägga till en webbplats koppling, skapa en nätverks-och beräknings profil och aktivera tjänster som migrering, nätverks tillägg eller haveri beredskap. 

### <a name="add-a-site-pairing"></a>Lägg till en webbplats ihopparning

Du kan ansluta (para ihop) VMware HCX Cloud Manager i Azure VMware-lösningen med VMware HCX-anslutningsprogrammet i ditt data Center. 

1. Logga in på din lokala vCenter och välj **HCX** under **Start** .

   :::image type="content" source="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png" alt-text="Skärm bild av surfning till en OVF-mall." lightbox="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png":::

1. Under **infrastruktur** väljer du **plats par** och väljer sedan alternativet **Anslut till fjärrplats** (i mitten av skärmen). 

   :::image type="content" source="media/tutorial-vmware-hcx/connect-remote-site.png" alt-text="Skärm bild av surfning till en OVF-mall." lightbox="media/tutorial-vmware-hcx/connect-remote-site.png":::

1. Ange den fjärranslutna HCX-URL eller IP-adress som du noterade tidigare, Azure VMware-lösningens cloudadmin@vsphere.local användar namn och lösen ord. Välj **Anslut** .

   > [!NOTE]
   > För att ett plats par ska kunna upprättas måste din HCX-anslutning kunna dirigera till din HCX Cloud Manager-IP via port 443.
   >
   > Lösen ordet är samma lösen ord som du använde för att logga in på vCenter. Du definierade det här lösen ordet på den första distributions skärmen.

   Du ser en skärm som visar att din HCX Cloud Manager i Azure VMware-lösningen och din lokala HCX-anslutning är ansluten (länkad).

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Skärm bild av surfning till en OVF-mall.":::

En översikt över slut punkt till slut punkt av den här proceduren finns i [Azure VMware-lösningen: HCX site ihopparning](https://www.youtube.com/embed/sKizDCRHOko) video.



### <a name="create-network-profiles"></a>Skapa nätverks profiler

VMware HCX distribuerar en delmängd av virtuella apparater (automatisk) som kräver flera IP-segment. När du skapar nätverks profiler definierar du de IP-segment som du identifierade under [förberedelse och planering av VMware HCX-nätverks segment för distribution](production-ready-deployment-steps.md#vmware-hcx-network-segments).

Du skapar fyra nätverks profiler:

   - Hantering
   - vMotion
   - Replikering
   - Ordna

1. Under **infrastruktur** väljer du **Interconnect**  >  nätverks profiler för **multipl-site service nät**  >  **Network Profiles**  >  **skapa nätverks profil** .

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="Skärm bild av surfning till en OVF-mall." lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. För varje nätverks profil väljer du nätverket och port gruppen, anger ett namn och skapar IP-poolen för segmentet. Välj sedan **Skapa** . 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="Skärm bild av surfning till en OVF-mall.":::

En översikt över slut punkt till slut punkt av den här proceduren finns i [Azure VMware-lösningen: HCX Network Profile](https://www.youtube.com/embed/NhyEcLco4JY) video.


### <a name="create-a-compute-profile"></a>Skapa en beräknings profil

1. Välj **beräknings profiler**  >  **skapa beräknings profil** .

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="Skärm bild av surfning till en OVF-mall." lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. Ange ett namn för profilen och välj **Fortsätt** .  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="Skärm bild av surfning till en OVF-mall." lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. Välj de tjänster som ska aktive ras, till exempel migrering, nätverks tillägg eller haveri beredskap, och välj sedan **Fortsätt** .
  
   > [!NOTE]
   > Normalt ändras ingenting här.

1. I **Välj tjänst resurser** väljer du en eller flera tjänst resurser (kluster) för att aktivera de valda VMware HCX-tjänsterna.  

1. När du ser kluster i ditt lokala data Center väljer du **Fortsätt** .

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="Skärm bild av surfning till en OVF-mall." lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. Välj data lager lagrings resurs för att distribuera VMware HCX Interconnect-enheter från **Välj data lager** . Välj sedan **Fortsätt** .

   När flera resurser är markerade använder VMware HCX den första resursen som väljs tills dess kapacitet är slut.   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="Skärm bild av surfning till en OVF-mall." lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. Välj den nätverks profil för hantering som du skapade i föregående steg i **Välj hanterings nätverks profil** . Välj sedan **Fortsätt** .  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="Skärm bild av surfning till en OVF-mall." lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

   > [!NOTE]
   > Med hanterings nätverkets profil kan VMware HCX-enheter kommunicera med vCenter. ESXi-värdarna kan nås via den här profilen.

1. Välj den nätverks profil för överordnad länk som du skapade i föregående steg från **Välj Nätverks profil för överordnad länk** . Välj sedan **Fortsätt** .

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="Skärm bild av surfning till en OVF-mall." lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. Från **Välj vMotion nätverks profil** väljer du nätverks profilen vMotion som du skapade i föregående steg. Välj sedan **Fortsätt** .

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="Skärm bild av surfning till en OVF-mall." lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. Välj den nätverks profil för replikering som du skapade i föregående steg från **Välj vSphere-replikeringsgruppens nätverks profil** . Välj sedan **Fortsätt** .

   I de flesta fall är nätverks profilen för replikering samma som nätverks profilen för hantering.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="Skärm bild av surfning till en OVF-mall." lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. Från **Välj distribuerade växlar för nätverks tillägg** väljer du de distribuerade virtuella växlar som innehåller de virtuella datorer som ska migreras till Azure VMware-lösningen i ett utökat nätverk i Layer-2. Välj sedan **Fortsätt** .

   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="Skärm bild av surfning till en OVF-mall." lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. Granska anslutnings reglerna och välj **Fortsätt** .  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="Skärm bild av surfning till en OVF-mall." lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. Klicka på **Slutför** för att skapa beräknings profilen.


   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="Skärm bild av surfning till en OVF-mall." lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

En översikt över slut punkt till slut punkt av den här proceduren finns i [Azure VMware-lösningen: Compute Profile](https://www.youtube.com/embed/qASXi5xrFzM) video.

### <a name="create-a-service-mesh"></a>Skapa ett service nät

Nu är det dags att konfigurera ett service nät mellan lokala och Azure VMware-SDDC.

   > [!NOTE]
   > Så här upprättar du ett service nät med Azure VMware-lösningen:
   >
   > Portarna UDP 500/4500 är öppna mellan nätverks profil adresserna för den lokala HCX-anslutningen och det HCX molnets nätverks profil adresser i Azure VMware-lösningen.
   >
   > Se till att granska [HCX-portar som krävs](https://ports.vmware.com/home/VMware-HCX).

1. Under **infrastruktur** väljer du **Interconnect**  >  **service-nät**  >  **skapa tjänst nät** .    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Skärm bild av surfning till en OVF-mall." lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Granska de platser som är ifyllda och välj sedan **Fortsätt** . 

   >[!NOTE]
   >Om det här är din första tjänst nät konfiguration behöver du inte ändra den här skärmen.  

1. Välj käll-och fjärrberäknings profiler i list rutorna och välj sedan **Fortsätt** .  

   Valen definierar de resurser där de virtuella datorerna kan använda VMware HCX-tjänster.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="Skärm bild av surfning till en OVF-mall." lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="Skärm bild av surfning till en OVF-mall." lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Granska tjänster som kommer att aktive ras och välj sedan **Fortsätt** .  

1. I **Avancerad konfiguration – Åsidosätt överordnad överordnad nätverks profil** , väljer du **Fortsätt** .  

   Nätverks profiler för överordnad länk ansluter till nätverket genom vilket fjärrplatsens sammanlänknings utrustning kan nås.  
  
1. I **Avancerad konfiguration – nätverks tilläggets utrustning skala ut** , granska och välj **Fortsätt** . 

1. I **Avancerad konfiguration – trafik teknik** , granskar och gör du nödvändiga ändringar och väljer sedan **Fortsätt** .

1. Granska förhands granskning av topologi och välj **Fortsätt** .

1. Ange ett användarvänligt namn för det här Service nätet och välj **Slutför** för att slutföra.  

1. Välj **Visa aktiviteter** för att övervaka distributionen. 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="Skärm bild av surfning till en OVF-mall.":::

   När service nät distributionen har slutförts visas tjänsterna som gröna.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="Skärm bild av surfning till en OVF-mall." lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. Kontrol lera tjänstens nät hälsa genom att kontrol lera status för enheten. 
1. Välj **Interconnect** -  >  **enheter** .

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="Skärm bild av surfning till en OVF-mall." lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

En översikt över slut punkt till slut punkt för den här proceduren finns i [Azure VMware-lösningen: service nät-](https://www.youtube.com/embed/FyZ0d3P_T24) video.



### <a name="optional-create-a-network-extension"></a>Valfritt Skapa ett nätverks tillägg

Följ dessa steg om du vill utöka alla nätverk från din lokala miljö till Azure VMware-lösningen:

1. Under **tjänster** väljer du **nätverks tillägg** och väljer sedan **skapa ett nätverks tillägg** .

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="Skärm bild av surfning till en OVF-mall." lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Välj vart och ett av de nätverk som du vill utöka till Azure VMware-lösningen och välj sedan **Nästa** .

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="Skärm bild av surfning till en OVF-mall.":::

1. Ange den lokala gateway-IP: en för vart och ett av de nätverk som du vill utöka och välj sedan **Skicka** . 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="Skärm bild av surfning till en OVF-mall.":::

   Det tar några minuter för nätverks tillägget att slutföras. När du gör det visas status ändringen till **tillägget slutförd** .

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="Skärm bild av surfning till en OVF-mall." lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

En översikt över slut punkt till slut punkt av det här steget finns i [Azure VMware-lösningen: Network Extension](https://www.youtube.com/embed/cNlp0f_tTr0) video.


## <a name="next-steps"></a>Nästa steg

Om du har nått den här punkten och statusen för apparatens Interconnect-tunnel är **upp** och grön, kan du migrera och skydda virtuella datorer i Azure VMware-lösningen med VMware HCX. Azure VMware-lösningen stöder migrering av arbets belastning (med eller utan ett nätverks tillägg). Du kan fortfarande migrera arbets belastningar i din vSphere-miljö, tillsammans med lokala skapande av nätverk och distribution av virtuella datorer till dessa nätverk.  

Mer information om hur du använder HCX finns i den tekniska dokumentationen för VMware:

* [VMware HCX-dokumentation](https://docs.vmware.com/en/VMware-HCX/index.html)
* [Migrera Virtual Machines med VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g).
* [HCX nödvändiga portar](https://ports.vmware.com/home/VMware-HCX)
