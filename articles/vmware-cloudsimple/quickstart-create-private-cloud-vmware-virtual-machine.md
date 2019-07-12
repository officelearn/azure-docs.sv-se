---
title: Snabbstart – skapa en virtuell VMware-dator på ett privat moln
description: Beskriver hur du skapar och en VMware-VM på CloudSimple privat moln
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/03/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 33354ce09ad6ba1a9a7c08a8cd3b945f3788011a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595688"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>Skapa virtuella VMware-datorer i ditt privata moln

Börja genom att gå till portalen CloudSimple från Azure-portalen för att skapa virtuella datorer i ditt privata moln.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Få åtkomst till CloudSimple-portalen

1. Välj **Alla tjänster**.
2. Sök efter **CloudSimple Services**.
3. Välj tjänsten CloudSimple där du vill skapa ditt privata moln.
4. Från den **översikt** klickar du på **gå till portalen CloudSimple** att öppna en ny webbläsarflik för CloudSimple-portalen.  Om du uppmanas logga in med ditt Azure inloggningsuppgifter.  

    ![Starta CloudSimple-portalen](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>Starta vCenter-webbgränssnittet

Nu kan du starta vCenter att konfigurera virtuella datorer och principer.

Starta från CloudSimple-portal för att komma åt vCenter. På startsidan, under **vanliga uppgifter**, klickar du på **starta vSphere-klienten**.  Välj privat moln och klickar sedan på **starta vSphere-klienten** på privat moln.

   ![Starta vSphere-klienten](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>Ladda upp en ISO- eller vSphere-mall

> [!WARNING]
> Använd vSphere HTML5-klienten för ISO-överföringar.  Med hjälp av Flash-klienten kan resultera i ett fel.

1. Hämta ISO- eller vSphere-mallen som du vill överföra till vCenter att skapa en virtuell dator och den tillgängliga i det lokala systemet.

2. I vCenter, klickar du på den **Disk** och välj **vsanDatastore**. Klicka på **filer** och klicka sedan på **ny mapp**.

    ![vCenter ISO](media/vcenter-create-folder.png)

3. Skapa en mapp för att lagra ISO-filer.

4. Navigera till den nya mappen som skapas och klicka på **Överför filer**. Följ den på skärmen instruktionerna för att ladda upp ISO.

## <a name="create-a-virtual-machine-in-vcenter"></a>Skapa en virtuell dator i vCenter

1. I vCenter, klickar du på den **värdar och kluster** ikon.

2. Högerklicka på **arbetsbelastning** och välj **ny virtuell dator**.
    
    ![Skapa en virtuell dator](media/create-vcenter-virtual-machine-01.png)

3. Välj **Skapa ny virtuell dator** och klicka på **nästa**.

    ![Guiden Ny virtuell dator](media/create-vcenter-virtual-machine-02.png)

4. Namn på datorn väljer den **arbetsbelastning VM** mappen och klicka på **nästa**.

    ![Välj namn och mapp](media/create-vcenter-virtual-machine-03.png)

5. Välj den **arbetsbelastning** beräkningsresurs och klicka på **nästa**.

    ![Välj beräkningsresurs](media/create-vcenter-virtual-machine-04.png)

6. Välj **vsanDatastore** och klicka på **nästa**.

    ![Välj Storage](media/create-vcenter-virtual-machine-05.png)

7. Behåll standardvalet ESXi 6.5-kompatibilitet och klicka på **nästa**.

    ![Välj kompatibilitet](media/create-vcenter-virtual-machine-06.png)

8. Välj gäst-OS i ISO för den virtuella datorn och klicka på **nästa**.

    ![Anpassa gäst-OS](media/create-vcenter-virtual-machine-07.png)

9. Välj hårddisk och Nätverksalternativ. Ny CD/DVD-enheten, Välj **Datastore ISO-filen**.  Om du vill tillåta trafik från den offentliga IP-adressen till den virtuella datorn, väljer du i nätverket som **vm-1**.

    ![Välj maskinvara anpassning](media/create-vcenter-virtual-machine-08.png)

10. Öppnas ett fönster för val av. Välj den fil som du tidigare har överförts till mappen ISO och mallar och klickar på **OK**.

    ![Välj ISO](media/create-vcenter-virtual-machine-10.png)

11. Granska inställningarna och klicka på **OK** att skapa den virtuella datorn.

    ![Granska alternativ](media/create-vcenter-virtual-machine-11.png)

Den virtuella datorn läggs nu till beräkningsresurser arbetsbelastning och är redo att användas. 

![Ny virtuell dator i vCenter](media/create-vcenter-virtual-machine-12.png)

Den grundläggande installationen är slutförd. Du kan börja använda dina privata moln som liknar hur du använder din lokala infrastruktur med virtuella datorer.

Nästa avsnitt innehåller valfri information om att konfigurera DNS och DHCP-servrar för privat moln arbetsbelastningar och ändra standard nätverkskonfigurationen.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>Lägga till användare och identitet källor till vCenter (valfritt)

CloudSimple tilldelar ett standard vCenter-användarkonto med användarnamn **cloudowner@cloudsimple.local** . Det krävs inga ytterligare kontokonfiguration för dig att komma igång.  CloudSimple tilldelar normalt administratörer den behörighet de behöver för att utföra normala åtgärder.  Konfigurera din lokala active directory eller Azure AD som en [ytterligare identitetskälla](https://docs.azure.cloudsimple.com/set-vcenter-identity/) i ditt privata moln.

## <a name="create-a-dns-and-dhcp-server-optional"></a>Skapa en DNS- och DHCP-server (valfritt)

Program och arbetsbelastningar som körs i en privat molnmiljö kräver namnmatchning och DHCP-tjänster för sökning och IP-adresstilldelning. En korrekt DHCP och DNS-infrastruktur krävs för att tillhandahålla tjänsterna. Du kan konfigurera en virtuell dator i vCenter att tillhandahålla dessa tjänster i din miljö för privat moln.

### <a name="prerequisites"></a>Förutsättningar

* En distribuerad portgrupp med VLAN som har konfigurerats

* Väg ställa in att en lokal eller Internet-baserad DNS-servrar

* Mall för virtuell dator eller ISO att skapa en virtuell dator

Följande länkar ger vägledning om hur du konfigurerar DHCP och DNS-servrar på Linux och Windows.

### <a name="linux-based-dns-server-setup"></a>Linux-Based DNS Server Setup

Linux erbjuder olika paket för att konfigurera DNS-servrar.  Här är en länk till anvisningar för att skapa en öppen källkod BINDA DNS-server.

[Exempelinställning](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

### <a name="windows-based-setup"></a>Windows-baserad installation

Dessa Microsoft-artiklar beskriver hur du konfigurerar en Windows-server som en DNS-server och som en DHCP-server.
<br>
[Windows Server som DNS-Server](https://docs.microsoft.com/windows-server/networking/dns/dns-top)

[Windows Server som DHCP-Server](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>Anpassa nätverkskonfiguration (valfritt)

Nätverket sidorna i CloudSimple-portalen kan du ange konfigurationen för brandväggen tabeller och den offentliga IP-adresser för virtuella datorer.

### <a name="allocate-public-ips"></a>Tilldela offentliga IP-adresser

1. Gå till **nätverk > offentliga IP-Adressen** CloudSimple-portalen.
2. Klicka på **tilldela offentliga IP-Adressen**.
3. Ange ett namn som identifierar IP-adresspost.
4. Välj platsen för ditt privata moln.
5. Använd skjutreglaget för att ändra tidsgränsen för inaktivitet om du vill.
6. Ange lokala IP-adressen som du vill tilldela en offentlig IP-adress.
7. Ange ett tillhörande DNS-namn om du vill.
8. Klicka på **Klar**.

    ![Offentlig IP-adress](media/quick-create-pc-public-ip.png)

Uppgiften att tilldela offentliga IP-adress börjar. Du kan kontrollera status för uppgiften i **aktivitet > uppgifter** sidan. När allokering är klar visas den nya posten på sidan offentliga IP-adresser.

Den virtuella datorn som den här IP-adressen måste mappas måste konfigureras med den lokala adressen som anges ovan. Proceduren för att konfigurera en IP-adress är specifik för VM-operativsystem. I dokumentationen för operativsystemet virtuell dator om hur du gör.

#### <a name="example"></a>Exempel

Här visas till exempel information om Ubuntu 16.04.

Lägg till statisk metod inet family adresskonfigurationen i filen ```/etc/network/interfaces```. Ändra adress, nätmask och gateway-värden. I det här exemplet använder vi eth0 gränssnitt, interna IP-adressen 192.168.24.10, gatewayadress 192.168.24.1 och nätmask 255.255.255.0. 

Redigera den ```interfaces``` filen.

```
sudo vi /etc/network/interfaces
```

Uppdatera följande avsnitt i ```interfaces``` fil.

```
auto eth0
iface eth0 inet static
address 192.168.24.10
netmask 255.255.255.0
gateway 192.168.24.1
dns-nameservers 8.8.8.8
dns-domain acme.com
dns-search acme.com
```

Inaktivera gränssnittet.

```
sudo ifdown eth0
```

Aktivera gränssnittet igen.

```
sudo ifup eth0
```

Som standard all inkommande trafik från Internet är **nekas**. Om du vill öppna någon annan port skapar du en [brandväggen tabell](https://docs.azure.cloudsimple.com/firewall/).

Kontrollera att du kan nå Internet från den virtuella datorn när du har konfigurerat en intern IP-adress som statiska IP-adress.

```
ping 8.8.8.8
```

Kontrollera att du kan nå den virtuella datorn från Internet med offentliga IP-adress.

Se till att alla brandväggsregler (iptable) på den virtuella datorn inte blockerar port 80 inkommande.

```
netstat -an | grep 80
```

Starta en http-server som lyssnar på port 80.
       
```
python2.7 -m SimpleHTTPServer 80
```

eller

```
python3 -m http.server 80
```

Starta en webbläsare på ditt skrivbord och peka den till port 80 för den offentliga IP-adressen till Sök till filerna på den virtuella datorn. 

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>Standard CloudSimple brandväggsregler för offentlig IP-adress

* VPN-trafik: All trafik mellan (från/till) det virtuella privata nätverket och alla arbetsbelastning nätverk och hanteringsnätverk tillåts.
* Privat moln intern trafik: Tillåts alla öst-väst-trafik mellan (från/till) arbetsbelastning nätverk och -hantering (se ovan).
* Internet-trafik:
    * All inkommande trafik från Internet är tillgängliga för arbetsbelastningen nätverk och -hantering.
    * All utgående trafik till Internet från arbetsbelastningen nätverk eller hanteringsnätverket tillåts.

Du kan också ändra hur trafiken är skyddad, använder funktionen brandväggsregler. Mer information finns i [ställa in brandväggen tabeller och regler](https://docs.azure.cloudsimple.com/firewall/).

## <a name="install-solutions-optional"></a>Installera lösningar (valfritt)
Du kan installera lösningar på din CloudSimple privat moln att dra full nytta av ditt privata moln vCenter-miljö. Du kan ställa in säkerhetskopiering, katastrofåterställning, replikering och andra funktioner för att skydda dina virtuella datorer. Exempel är VMware Site Recovery Manager (VMware SRM) och Veeam säkerhetskopiering och replikering.

Om du vill installera en lösning måste du begära ytterligare behörigheter för en begränsad period. Se [eskalera privilegier](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges).

## <a name="next-steps"></a>Nästa steg

* [Använda VMware-datorer på Azure](quickstart-create-vmware-virtual-machine.md)
* [Ansluta till lokala nätverk med Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection)
* [Konfigurera VPN-gatewayer i CloudSimple nätverk](https://docs.azure.cloudsimple.com/vpn-gateway)
