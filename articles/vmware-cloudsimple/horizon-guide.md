---
title: Azure VMware Solution by CloudSimple – Använd privat molnwebbplats för att vara värd för en virtuell skrivbordsinfrastruktur med VMware Horizon
description: Beskriver hur du kan använda din CloudSimple Private Cloud-webbplats som värd för en virtuell skrivbordsinfrastruktur med VMware Horizon
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 723821a78ecae308443c93567402e3b232c036f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025256"
---
# <a name="use-cloudsimple-private-cloud-site-to-host-a-virtual-desktop-infrastructure-using-vmware-horizon"></a>Använd CloudSimple Private Cloud-webbplatsen för att vara värd för en virtuell skrivbordsinfrastruktur med VMware Horizon

Du kan använda din CloudSimple Private Cloud-webbplats för att vara värd för en virtuell skrivbordsinfrastruktur (VDI) med VMware Horizon 7.x. Följande bild visar den logiska lösningsarkitekturen för VDI.

![Horisontutplacering](media/horizon-deployment.png)

Med den här lösningen har du full kontroll över Horizon View Manager och App Volume. De välbekanta gränssnitten, API:et och CLI-gränssnitten gör det möjligt att använda dina befintliga skript och verktyg.

CloudSimple-lösningen kräver att du gör följande:

* Installera, konfigurera och hantera VMware Horizon 7.x i ditt privata moln.
* Ange dina egna Horizon-licenser.

## <a name="deploy-the-solution"></a>Distribuera lösningen

I följande avsnitt beskrivs hur du distribuerar en VDI-lösning med Horizon i ditt privata moln.

1. [Kontrollera att VMware-produktversioner är kompatibla](#verify-that-vmware-product-versions-are-compatible)
2. [Uppskatta storleken på din skrivbordsmiljö](#estimate-the-size-of-your-desktop-environment)
3. [Skapa ett privat moln för din miljö](#create-a-private-cloud-for-your-environment)
4. [Installera VMware Horizon i ditt privata moln](#install-vmware-horizon-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Kontrollera att VMware-produktversioner är kompatibla

* Kontrollera att dina nuvarande och planerade versioner av Horizon, AppVolymer, Unified Access Gateway och User Environment Manager är kompatibla med varandra och med vCenter och PSC i det privata molnet. Information om kompatibilitet finns i [VMware Compatibility Matrix för Horizon 7.5](https://www.vmware.com/resources/compatibility/sim/interop_matrix.php#interop&260=2877&0=).
* Om du vill ta reda på de aktuella versionerna av vCenter och PSC i ditt privata moln går du till **Resurser** i [CloudSimple-portalen,](access-cloudsimple-portal.md)väljer ditt privata moln och klickar på fliken **vSphere Management Network.**

![vCenter- och PSC-versioner](media/private-cloud-vsphere-versions.png)

### <a name="estimate-the-size-of-your-desktop-environment"></a>Uppskatta storleken på din skrivbordsmiljö

* Kontrollera att den identifierade konfigurationen ligger inom VMwares driftsgränser.
* Uppskatta de resurser som behövs för alla dina skrivbord och dina Horizon-hanteringskomponenter.

### <a name="create-a-private-cloud-for-your-environment"></a>Skapa ett privat moln för din miljö

1. Skapa ett privat moln från CloudSimple-portalen genom att följa instruktionerna i [Konfigurera en privat molnmiljö](quickstart-create-private-cloud.md).  CloudSimple skapar en standard vCenter-användare med namnet "cloudowner" i varje nyskapade privata moln. Mer information om standardmodellen för användare och behörighet för Private Cloud finns [i Lär dig behörighetsmodellen För privat moln](learn-private-cloud-permissions.md).
2. Skapa en VLAN i ditt privata moln för Horizon-hanteringsplanet och tilldela det ett undernät CIDR. Instruktioner finns i [Skapa och hantera VLAN/undernät](create-vlan-subnet.md). Det här är nätverket där alla lösningskomponenter (Unified Access Gateway, Connection Server, App Volume Server och User Environment Manager-servrar) ska installeras.
3. Bestäm om du vill använda en extern identitetsleverantör med ditt privata moln vCenter. Om ja väljer du något av följande alternativ:
    * Använd den lokala Active Directory som extern identitetsprovider. Instruktioner finns i [vCenter Identity Sources](set-vcenter-identity.md).
    * Konfigurera en Active Directory-server i det privata molnet i Horizon-hanteringsplanet VLAN för att använda som extern identitetsprovider. Instruktioner finns i [vCenter Identity Sources](set-vcenter-identity.md).
    * Konfigurera en DHCP- och DNS-server i Horizon-hanteringsplanet VLAN i det privata molnet. Instruktioner finns i [Konfigurera DNS- och DHCP-program och arbetsbelastningar i ditt CloudSimple Private Cloud](dns-dhcp-setup.md).
4. Konfigurera DNS-vidarebefordran på DNS-servern som är installerad i det privata molnet. Instruktioner finns i [Skapa en villkorlig vidarebefordrare](on-premises-dns-setup.md#create-a-conditional-forwarder).

### <a name="install-vmware-horizon-in-your-private-cloud"></a>Installera VMware Horizon i ditt privata moln

Följande distributionsdiagram visar en Horizon-lösning som distribueras i ett privat moln. Unified Access Gateway, AD/DC, View och App Volume Server installeras i användarskapade VLAN 234. Unified Access Gateway har en tilldelad offentlig IP-adress som kan nås från Internet. Virtuella datorer för horisontspooler distribueras i VLAN 235 för att ge ytterligare isolering och säkerhet.

![Horizon-distribution i det privata molnet](media/horizon-private-cloud.png)

I följande avsnitt beskrivs instruktionerna för att konfigurera en distribution som liknar den som visas i figuren. Innan du börjar kontrollerar du att du har följande:

* Ett privat moln som skapats med CloudSimple-portalen med tillräcklig kapacitet för att köra dina skrivbordspooler.
* Tillräcklig bandbredd mellan din lokala miljö och den privata molnmiljön för att stödja nätverkstrafiken för dina skrivbord.
* En VPN-tunnel för plats till plats som konfigureras mellan ditt lokala datacenter och det privata molnet.
* IP-nåbarhet från slutanvändarens undernät i din lokala miljö till Undernäten CloudSimple Private Cloud.
* AD/DHCP/DNS installerat för ditt privata moln.

#### <a name="cloudsimple-portal-create-a-dedicated-vlansubnet-for-desktop-pools"></a>CloudSimple-portal: Skapa ett dedikerat VLAN/-undernät för skrivbordspooler

Skapa en VLAN för Horizon-skrivbordspoolerna och tilldela den ett undernät CIDR. Instruktioner finns i [Skapa och hantera VLAN/undernät](create-vlan-subnet.md). Det här är nätverket där alla virtuella datorer för skrivbordet ska köras.

Följ standardpraxis för säkerhet för att skydda din Horizon-distribution:

* Tillåt endast stationär RDP-trafik / SSH-trafik till dina virtuella datorer på skrivbordet.
* Tillåt endast hanteringstrafik mellan Horizon management plan VLAN och skrivbordspool VLAN.
* Tillåt endast hanteringstrafik från lokalt nätverk.

Du kan tillämpa dessa metodtips genom att konfigurera [brandväggsregler](firewall.md) från CloudSimple-portalen.

#### <a name="cloudsimple-portal-configure-firewall-rules-to-secure-horizon-management-plane"></a>CloudSimple-portal: Konfigurera brandväggsregler för att skydda Horizon-hanteringsplan

Ställ in följande regler i CloudSimple-portalen. Instruktioner finns i [Konfigurera brandväggstabeller och regler](firewall.md).

1. Konfigurera brandväggsregler i CloudSimple N-S-brandväggen så att kommunikation mellan lokala undernät och Horizon-hantering VLAN tillåts så att endast de nätverksportar som anges i VMware-dokumentet [Horizon-portlistan](https://docs.vmware.com/en/VMware-Horizon-7/7.1/com.vmware.horizon-client-agent.security.doc/GUID-52807839-6BB0-4727-A9C7-EA73DE61ADAB.html) tillåts.

2. Skapa E-W-brandväggsregler mellan VLAN-horisonthanteringen och den stationära poolen VLAN i det privata molnet.

#### <a name="cloudsimple-portal-create-a-public-ip-address-for-unified-access-gateway"></a>CloudSimple-portal: Skapa en offentlig IP-adress för Unified Access Gateway

Skapa en offentlig IP-adress för unified Access Gateway-enheten för att aktivera klientanslutningar för stationära datorer från Internet. Instruktioner finns i [Tilldela offentliga IP-adresser](public-ips.md).

När installationen är klar tilldelas den offentliga IP-adressen och visas på sidan Offentliga IP-adresser.

#### <a name="cloudsimple-portal-escalate-privileges"></a>CloudSimple-portal: Eskalera privilegier

Standardanvändaren för molnägare har inte tillräckliga privilegier i Private Cloud vCenter för att installera Horizon, så användarens vCenter-privilegier måste eskaleras. Mer information finns i [Eskalera privilegier](escalate-private-cloud-privileges.md).

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-horizon-installation"></a>vCenter UI: Skapa en användare i Privat Moln för Horizon-installation

1. Logga in på vCenter med användarautentiseringsuppgifterna för "cloudowner".
2. Skapa en ny användare, "horizon-soln-admin", i vCenter och lägg till användaren i administratörsgruppen i vCenter.
3. Logga ut från vCenter som "cloudowner"-användare och logga in som användare av horisont-soln-admin.

#### <a name="vcenter-ui-install-vmware-horizon"></a>vCenter UI: Installera VMware Horizon

Som nämnts i det tidigare logiska arkitekturavsnittet har Horizon-lösningen följande komponenter:

* VMware Horizon-vy
* VMware Unified Access Gateway
* Volymhanteraren för VMware-appen
* VMware-användarmiljöhanterare

Installera komponenterna enligt följande:

1. Installera och konfigurera Unified Access Gateway genom att följa instruktionerna i VMware-dokumentet [Distribuera och konfigurera VMware Unified Access Gateway](https://docs.vmware.com/en/Unified-Access-Gateway/3.3.1/com.vmware.uag-331-deploy-config.doc/GUID-F5CE0D5E-BE85-4FA5-BBCF-0F86C9AB8A70.html).

2. Installera Horizon View i det privata molnet genom att följa instruktionerna i [Visa installationsguiden](https://docs.vmware.com/en/VMware-Horizon-7/7.4/horizon-installation/GUID-37D39B4F-5870-4188-8B11-B6C41AE9133C.html).

3. Installera App Volume Manager genom att följa instruktionerna i [Installera och konfigurera VMware-appvolymer](https://docs.vmware.com/en/VMware-App-Volumes/2.10/com.vmware.appvolumes.user.doc/GUID-5E8BAF8C-F5A6-412C-9424-266BA7109BA4.html).

4. Installera och konfigurera User Environment Manager genom att följa instruktionerna i [Om att installera och konfigurera VMware User Environment Manager](https://docs.vmware.com/en/VMware-User-Environment-Manager/9.4/com.vmware.user.environment.manager-install-config/GUID-DBBC82E4-483F-4B28-9D49-4D28E08715BC.html).

#### <a name="file-a-support-request-to-upload-vmware-horizon-pre-packaged-app-volumes"></a>Lämna in en supportbegäran om att ladda upp VMware Horizon förpaketerade appvolymer

Som en del av installationsprocessen använder App Volume Manager förpaketerade volymer för att etablera appstaplar och skrivbara volymer. Dessa volymer fungerar som mallar för appstaplar och skrivbara volymer.

Att överföra volymerna till datalagret Private Cloud kräver ESXi-rotlösenordet. Om du behöver hjälp skickar du en [supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Bifoga installationspaketet för AppVolumes så att CloudSimple-supportpersonal kan ladda upp mallarna till din privata molnmiljö.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>CloudSimple-portal: De-eskalera privilegier

Du kan nu [de-eskalera privilegierna](escalate-private-cloud-privileges.md#de-escalate-privileges) för användaren "cloudowner".

## <a name="ongoing-management-of-your-horizon-solution"></a>Löpande hantering av din Horizon-lösning

Du har full kontroll över Programvaran Horizon och App Volume Manager i din privata molnmiljö och förväntas utföra nödvändig livscykelhantering för programvara. Kontrollera att alla nya versioner av programvaran är kompatibla med Private Cloud vCenter och PSC innan du uppdaterar eller uppgraderar Horizon eller App Volume.
