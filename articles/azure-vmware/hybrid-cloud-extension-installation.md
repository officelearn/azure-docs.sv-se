---
title: Installera Hybrid Cloud Extension (HCX)
description: Konfigurera VMWare Hybrid Cloud Extension (HCX)-lösningen för Azure VMWare-lösningen (AVS) privat moln
ms.topic: how-to
ms.date: 05/19/2020
ms.openlocfilehash: 74bc0dece2fc565aebc6e5301ef7a36187d6a250
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83776477"
---
# <a name="install-hcx-for-azure-vmware-solution"></a>Installera HCX för Azure VMWare-lösning

I den här artikeln går vi igenom procedurerna för att ställa in VMWare Hybrid Cloud Extension (HCX)-lösningen för ditt moln i Azure VMWare-lösningen (AVS). HCX Advanced (standard installation) stöder upp till tre externa platser, där varje extern plats kräver att en HCX Enterprise Manager eller Connector har installerats och Aktiver ATS.
HCX möjliggör migrering av VMware-arbetsbelastningar till molnet och andra anslutna platser via olika inbyggda HCX-typer som stöds. Om fler än tre platser krävs kan kunder välja att aktivera HCX Enterprise-tillägget via support. HCX Enterprise ger ytterligare avgifter för kunder efter allmän tillgänglighet (GA) men innehåller [ytterligare funktioner](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/).

Noggrant granska [innan du börjar](#before-you-begin), [program versions krav](#software-version-requirements)och [förutsättningar](#prerequisites) först. 

Sedan går vi igenom alla nödvändiga procedurer för att:

> [!div class="checklist"]
> * Distribuera de lokala HCX-EMBRYOna
> * Aktivera och konfigurera HCX
> * Konfigurera nätverks överordnad länk och service nät
> * Slutför installationen genom att kontrol lera status för enheten

När du har slutfört installationen tillhandahålls rekommenderade nästa steg.

## <a name="before-you-begin"></a>Innan du börjar
    
* Gå igenom [självstudien](tutorial-network-checklist.md) för den grundläggande SDDC-serien (definition Software Defined Data Center)
* Granska och referera till [VMware HCX-dokumentationen](https://docs.vmware.com/en/VMware-HCX/index.html) inklusive användar handboken för HCX
* Granska VMware-dokument [migrera Virtual Machines med VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* Om du vill kan du granska [överväganden vid distribution av VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)
* Du kan också granska relaterade VMware-material på HCX, t. ex. VMware vSphere [blogg Serien](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) på HCX. 
* Beställ en AVS-HCX Enterprise-aktivering via support kanaler för AVS.

Att ändra storlek på arbets belastningar mot beräknings-och lagrings resurser är ett viktigt planerings steg när du förbereder att använda HCX-lösningen i AVS-moln. Detta storleks steg ska åtgärdas som en del av den ursprungliga planeringen av molnet i den ursprungliga miljön. 

## <a name="software-version-requirements"></a>Program versions krav
Infrastruktur komponenter måste köra den lägsta version som krävs. 
                                                         
| Komponent typ                                                          | Käll miljö krav                                                                   | Mål miljö krav                                                                      |
| --- | --- | --- |
| vCenter Server                                                          | 5.1<br/><br/>Om du använder 5,5 U1 eller tidigare använder du det fristående HCX-användargränssnittet för HCX-åtgärder.         | 6,0 U2 och senare                                                                                          |
| ESXi                                                                    | 5.0                                                                                               | ESXi 6,0 och senare                                                                                        |
| NSX                                                                     | För HCX nätverks tillägg för logiska växlar vid källan: NSXv 6.2 + eller NSX-T 2.4 +              | NSXv 6.2 + eller NSX-T 2.4 +<br/><HCX närhets dirigering: NSXv 6.4 + (närhet routning stöds inte med NSX-T) |
| vCloud-regissör                                                         | Krävs inte – ingen interoperabilitet med vCloud Director på käll platsen | När mål miljön är integrerad med vCloud Director är den minsta 9.1.0.2.              |

## <a name="prerequisites"></a>Krav

* Global räckvidd bör konfigureras mellan lokala och AVS-SDDC ER-kretsar.

* Alla portar som krävs måste vara öppna mellan lokala och AVS-SDDC (se [VMware HCX-dokumentation](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)).

* En IP-adress för HCX Manager lokalt och minst två IP-adresser för Interconnect (IX) och nätverks tillägg (NE).

* Lokala HCX IX-och NE-enheter ska kunna uppnå vCenter-och ESXi-infrastruktur.

* För att distribuera WAN Interconnect-installationen, förutom det/22 CIDR-SDDC som används för distribution av i Azure Portal, kräver HCX ett/29-block. Du bör väga detta i din nätverks planering.

## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>Distribuera VMware HCX-ägg lokalt

1. Logga in på AVS SDDC vCenter och välj **HCX**.

    ![Välj HCX i till AVS vCenter](./media/hybrid-cloud-extension-installation/avs-vsphere-client.png)

1. Om du vill ladda ned VMware HCX-filer för VMware väljer du **Administration**  >  **system updates**.

    ![Hämta system uppdateringar](./media/hybrid-cloud-extension-installation/administration-updates.png)

1. Välj en OVF-mall för att distribuera till lokal vCenter.  

    ![Välj mall för OVF](./media/hybrid-cloud-extension-installation/select-template.png)

1. Välj ett namn och en plats och välj sedan en resurs/ett kluster där HCX måste distribueras. Granska sedan information och nödvändiga resurser.  

    ![Granska mal linne Detaljer](./media/hybrid-cloud-extension-installation/configure-template.png)

1. Läs igenom licens villkoren och välj nödvändigt lagring och nätverk om du godkänner. Välj **Nästa**.

1. I **Anpassa mall**anger du all information som krävs. 

    ![Anpassa mall](./media/hybrid-cloud-extension-installation/customize-template.png)  

1. Välj **Nästa**, verifiera konfigurationen och välj **Slutför** för att distribuera HCX-ägg.

## <a name="activate-hcx"></a>Aktivera HCX

Efter installationen utför du följande steg.

1. Öppna HCX Manager på `https://HCXManagerIP:9443` och logga in med ditt användar namn och ditt lösen ord. 

1. Ange din **HCX Advanced-nyckel**i **licensiering**.  

    ![Ange HCX-nyckel](./media/hybrid-cloud-extension-installation/hcx-key.png)  
    
    > [!NOTE]
    > HCX Manager måste ha öppen Internet åtkomst eller en proxy konfigurerad.

1. Konfigurera vCenter.

    ![Konfigurera VCenter](./media/hybrid-cloud-extension-installation/configure-vcenter.png)

1. På **plats för data Center**, om det behövs, redigerar du data Center platsen.

    ![Plats för databas](./media/hybrid-cloud-extension-installation/system-location.png)

## <a name="configure-hcx"></a>Konfigurera HCX 

1. Logga in på lokal vCenter och välj sedan **hem**-  >  **HCX**.

    ![HCX i VCenter](./media/hybrid-cloud-extension-installation/hcx-vcenter.png)

1. Välj länkning av **infrastruktur**  >  **plats**  >  **Lägg till en webbplats ihopparning**.

    ![Lägg till webbplats koppling](./media/hybrid-cloud-extension-installation/site-pairing.png)

1. Ange **URL**, **användar namn**och **lösen ord**för fjärr-HCX. Välj **Anslut**.

   Systemet visar den anslutna platsen.
   
    ![Plats anslutning](./media/hybrid-cloud-extension-installation/site-connection.png)

1. Välj **Interconnect**  >  nätverks profiler för**multipl-site service nät**  >  **Network Profiles**  >  **skapa nätverks profil**.

    ![Skapa nätverks profil](./media/hybrid-cloud-extension-installation/create-network-profile.png)

1. Ange HCX IX och NE IP-adressintervall (minst 2 IP-adresser krävs för IX-och NE-enheter).
    
   ![Ange IP-adressintervall](./media/hybrid-cloud-extension-installation/enter-address-ranges.png)
  
   > [!NOTE]
   > Nätverks tilläggs enheten (HCX-NE) har en 1-till-1-relation med en distribuerad virtuell växel (DVS).  

1. Välj **beräknings profil**  >  **skapa beräknings profil**.

1. Ange ett namn på beräknings profilen och välj **Fortsätt**.  

    ![Skapa beräknings profil](./media/hybrid-cloud-extension-installation/create-compute-profile.png)

1. Välj tjänster som ska aktive ras, t. ex. migrering, nätverks tillägg, PR haveri beredskap. Välj **Fortsätt**.

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
      
    Välj en eller flera nätverks profiler, så att något av följande stämmer:  
    * Interconnect-apparater på fjärrplatsen kan nås via det här nätverket  
    * Fjärranslutna enheter kan komma åt lokala Interconnect-enheter via det här nätverket.  
    
    Om du har punkt-till-punkt-nätverk som direktansluten anslutning som inte delas mellan flera platser, kan du hoppa över det här steget eftersom beräknings profilerna delas med flera platser. I sådana fall kan nätverks profiler för överordnad länk åsidosättas och anges när Interconnect-tjänstens nät skapas.  
    
    ![Välj Nätverks profil för överordnad länk](./media/hybrid-cloud-extension-installation/uplink-network-profile.png)

1. Välj **vMotion nätverks profil** och välj **Fortsätt**.
      
    Välj den nätverks profil som vMotion-gränssnittet för ESXi-värdarna kan nås via. Om du inte redan har definierat en sådan nätverks profil kan du skapa den här. Om du inte har vMotion Network väljer du **hanterings nätverks profil**.  
    
    ![Välj Nätverks profil för vMotion](./media/hybrid-cloud-extension-installation/vmotion-network-profile.png)

1. Välj **nätverks profil för vSphere-replikering** och välj **Fortsätt**.
      
    Välj en nätverks profil som vSphere Replication-gränssnittet för ESXi-värdar kan nås via. I de flesta fall är den här profilen densamma som nätverks profilen för hantering.  
    
    ![Välj Nätverks profil för vSphere-replikering](./media/hybrid-cloud-extension-installation/vsphere-replication-network-profile.png)

1. Välj **distribuerade växlar för nätverks tillägg** och välj **Fortsätt**.  
      
    Välj de distribuerade virtuella växlar där du har nätverk som den Virtual Machines som ska migreras är ansluten till.

    ![Välj distribuerade virtuella växlar](./media/hybrid-cloud-extension-installation/distributed-switches.png)

1. Granska anslutnings regler och välj **Fortsätt**. Klicka på **Slutför** för att skapa beräknings profilen.  

    ![Skapa beräknings profil](./media/hybrid-cloud-extension-installation/complete-compute-profile.png)

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

1. Välj **infrastruktur**  >  **Interconnect**  >  **service nät**  >  **skapa tjänst nät**.  Konfigurera nätverks-och beräknings profilerna som skapas i föregående steg.    
      
    ![Konfigurera service nät](./media/hybrid-cloud-extension-installation/configure-service-mesh.png)

1. Välj **Skapa service nät** och välj **Fortsätt**.  
      
    Välj kopplade platser mellan vilka du vill aktivera hybrid mobilitet.  
    
    ![Välj kopplade platser](./media/hybrid-cloud-extension-installation/select-paired-sites.png)

1. Välj **Compute-profil** och välj **Fortsätt**.
      
    Välj en beräknings profil var och en på käll-och fjärranslutna platser för att aktivera hybrid tjänster. Valen definierar resurserna där Virtual Machines kan använda HCX-tjänster.  
      
    ![Aktivera hybrid tjänster](./media/hybrid-cloud-extension-installation/enable-hybridity.png)

1. Välj de tjänster som ska aktive ras för HCX och välj **Fortsätt**.  
      
    ![Välj HCX Services](./media/hybrid-cloud-extension-installation/hcx-services.png)

1. I **Avancerad konfiguration – Åsidosätt överordnad överordnad nätverks profil** Välj **Fortsätt**.  
      
    Nätverks profiler för överordnad länk används för att ansluta till nätverket via vilket fjärrplatsens sammanlänknings utrustning kan nås.  
      
    ![Åsidosätt profiler för överordnad länk](./media/hybrid-cloud-extension-installation/override-uplink-profiles.png)

1. I **Avancerad konfiguration – nätverks tilläggets utrustning utskalning**, väljer **du konfigurera nätverks tilläggs enheten skala ut**. 
      
    ![Nätverks tillägget skalas ut](./media/hybrid-cloud-extension-installation/network-extension-scale-out.png)

1. Ange det antal installationer som motsvarar antalet DVS-switchar.  
      
    ![Konfigurera antal installationer](./media/hybrid-cloud-extension-installation/appliance-scale.png)

1. I **Avancerad konfiguration – trafik teknik**väljer du **Fortsätt**.  
      
    ![Konfigurera trafik teknik](./media/hybrid-cloud-extension-installation/traffic-engineering.png)

1. Granska förhands granskning av topologi och välj **Fortsätt**. Ange ett användarvänligt namn för det här tjänst nätet och välj **Slutför** .  
      
    ![Fullständigt service nät](./media/hybrid-cloud-extension-installation/complete-service-mesh.png)

    Service nätet har distribuerats och kon figurer ATS.  
      
    ![Distribuerat tjänst nät](./media/hybrid-cloud-extension-installation/deployed-service-mesh.png)

## <a name="check-appliance-status"></a>Kontrol lera status för enheten
Välj **Interconnect**-enheter om du vill kontrol lera status för enheten  >  **Appliances**. 
      
![Status för apparat](./media/hybrid-cloud-extension-installation/appliance-status.png)

## <a name="next-steps"></a>Nästa steg

När status för apparatens Interconnect-tunnel är **upp** och grön är du redo att migrera och skydda virtuella datorer i AVS- **läge** med HCX. Se [VMware HCX-dokumentationen](https://docs.vmware.com/en/VMware-HCX/index.html) och [migrera Virtual Machines med VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g) i den tekniska dokumentationen för VMware.
