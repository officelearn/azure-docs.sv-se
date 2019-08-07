---
title: Snabb start – skapa en virtuell VMware-dator i ett privat moln
description: Beskriver hur du skapar och en virtuell VMware-dator på CloudSimple privata moln
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/03/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7d21035fc3e9e80344264b9fde21820162f376d3
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816704"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>Skapa virtuella VMware-datorer i ditt privata moln

Börja med att komma åt CloudSimple-portalen från Azure Portal om du vill skapa virtuella datorer i ditt privata moln.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Få åtkomst till CloudSimple-portalen

1. Välj **Alla tjänster**.
2. Sök efter **CloudSimple-tjänster**.
3. Välj den CloudSimple-tjänst som du vill skapa ditt privata moln på.
4. På sidan **Översikt** klickar du på **gå till CloudSimple-portalen** för att öppna en ny flik i webbläsaren för CloudSimple-portalen.  Logga in med dina inloggnings uppgifter för Azure om du uppmanas till detta.  

    ![Starta CloudSimple-portalen](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>Starta vCenter Web-UI

Du kan nu starta vCenter för att konfigurera virtuella datorer och principer.

Kom åt vCenter genom att starta från CloudSimple-portalen. På Start sidan, under **vanliga uppgifter**, klickar du på **Starta vSphere-klient**.  Välj det privata molnet och klicka sedan på **Starta vSphere-klient** i det privata molnet.

   ![Starta vSphere-klienten](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>Ladda upp en ISO-eller vSphere-mall

> [!WARNING]
> För ISO-uppladdningar använder du vSphere HTML5-klienten.  Att använda Flash-klienten kan resultera i ett fel.

1. Hämta ISO-eller vSphere-mallen som du vill ladda upp till vCenter för att skapa en virtuell dator och ha den tillgänglig i det lokala systemet.

2. Klicka på **disk** ikonen i vCenter och välj **vsanDatastore**. Klicka på **filer** och sedan på **ny mapp**.

    ![vCenter ISO](media/vcenter-create-folder.png)

3. Skapa en mapp för att lagra ISO-filer.

4. Navigera till den nya mappen som skapats och klicka på **överför filer**. Följ anvisningarna på skärmen för att ladda upp ISO.

## <a name="create-a-virtual-machine-in-vcenter"></a>Skapa en virtuell dator i vCenter

1. I vCenter klickar du på ikonen **värdar och kluster** .

2. Högerklicka på **arbets belastning** och välj **ny virtuell dator**.
    
    ![Skapa en virtuell dator](media/create-vcenter-virtual-machine-01.png)

3. Välj **Skapa ny virtuell dator** och klicka på **Nästa**.

    ![Guiden ny virtuell dator](media/create-vcenter-virtual-machine-02.png)

4. Ge datorn namnet, Välj mappen för den **virtuella arbets belastningen** och klicka på **Nästa**.

    ![Välj namn och mapp](media/create-vcenter-virtual-machine-03.png)

5. Välj resurs för beräkning av **arbets belastning** och klicka på **Nästa**.

    ![Välj beräknings resurs](media/create-vcenter-virtual-machine-04.png)

6. Välj **vsanDatastore** och klicka på **Nästa**.

    ![Välj Storage](media/create-vcenter-virtual-machine-05.png)

7. Behåll standard valet av ESXi 6,5 och klicka på **Nästa**.

    ![Välj kompatibilitet](media/create-vcenter-virtual-machine-06.png)

8. Välj gäst operativ systemet för den virtuella datorns ISO för den virtuella datorn och klicka på **Nästa**.

    ![Anpassa gäst operativ system](media/create-vcenter-virtual-machine-07.png)

9. Välj alternativ för hård disk och nätverk. För ny CD/DVD-enhet väljer du **data lager ISO-fil**.  Om du vill tillåta trafik från den offentliga IP-adressen till den virtuella datorn väljer du nätverket som **VM-1**.

    ![Välj maskin varu anpassning](media/create-vcenter-virtual-machine-08.png)

10. Ett val fönster öppnas. Välj den fil som du tidigare laddade upp till mappen ISO och mallar och klicka på **OK**.

    ![Välj ISO](media/create-vcenter-virtual-machine-10.png)

11. Granska inställningarna och klicka på **OK** för att skapa den virtuella datorn.

    ![Gransknings alternativ](media/create-vcenter-virtual-machine-11.png)

Den virtuella datorn läggs nu till i beräknings resurserna för arbets belastningen och är redo att användas. 

![Ny virtuell dator i vCenter](media/create-vcenter-virtual-machine-12.png)

Den grundläggande installationen slutförs nu. Du kan börja använda ditt privata moln på samma sätt som du använder din lokala infrastruktur för virtuella datorer.

Nästa avsnitt innehåller information om hur du konfigurerar DNS-och DHCP-servrar för privata moln arbets belastningar och ändrar standard konfigurationen för nätverk.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>Lägg till användare och identitets källor till vCenter (valfritt)

CloudSimple tilldelar ett standard-vCenter-användarkonto med användar namn **cloudowner@cloudsimple.local** . Det krävs inga ytterligare konto inställningar för att komma igång.  CloudSimple tilldelar vanligt vis administratörer de privilegier de behöver för att utföra normala åtgärder.  Konfigurera din lokala Active Directory eller Azure AD som en [ytterligare identitets källa](https://docs.azure.cloudsimple.com/set-vcenter-identity/) i ditt privata moln.

## <a name="create-a-dns-and-dhcp-server-optional"></a>Skapa en DNS-och DHCP-server (valfritt)

Program och arbets belastningar som körs i en privat moln miljö kräver namn matchning och DHCP-tjänster för sökning och tilldelning av IP-adresser. En korrekt DHCP-och DNS-infrastruktur krävs för att tillhandahålla dessa tjänster. Du kan konfigurera en virtuell dator i vCenter för att tillhandahålla dessa tjänster i din privata moln miljö.

### <a name="prerequisites"></a>Förutsättningar

* En distribuerad port grupp med VLAN konfigurerat

* Dirigera konfiguration till lokala eller Internetbaserade DNS-servrar

* Mall för virtuella datorer eller ISO för att skapa en virtuell dator

Följande länkar ger vägledning om hur du konfigurerar DHCP-och DNS-servrar i Linux och Windows.

### <a name="linux-based-dns-server-setup"></a>Installation av Linux-baserad DNS-Server

Linux erbjuder olika paket för att konfigurera DNS-servrar.  Här är en länk till instruktioner för att skapa en BIND DNS-server med öppen källkod.

[Exempel på installation](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

### <a name="windows-based-setup"></a>Windows-baserad installation

Dessa Microsoft-artiklar beskriver hur du konfigurerar en Windows-Server som en DNS-server och som en DHCP-server.
<br>
[Windows Server som DNS-Server](https://docs.microsoft.com/windows-server/networking/dns/dns-top)

[Windows Server som DHCP-server](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>Anpassa nätverks konfiguration (valfritt)

På nätverks sidorna i CloudSimple-portalen kan du ange konfigurationen för brand Väggs tabeller och offentliga IP-adresser för virtuella datorer.

### <a name="allocate-public-ips"></a>Allokera offentliga IP-adresser

1. Navigera till **nätverks > offentlig IP-adress** i CloudSimple-portalen.
2. Klicka på **allokera offentlig IP**.
3. Ange ett namn för att identifiera IP-adress posten.
4. Välj plats för ditt privata moln.
5. Använd skjutreglaget för att ändra tids gränsen för inaktivitet om du vill.
6. Ange den lokala IP-adress som du vill tilldela en offentlig IP-adress för.
7. Ange ett associerat DNS-namn om du vill.
8. Klicka på **Klar**.

    ![Offentlig IP](media/quick-create-pc-public-ip.png)

Uppgiften att allokera den offentliga IP-adressen börjar. Du kan kontrol lera status för aktiviteten på sidan **aktiviteter > uppgifter** . När allokeringen är klar visas den nya posten på sidan offentliga IP-adresser.

Den virtuella dator som den här IP-adressen måste mappas till måste konfigureras med den lokala adress som anges ovan. Proceduren för att konfigurera en IP-adress är speciell för operativ systemet för den virtuella datorn. I dokumentationen för operativ systemet för den virtuella datorn finns en korrekt procedur.

#### <a name="example"></a>Exempel

Här är till exempel information om Ubuntu 16,04.

Lägg till den statiska metoden i inet-adress familjens konfiguration i ```/etc/network/interfaces```filen. Ändra adress, nätmask och gateway-värden. I det här exemplet använder vi eth0-gränssnittet, interna IP-192.168.24.10, Gateway-192.168.24.1 och netmask 255.255.255.0. 

```interfaces``` Redigera filen.

```
sudo vi /etc/network/interfaces
```

Uppdatera följande avsnitt i ```interfaces``` filen.

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

Som standard nekas all inkommande trafik från Internet. Skapa en [brand Väggs tabell](https://docs.azure.cloudsimple.com/firewall/)om du vill öppna någon annan port.

När du har konfigurerat en intern IP-adress som statisk IP-adress kontrollerar du att du kan ansluta till Internet från den virtuella datorn.

```
ping 8.8.8.8
```

Kontrol lera att du kan komma åt den virtuella datorn från Internet med hjälp av den offentliga IP-adressen.

Se till att alla brand Väggs regler (iptable) på den virtuella datorn inte blockerar port 80 inkommande.

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

Starta en webbläsare på Skriv bordet och peka på port 80 för den offentliga IP-adressen för att bläddra bland filerna på den virtuella datorn. 

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>Standard brand Väggs regler för offentlig IP-CloudSimple

* VPN-trafik: All trafik mellan (från/till) VPN och alla arbets belastnings nätverk och hanterings nätverk tillåts.
* Intern trafik för privat moln: All öst-väst-trafik mellan (från/till) arbets belastnings nätverk och hanterings nätverket (visas ovan) tillåts.
* Internet trafik:
    * All inkommande trafik från Internet nekas till arbets belastnings nätverk och hanterings nätverket.
    * All utgående trafik till Internet från arbets belastnings nätverk eller hanterings nätverk tillåts.

Du kan också ändra hur trafiken skyddas med hjälp av funktionen brand Väggs regler. Mer information finns i [Konfigurera brand Väggs tabeller och regler](https://docs.azure.cloudsimple.com/firewall/).

## <a name="install-solutions-optional"></a>Installera lösningar (valfritt)
Du kan installera lösningar på ditt privata moln i CloudSimple för att dra full nytta av din privata moln vCenter-miljö. Du kan konfigurera säkerhets kopiering, katastrof återställning, replikering och andra funktioner för att skydda dina virtuella datorer. Exempel på detta är VMware Site Recovery Manager (VMware SRM) och säkerhets kopiering & replikering av Veeam.

Om du vill installera en lösning måste du begära ytterligare behörigheter under en begränsad period. Se [eskalera privilegier](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges).

## <a name="next-steps"></a>Nästa steg

* [Använda virtuella VMware-datorer på Azure](quickstart-create-vmware-virtual-machine.md)
* [Ansluta till ett lokalt nätverk med hjälp av Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection)
* [Konfigurera VPN-gatewayer i CloudSimple-nätverk](https://docs.azure.cloudsimple.com/vpn-gateway)
