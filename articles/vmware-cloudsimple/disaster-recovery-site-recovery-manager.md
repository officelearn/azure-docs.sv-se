---
title: Azure VMware Solution by CloudSimple – Konfigurera Private Cloud som en plats för haveriberedskap med hjälp av VMware Site Recovery Manager
description: Beskriver hur du konfigurerar ditt CloudSimple Private Cloud som en plats för haveriberedskap för lokala VMware-arbetsbelastningar
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fa8b2da683d68a337df38e13726f22c5af43540a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565935"
---
# <a name="set-up-private-cloud-as-a-disaster-recovery-target-with-vmware-site-recovery-manager"></a>Konfigurera Private Cloud som ett katastrofåterställningsmål med VMware Site Recovery Manager

Du kan använda ditt CloudSimple Private Cloud som en DR-plats (Disaster Recovery) för lokala VMware-arbetsbelastningar.

DR-lösningen är baserad på vSphere Replication och VMware Site Recovery Manager (SRM). En liknande metod kan följas för att aktivera ditt privata moln som en primär plats som skyddas av din lokala återställningsplats.

CloudSimple-lösningen:

* Eliminerar behovet av att konfigurera ett datacenter specifikt för DR.
* Gör att du kan utnyttja Azure-platser där CloudSimple distribueras för global geografisk återhämtning.
* Ger dig möjlighet att minska distributionskostnaderna och den totala ägandekostnaden för att upprätta DR.

CloudSimple-lösningen kräver att du gör följande:

* Installera, konfigurera och hantera vSphere Replication och SRM i ditt privata moln.
* Ange dina egna licenser för SRM när det privata molnet är den skyddade platsen. Du behöver inga ytterligare SRM-licenser för CloudSimple-webbplatsen när den används som återställningsplats.

Med den här lösningen har du full kontroll över vSphere-replikering och SRM. De välbekanta gränssnitten, API:et och CLI-gränssnitten gör det möjligt att använda dina befintliga skript och verktyg.

![Distribution av hanteraren för platsåterställning](media/srm-deployment.png)

Du kan använda alla versioner av vRA och SRM som är kompatibla med ditt privata moln och lokala miljöer. Exemplen i den här guiden använder vRA 6.5 och SRM 6.5. Dessa versioner är kompatibla med vSphere 6.5, som stöds av CloudSimple.

## <a name="deploy-the-solution"></a>Distribuera lösningen

I följande avsnitt beskrivs hur du distribuerar en DR-lösning med SRM i ditt privata moln.

1. [Kontrollera att VMware-produktversioner är kompatibla](#verify-that-vmware-product-versions-are-compatible)
2. [Uppskatta storleken på din DR-miljö](#estimate-the-size-of-your-dr-environment)
3. [Skapa ett privat moln för din miljö](#create-a-private-cloud-for-your-environment)
4. [Konfigurera privata molnnätverk för SRM-lösningen](#set-up-private-cloud-networking-for-the-srm-solution)
5. [Konfigurera en VPN-anslutning från plats till plats mellan det lokala nätverket och det privata molnet och öppna nödvändiga portar](#set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports)
6. [Konfigurera infrastrukturtjänster i ditt privata moln](#set-up-infrastructure-services-in-your-private-cloud)
7. [Installera vSphere Replication-apparat i din lokala miljö](#install-vsphere-replication-appliance-in-your-on-premises-environment)
8. [Installera vSphere Replication-apparat i din privata molnmiljö](#install-vsphere-replication-appliance-in-your-private-cloud-environment)
9. [Installera SRM-server i din lokala miljö](#install-srm-server-in-your-on-premises-environment)
10. [Installera SRM-server i ditt privata moln](#install-srm-server-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Kontrollera att VMware-produktversioner är kompatibla

Konfigurationerna i den här handboken omfattas av följande kompatibilitetskrav:

* Samma version av SRM måste distribueras i ditt privata moln och din lokala miljö.
* Samma version av vSphere Replication måste distribueras i ditt privata moln och din lokala miljö.
* Versionerna av PSC (Platform Services Controller) i ditt privata moln och din lokala miljö måste vara kompatibla.
* Versionerna av vCenter i ditt privata moln och din lokala miljö måste vara kompatibla.
* Versionerna av SRM- och vSphere-replikering måste vara kompatibla med varandra och med versionerna av PSC och vCenter.

Länkar till relevant VMware-dokumentation och kompatibilitetsinformation finns i dokumentationen till [VMware Site Recovery Manager.](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)

Om du vill ta reda på versionerna av vCenter och PSC i ditt privata moln öppnar du CloudSimple-portalen. Gå till **Resurser,** välj ditt privata moln och klicka på fliken **vSphere Management Network.**

![vCenter & PSC-versioner i Private Cloud](media/srm-resources.png)

### <a name="estimate-the-size-of-your-dr-environment"></a>Uppskatta storleken på din DR-miljö

1. Kontrollera att den identifierade lokala konfigurationen ligger inom gränser som stöds. För SRM 6.5 dokumenteras gränserna i VMwares kunskapsbasartikel om [driftsgränser för Site Recovery Manager 6.5](https://kb.vmware.com/s/article/2147110).
2. Se till att du har tillräcklig nätverksbandbredd för att uppfylla dina arbetsbelastningsstorleks- och RPO-krav. VMware-kunskapsbasartikeln om [beräkning av bandbreddskrav för vSphere Replication](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html) ger vägledning om bandbreddsbegränsningar.
3. Använd CloudSimple sizer-verktyget för att uppskatta de resurser som behövs på din DR-plats för att skydda din lokala miljö.

### <a name="create-a-private-cloud-for-your-environment"></a>Skapa ett privat moln för din miljö

Skapa ett privat moln från CloudSimple-portalen genom att följa instruktionerna och storleksrekommendationerna i [Skapa ett privat moln](create-private-cloud.md).

### <a name="set-up-private-cloud-networking-for-the-srm-solution"></a>Konfigurera privata molnnätverk för SRM-lösningen

Öppna CloudSimple-portalen för att konfigurera Privata molnnätverk för SRM-lösningen.

Skapa en VLAN för SRM-lösningsnätverket och tilldela det ett undernät CIDR. Instruktioner finns i [Skapa och hantera VLAN/undernät](create-vlan-subnet.md).

### <a name="set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports"></a>Konfigurera en VPN-anslutning från plats till plats mellan det lokala nätverket och det privata molnet och öppna nödvändiga portar

Så här konfigurerar du anslutning mellan lokalt nätverk och ditt privata moln. Instruktioner finns i [Konfigurera en VPN-anslutning till ditt CloudSimple Private Cloud](set-up-vpn.md).

### <a name="set-up-infrastructure-services-in-your-private-cloud"></a>Konfigurera infrastrukturtjänster i ditt privata moln

Konfigurera infrastrukturtjänster i det privata molnet så att det blir enkelt att hantera dina arbetsbelastningar och verktyg.

Du kan lägga till en extern identitetsprovider enligt beskrivningen i [Använd Azure AD som identitetsleverantör för vCenter på CloudSimple Private Cloud](azure-ad.md) om du vill göra något av följande:

* Identifiera användare från din lokala Active Directory (AD) i ditt privata moln.
* Konfigurera en AD i ditt privata moln för alla användare.
* Använd Azure AD.

Om du vill tillhandahålla IP-adresssökning, IP-adresshantering och namnmatchningstjänster för dina arbetsbelastningar i det privata molnet konfigurerar du en DHCP- och DNS-server enligt beskrivningen i [Konfigurera DNS- och DHCP-program och arbetsbelastningar i ditt CloudSimple Private Cloud](dns-dhcp-setup.md).

*.cloudsimple.io-domänen används av virtuella hanterings-datorer och värdar i ditt privata moln. Om du vill matcha begäranden till den här domänen konfigurerar du DNS-vidarebefordran på DNS-servern enligt beskrivningen i [Skapa en villkorlig vidarebefordrare](on-premises-dns-setup.md#create-a-conditional-forwarder).

### <a name="install-vsphere-replication-appliance-in-your-on-premises-environment"></a>Installera vSphere Replication-apparat i din lokala miljö

Installera vSphere Replication Appliance (vRA) i din lokala miljö genom att följa VMware-dokumentationen. Installationen består av följande steg på hög nivå:

1. Förbered din lokala miljö för vRA-installation.

2. Distribuera vRA i din lokala miljö med hjälp av OVF i VR ISO från vmware.com. För vRA 6.5 har [denna VMware blogg](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) relevant information.

3. Registrera din lokala vRA med vCenter Single Sign-On på den lokala webbplatsen. Detaljerade instruktioner för vSphere Replication 6.5 finns i VMware-dokumentet [VMware vSphere Replication 6.5 Installation och konfiguration](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

## <a name="install-vsphere-replication-appliance-in-your-private-cloud-environment"></a>Installera vSphere Replication-apparat i din privata molnmiljö

Innan du börjar kontrollerar du att du har följande:

* IP-nåbarhet från undernät i din lokala miljö till hanteringsundernätet i ditt privata moln
* IP-nåbarhet från replikeringsundernät i din lokala vSphere-miljö till SRM-lösningens undernät i ditt privata moln

Instruktioner finns i [Konfigurera en VPN-anslutning till ditt CloudSimple Private Cloud](set-up-vpn.md). Stegen liknar dem för den lokala installationen.

CloudSimple rekommenderar att du använder FQDN i stället för IP-adresser under vRA- och SRM-installationen. Om du vill ta reda på FQDN för vCenter och PSC i ditt privata moln öppnar du CloudSimple-portalen. Gå till **Resurser,** välj ditt privata moln och klicka på fliken **vSphere Management Network.**

![Hitta FQDN för vCenter/PSC i privat moln](media/srm-resources.png)

CloudSimple kräver att du inte installerar vRA och SRM med hjälp av standardanvändaren "cloudowner" utan i stället skapar en ny användare. Detta görs för att säkerställa hög drifttid och tillgänglighet för din private cloud vCenter-miljö. Standardanvändaren för molnägare i Private Cloud vCenter har dock inte tillräckliga privilegier för att skapa en ny användare med administratörsbehörighet.

Innan du installerar vRA och SRM måste du eskalera domänen vCenter för molnägaren och sedan skapa en användare med administratörsbehörighet i vCenter SSO-domänen. Mer information om standardanvändare och behörighetsmodell för Private Cloud finns [i Lär dig behörighetsmodellen För privat moln](learn-private-cloud-permissions.md).

Installationen består av följande steg på hög nivå:

1. [Eskalera privilegier](escalate-private-cloud-privileges.md).
2. Skapa en användare i ditt privata moln för vSphere Replication och SRM-installation. Förklaras nedan i [vCenter UI: Skapa en användare i Privat moln för vRA & SRM-installation](#vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation).
3. Förbered din privata molnmiljö för vRA-installation.
4. Distribuera vRA i ditt privata moln med OVF i VR ISO från vmware.com. För vRA 6,5, [har denna VMware blogg](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) relevant information.
5. Konfigurera brandväggsregler för vRA. Förklaras nedan i [CloudSimple portal: Konfigurera brandväggsregler för vRA](#cloudsimple-portal-configure-firewall-rules-for-vra).
6. Registrera Private Cloud vRA med vCenter Single Sign-On på webbplatsen Private Cloud.
7. Konfigurera vSphere Replication-anslutningar mellan de två apparaterna. Kontrollera att de nödvändiga portarna öppnas över brandväggarna. Se [den här VMware-kunskapsbasartikeln](https://kb.vmware.com/s/article/2087769) för en lista över portnummer som måste vara öppna för vSphere Replication 6.5.

Detaljerade installationsanvisningar för vSphere Replication 6.5 finns i VMware-dokumentet [VMware vSphere Replication 6.5 Installation och konfiguration](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation"></a>vCenter UI: Skapa en användare i Private Cloud för vRA- och SRM-installation

Logga in på vCenter med molnägareanvändarautentiseringsuppgifter efter eskalerande privilegier från CloudSimple-portalen.

Skapa en ny `srm-soln-admin`användare i vCenter och lägg till den i administratörsgruppen i vCenter.
Logga ut från vCenter som användare av molnägare och logga in som *srm-soln-admin-användare.*

#### <a name="cloudsimple-portal-configure-firewall-rules-for-vra"></a>CloudSimple portal: Konfigurera brandväggsregler för vRA

Konfigurera brandväggsregler enligt beskrivningen i [Konfigurera brandväggstabeller och regler](firewall.md) för att öppna portar för att aktivera kommunikation mellan:

* vRA i SRM-lösningsnätverket och vCenter- och ESXi-värdar i hanteringsnätverket.
* vRA-apparater på de två ställena.

Se den här [VMware-kunskapsbasartikeln](https://kb.vmware.com/s/article/2087769) för en lista över portnummer som måste vara öppna för vSphere Replication 6.5.

### <a name="install-srm-server-in-your-on-premises-environment"></a>Installera SRM-server i din lokala miljö

Innan du börjar kontrollerar du följande:

* vSphere Replication Appliance installeras i lokala och privata molnmiljöer.
* VSphere Replication-apparaterna på båda platserna är anslutna till varandra.
* Du har granskat VMware-informationen om förutsättningar och bästa praxis. För SRM 6.5 kan du läsa VMware-dokumentet [Förutsättningar och metodtips för SRM 6.5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html).

Följ VMware-dokumentationen för att utföra SRM-serverinstallation i distributionsmodellen "Tvåplatstopologi med en vCenter-instans per controller för plattformstjänster" enligt beskrivningen i det här [VMWare-dokumentet](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). Installationsinstruktionerna för SRM 6.5 finns i VMware-dokumentet [Installera Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

### <a name="install-srm-server-in-your-private-cloud"></a>Installera SRM-server i ditt privata moln

Innan du börjar kontrollerar du följande:

* vSphere Replication Appliance installeras i lokala och privata molnmiljöer.
* VSphere Replication-apparaterna på båda platserna är anslutna till varandra.
* Du har granskat VMware-informationen om förutsättningar och bästa praxis. För SRM 6.5 kan du läsa [förutsättningar och metodtips för platsåterställningshanteraren 6.5 Serverinstallation](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html).

Följande steg beskriver den privata cloud SRM-installationen.

1. [vCenter UI: Installera SRM](#vcenter-ui-install-srm)
2. [CloudSimple portal: Konfigurera brandväggsregler för SRM](#cloudsimple-portal-configure-firewall-rules-for-srm)
3. [vCenter UI: Konfigurera SRM](#vcenter-ui-configure-srm)
4. [CloudSimple-portal: de-eskalera privilegier](#cloudsimple-portal-de-escalate-privileges)

#### <a name="vcenter-ui-install-srm"></a>vCenter UI: Installera SRM

När du har loggat till vCenter med autentiseringsuppgifter för srm-soln-admin följer du VMware-dokumentationen för att utföra SRM-serverinstallation i distributionsmodellen "Tvåplatstopologi med en vCenter-instans per controller för plattformstjänster" enligt beskrivningen i det här [VMWare-dokumentet](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). Installationsinstruktionerna för SRM 6.5 finns i VMware-dokumentet [Installera Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

#### <a name="cloudsimple-portal-configure-firewall-rules-for-srm"></a>CloudSimple portal: Konfigurera brandväggsregler för SRM

Konfigurera brandväggsregler enligt beskrivningen i [Konfigurera brandväggstabeller och regler](firewall.md) för att tillåta kommunikation mellan:

SRM-servern och vCenter / PSC i det privata molnet.
SRM-servrarna på båda platserna

Se [den här VMware-kunskapsbasartikeln](https://kb.vmware.com/s/article/2087769) för en lista över portnummer som måste vara öppna för vSphere Replication 6.5.

#### <a name="vcenter-ui-configure-srm"></a>vCenter UI: Konfigurera SRM

När SRM har installerats i det privata molnet utför du följande uppgifter enligt beskrivningen i avsnitten i installations- och konfigurationsguiden för VMware Site Recovery Manager. För SRM 6.5 finns instruktionerna i VMware-dokumentet [Installera Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

1. Anslut serverinstanserna för platsåterställningshanteraren på skyddade platser och återställningsplatser.
2. Upprätta en klientanslutning till serverinstansen för Fjärrinstallationshanteraren.
3. Installera licensnyckeln för Site Recovery Manager.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>CloudSimple-portal: De-eskalera privilegier

Om du vill de-eskalera privilegier läser du [De-eskalera privilegier](escalate-private-cloud-privileges.md#de-escalate-privileges).

## <a name="ongoing-management-of-your-srm-solution"></a>Löpande hantering av din SRM-lösning

Du har full kontroll över vSphere Replication och SRM-programvara i din privata molnmiljö och förväntas utföra nödvändig livscykelhantering för programvara. Kontrollera att alla nya versioner av programvaran är kompatibla med Private Cloud vCenter och PSC innan du uppdaterar eller uppgraderar vSphere Replication eller SRM.

> [!NOTE]
> CloudSimple undersöker för närvarande alternativ för att erbjuda en hanterad DR-tjänst. 

## <a name="multiple-replication-configuration"></a>Konfiguration för flera replikeringar

 [Både matrisbaserad replikerings- och vSphere-replikeringsteknik kan användas tillsammans med SRM](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication) samtidigt. De måste dock tillämpas på separata uppsättning virtuella datorer (en viss virtuell dator kan skyddas antingen av matrisbaserad replikering eller vSphere-replikering, men inte båda). Dessutom kan CloudSimple-platsen konfigureras som en återställningsplats för flera skyddade platser. Se [SRM Multi-Site Alternativ](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite/) för information om flerplatskonfigurationer.

## <a name="references"></a>Referenser

* [Dokumentation för VMware Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
* [Driftsgränser för Site Recovery Manager 6.5](https://kb.vmware.com/s/article/2147110)
* [Beräkna bandbreddskrav för vSphere Replication](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)
* [OVF-alternativ vid distribution av vSphere Replication 6.5](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices/)
* [VMware vSphere Replication 6.5 Installation och konfiguration](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)
* [Förutsättningar och bästa praxis för SRM 6.5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
* [Site Recovery Manager i en topologi med två platser med en vCenter-serverinstans per controller för plattformstjänster](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)
* [Installations- och konfigurationsguide för VMware Site Recovery Manager 6.5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)
* [VMware Blogg på SRM med matris baserad replikering vs vSphere replikering](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)
* [VMware Blogg på SRM Multi-site alternativ](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite)
* [Portnummer som måste vara öppna för vSphere Replication 5.8.x, 6.x och 8](https://kb.vmware.com/s/article/2147112)
