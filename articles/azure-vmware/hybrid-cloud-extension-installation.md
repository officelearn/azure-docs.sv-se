---
title: Installera Hybrid Cloud Extension (HCX)
description: Konfigurera VMware Hybrid Cloud Extension (HCX)-lösningen för Azure VMware-lösningen (AVS) privat moln
ms.topic: how-to
ms.date: 05/19/2020
ms.openlocfilehash: 3037d12ebbb036098cfc00a42521513bc2df6170
ms.sourcegitcommit: bf8c447dada2b4c8af017ba7ca8bfd80f943d508
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2020
ms.locfileid: "85367554"
---
# <a name="install-hcx-for-azure-vmware-solution"></a>Installera HCX för Azure VMware-lösning

I den här artikeln går vi igenom procedurerna för att ställa in VMWare Hybrid Cloud Extension (HCX)-lösningen för ditt moln i Azure VMWare-lösningen (AVS). HCX möjliggör migrering av VMware-arbetsbelastningar till molnet och andra anslutna platser via olika inbyggda HCX-typer som stöds.

HCX Advanced, standard-installationen stöder upp till tre externa platser. Om fler än tre platser krävs kan kunder välja att aktivera HCX Enterprise-tillägget via support. HCX Enterprise-installation medför ytterligare kostnader för kunder efter allmän tillgänglighet (GA), men innehåller [ytterligare funktioner](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/).


Noggrant granska [innan du börjar](#before-you-begin), [program versions krav](#software-version-requirements)och [förutsättningar](#prerequisites) först. 

Sedan går vi igenom alla nödvändiga procedurer för att:

> [!div class="checklist"]
> * Distribuera de lokala HCX-EMBRYOna
> * Aktivera och konfigurera HCX
> * Konfigurera nätverks överordnad länk och service nät
> * Slutför installationen genom att kontrol lera status för enheten

När du har slutfört installationen kan du följa de rekommenderade nästa stegen som anges i slutet av den här artikeln.  

## <a name="before-you-begin"></a>Innan du börjar
    
* Gå igenom [självstudien](tutorial-network-checklist.md) för den grundläggande SDDC-serien (definition Software Defined Data Center)
* Granska och referera till [VMware HCX-dokumentationen](https://docs.vmware.com/en/VMware-HCX/index.html) inklusive användar handboken för HCX
* Granska VMware-dokument [migrera Virtual Machines med VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* Om du vill kan du granska [överväganden vid distribution av VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)
* Du kan också granska relaterade VMware-material på HCX, t. ex. VMware vSphere [blogg Serien](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) på HCX. 
* Beställ en AVS-HCX Enterprise-aktivering via support kanaler för AVS.

Att ändra storlek på arbets belastningar mot beräknings-och lagrings resurser är ett viktigt planerings steg när du förbereder att använda HCX-lösningen i AVS-moln. Adressera storleks steget som en del av den ursprungliga planeringen av molnet i den ursprungliga miljön.   

## <a name="software-version-requirements"></a>Program versions krav
Infrastruktur komponenter måste köra den lägsta version som krävs. 
                                                         
| Komponent typ    | Käll miljö krav    | Mål miljö krav   |
| --- | --- | --- |
| vCenter Server   | 5.1<br/><br/>Om du använder 5,5 U1 eller tidigare använder du det fristående HCX-användargränssnittet för HCX-åtgärder.  | 6,0 U2 och senare   |
| ESXi   | 5.0    | ESXi 6,0 och senare   |
| NSX    | För HCX nätverks tillägg för logiska växlar vid källan: NSXv 6.2 + eller NSX-T 2.4 +   | NSXv 6.2 + eller NSX-T 2.4 +<br/><br/>För HCX närhet: NSXv 6.4 + (närhets dirigering stöds inte med NSX-T) |
| vCloud-regissör   | Krävs inte – ingen interoperabilitet med vCloud Director på käll platsen | När du integrerar mål miljön med vCloud Director är minimivärdet 9.1.0.2.  |

## <a name="prerequisites"></a>Krav

* Global räckvidd bör konfigureras mellan lokala och AVS-SDDC ER-kretsar.

* Alla portar som krävs måste vara öppna mellan lokala och AVS-SDDC (se [VMware HCX-dokumentation](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)).

* En IP-adress för HCX Manager lokalt och minst två IP-adresser för Interconnect (IX) och nätverks tillägg (NE).

* Lokala HCX IX-och NE-enheter ska kunna uppnå vCenter-och ESXi-infrastruktur.

* För att distribuera WAN Interconnect-installationen, förutom det/22 CIDR-SDDC som används för distribution av i Azure Portal, kräver HCX ett/29-block. Se till att planera detta krav i din nätverks planering.

## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>Distribuera VMware HCX-ägg lokalt

1. Logga in på AVS SDDC vCenter och välj **HCX**.

    ![Välj HCX i till AVS vCenter](./media/hybrid-cloud-extension-installation/avs-vsphere-client.png)

1. Under **Administration**väljer du **System uppdateringar** och väljer sedan **begär nedladdnings länk** för att ladda ned VMware HCX-filer.

    ![Hämta system uppdateringar](./media/hybrid-cloud-extension-installation/administration-updates.png)

1. Gå sedan till den lokala vCenter och välj en OVF-mall för att distribuera till din lokala vCenter.  

    ![Välj mall för OVF](./media/hybrid-cloud-extension-installation/select-template.png)

1. Välj ett namn och en plats och välj sedan en resurs/ett kluster där HCX måste distribueras. Granska sedan information och nödvändiga resurser.  

    ![Granska mal linne Detaljer](./media/hybrid-cloud-extension-installation/configure-template.png)

1. Läs igenom licens villkoren och välj nödvändigt lagring och nätverk om du godkänner. Välj **Nästa**.

1. I **Anpassa mall**anger du all information som krävs. 

    ![Anpassa mall](./media/hybrid-cloud-extension-installation/customize-template.png)  

1. Välj **Nästa**, verifiera konfigurationen och välj **Slutför** för att distribuera HCX-ägg.

## <a name="activate-hcx"></a>Aktivera HCX

Efter installationen utför du följande steg.

1. Logga in på den lokala HCX Manager på `https://HCXManagerIP:9443` och logga in med ditt användar namn och ditt lösen ord. 

   > [!IMPORTANT]
   > Se till att ta `9443` med port numret med IP-adressen för HCX Manager.

1. Ange din **HCX Advanced-nyckel**i **licensiering**.  

    ![Ange HCX-nyckel](./media/hybrid-cloud-extension-installation/hcx-key.png)  
    
    > [!NOTE]
    > HCX Manager måste ha öppen Internet åtkomst eller en proxy konfigurerad.

1. I **vCenter**, om det behövs, redigerar du vCenter-informationen.

    ![Konfigurera VCenter](./media/hybrid-cloud-extension-installation/configure-vcenter.png)

1. På **plats för data Center**, om det behövs, redigerar du data Center platsen.

    ![Plats för databas](./media/hybrid-cloud-extension-installation/system-location.png)

## <a name="configure-hcx"></a>Konfigurera HCX 

1. Logga in på lokal vCenter och välj **HCX**under **Start**.

    ![HCX i VCenter](./media/hybrid-cloud-extension-installation/hcx-vcenter.png)

1. Under **infrastruktur**väljer du **plats länkning**  >  **Lägg till en webbplats ihopparning**.

    ![Lägg till webbplats koppling](./media/hybrid-cloud-extension-installation/site-pairing.png)

1. Ange den fjärranslutna HCX URL eller IP-adress, AVS cloudadmin användar namn och lösen ord och välj sedan **Anslut**.

   Systemet visar den anslutna platsen.
   
    ![Plats anslutning](./media/hybrid-cloud-extension-installation/site-connection.png)

1. Under **infrastruktur**väljer du **Interconnect**  >  nätverks profiler för**multipl-site service nät**  >  **Network Profiles**  >  **skapa nätverks profil**.

    ![Skapa nätverks profil](./media/hybrid-cloud-extension-installation/create-network-profile.png)

1. För den nya nätverks profilen anger du HCX IX och NE IP-adressintervall (minst två IP-adresser krävs för IX-och NE-enheter).
    
   ![Ange IP-adressintervall](./media/hybrid-cloud-extension-installation/enter-address-ranges.png)
  
   > [!NOTE]
   > Nätverks tilläggs enheten (HCX-NE) har en 1-till-1-relation med en distribuerad virtuell växel (DVS).  

1. Välj **beräknings profil**  >  **skapa beräknings profil**.

1. Ange ett namn på beräknings profilen och välj **Fortsätt**.  

    ![Skapa beräknings profil](./media/hybrid-cloud-extension-installation/create-compute-profile.png)

1. Välj de tjänster som ska aktive ras, till exempel migrering, nätverks tillägg eller haveri beredskap, och välj sedan **Fortsätt**.

    ![Välj tjänster](./media/hybrid-cloud-extension-installation/select-services.png)

1. I **Välj tjänst resurser**väljer du en eller flera tjänst resurser som de valda HCX-tjänsterna ska aktive ras för. Välj **Fortsätt**.
    
   ![Välj tjänst resurser](./media/hybrid-cloud-extension-installation/select-service-resources.png)
  
   > [!NOTE]
   > Välj specifika kluster där de virtuella käll datorerna är avsedda för migrering med HCX.

1. Välj **data lager** och välj **Fortsätt**. 
      
    Välj varje beräknings-och lagrings resurs för att distribuera HCX-Interconnect-utrustningarna. När flera resurser har marker ATS använder HCX den första resursen som valdes tills dess kapacitet är förbrukad.  
    
    ![Välj distributions resurser](./media/hybrid-cloud-extension-installation/deployment-resources.png)

1. Välj den hanterings nätverks profil som skapades i **nätverks profiler** och välj **Fortsätt**.  
      
    Välj den nätverks profil som hanterings gränssnittet för vCenter och ESXi-värdarna kan nås via. Om du inte redan har definierat en sådan nätverks profil kan du skapa den här.  
    
    ![Välj hanterings nätverks profil](./media/hybrid-cloud-extension-installation/management-network-profile.png)

1. Välj **nätverks överordnad länk** och välj **Fortsätt**.
      
    Välj en eller flera nätverks profiler så att något av följande stämmer:  
    * Interconnect-apparater på fjärrplatsen kan nås via det här nätverket  
    * Fjärranslutna enheter kan komma åt lokala Interconnect-enheter via det här nätverket.  
    
    Om du har punkt-till-punkt-nätverk som direktansluten anslutning som inte delas mellan flera platser, kan du hoppa över det här steget eftersom beräknings profilerna delas med flera platser. I sådana fall kan nätverks profiler för överordnad länk åsidosättas och anges när Interconnect-tjänstens nät skapas.  
    
    ![Välj Nätverks profil för överordnad länk](./media/hybrid-cloud-extension-installation/uplink-network-profile.png)

1. Välj **vMotion nätverks profil** och välj **Fortsätt**.
      
   Välj den nätverks profil som vMotion-gränssnittet för ESXi-värdarna kan nås via. Om du inte redan har definierat en sådan nätverks profil kan du skapa den här. Om du inte har vMotion Network väljer du **hanterings nätverks profil**.  
    
   ![Välj Nätverks profil för vMotion](./media/hybrid-cloud-extension-installation/vmotion-network-profile.png)

1. Välj en nätverks profil i **nätverks profilen för vSphere-replikering**och välj sedan **Fortsätt**.
      
   I de flesta fall är den här profilen densamma som nätverks profilen för hantering.  
    
   ![Välj Nätverks profil för vSphere-replikering](./media/hybrid-cloud-extension-installation/vsphere-replication-network-profile.png)

1. Från **Välj distribuerade växlar för nätverks tillägg**väljer du den dvs där du har nätverk som de virtuella datorerna ska integreras och är anslutna.  Välj **Fortsätt**.  
      
    ![Välj distribuerade virtuella växlar](./media/hybrid-cloud-extension-installation/distributed-switches.png)

1. Granska anslutnings reglerna och välj **Fortsätt**.  

    ![Skapa beräknings profil](./media/hybrid-cloud-extension-installation/complete-compute-profile.png)

1.  Klicka på **Slutför** för att skapa beräknings profilen.

## <a name="configure-network-uplink"></a>Konfigurera nätverks överordnad länk

Nu ska du konfigurera nätverks profil ändringen i AVS-SDDC för nätverks överordnad länk.

1. Logga in på SDDC NSX-T för att skapa en ny logisk växel eller Använd en befintlig logisk växel som kan användas för överordnad nätverks överordnad mellan lokala och AVS-SDDC.

1. Skapa en nätverks profil för HCX-överordnad länk i AVS-SDDC som kan användas för lokal kommunikation med SDDC i AVS-.  
    
   ![Skapa nätverks profil för överordnad länk](./media/hybrid-cloud-extension-installation/network-profile-uplink.png)

1. Ange ett namn för nätverks profilen och minst 4-5 kostnads fria IP-adresser som är baserade på det L2-Nätverksanslutnings tillägg som krävs.  
    
   ![Konfigurera nätverks profil för överordnad länk](./media/hybrid-cloud-extension-installation/configure-uplink-profile.png)

1. Välj **skapa** för att slutföra konfigurationen av AVS-SDDC

## <a name="configure-service-mesh"></a>Konfigurera service nät

Konfigurera nu service nät mellan lokala och AVS-SDDC.

1. Logga in på AVS SDDC vCenter och välj **HCX**.

2. Under **infrastruktur**väljer du **Interconnect**  >  **service-nät**  >  **skapa tjänst nät** för att konfigurera nätverks-och beräknings profilerna som skapas i föregående steg.    
      
    ![Konfigurera service nät](./media/hybrid-cloud-extension-installation/configure-service-mesh.png)

3. Välj kopplade webbplatser för att aktivera hybrid möjligheter och välj **Fortsätt**.   
    
    ![Välj kopplade platser](./media/hybrid-cloud-extension-installation/select-paired-sites.png)

4. Välj källa och fjär beräknings profiler för att aktivera hybrid tjänster och välj **Fortsätt**.
      
    Valen definierar resurserna där de virtuella datorerna kan använda HCX-tjänster.  
      
    ![Aktivera hybrid tjänster](./media/hybrid-cloud-extension-installation/enable-hybridity.png)

5. Välj tjänster som ska aktive ras och välj **Fortsätt**.  
      
    ![Välj HCX Services](./media/hybrid-cloud-extension-installation/hcx-services.png)

6. I **Avancerad konfiguration – Åsidosätt överordnad överordnad nätverks profil** Välj **Fortsätt**.  
      
    Nätverks profiler för överordnad länk används för att ansluta till nätverket via vilket fjärrplatsens sammanlänknings utrustning kan nås.  
      
    ![Åsidosätt profiler för överordnad länk](./media/hybrid-cloud-extension-installation/override-uplink-profiles.png)

7. Välj **Konfigurera nätverks tilläggs enheten skala ut**. 
      
    ![Nätverks tillägget skalas ut](./media/hybrid-cloud-extension-installation/network-extension-scale-out.png)

8. Ange det antal installationer som motsvarar antalet DVS-switchar.  
      
    ![Konfigurera antal installationer](./media/hybrid-cloud-extension-installation/appliance-scale.png)

9. Välj **Fortsätt** för att hoppa över.  
      
    ![Konfigurera trafik teknik](./media/hybrid-cloud-extension-installation/traffic-engineering.png)

10. Granska förhands granskning av topologi och välj **Fortsätt**. 

11. Ange ett användarvänligt namn för det här Service nätet och välj **Slutför** för att slutföra.  
      
    ![Fullständigt service nät](./media/hybrid-cloud-extension-installation/complete-service-mesh.png)

    Service nätet har distribuerats och kon figurer ATS.  
      
    ![Distribuerat tjänst nät](./media/hybrid-cloud-extension-installation/deployed-service-mesh.png)

## <a name="check-appliance-status"></a>Kontrol lera status för enheten
Välj **Interconnect**-enheter om du vill kontrol lera status för enheten  >  **Appliances**. 
      
![Status för apparat](./media/hybrid-cloud-extension-installation/appliance-status.png)

## <a name="next-steps"></a>Nästa steg

När status för apparatens Interconnect-tunnel är **upp** och grön är du redo att migrera och skydda virtuella datorer i AVS- **läge** med HCX. Se [VMware HCX-dokumentationen](https://docs.vmware.com/en/VMware-HCX/index.html) och [migrera Virtual Machines med VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) i den tekniska dokumentationen för VMware.
