---
title: Snabbstart – skapa en virtuell azure VMware-dator i ett privat moln – Azure VMware-lösning från CloudSimple
description: Beskriver hur du skapar en virtuell azure VMware-dator i ett CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4ac818cfd267b781366c0e32c9f93cc885dff99c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77566156"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>Skapa virtuella VMware-datorer i ditt privata moln

Om du vill skapa virtuella datorer i ditt privata moln börjar du med att komma åt CloudSimple-portalen från Azure-portalen.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Få åtkomst till CloudSimple-portalen

1. Välj **Alla tjänster**.
2. Sök efter **CloudSimple Services**.
3. Välj den CloudSimple-tjänst som du vill skapa ditt privata moln på.
4. På sidan **Översikt** klickar du på **Gå till CloudSimple-portalen** för att öppna en ny webbläsarflik för CloudSimple-portalen.  Om du uppmanas till det loggar du in med dina Azure-inloggningsuppgifter.  

    ![Starta CloudSimple-portalen](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>Starta vCenter web-ui

Du kan nu starta vCenter för att ställa in virtuella datorer och principer.

Om du vill komma åt vCenter startar du från CloudSimple-portalen. Klicka på **Starta vSphere-klient**under **Vanliga uppgifter**på startsidan.  Markera det privata molnet och klicka sedan på **Starta vSphere-klienten** i det privata molnet.

   ![Starta vSphere-klient](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>Ladda upp en ISO- eller vSphere-mall

  > [!WARNING]
  > För ISO-uppladdning använder du vSphere HTML5-klienten.  Om du använder en Flash-klient kan det leda till ett fel.

1. Skaffa iso- eller vSphere-mallen som du vill ladda upp till vCenter för att skapa en virtuell dator och ha den tillgänglig på ditt lokala system.
2. I vCenter klickar du på **diskikonen** och väljer **vsanDatastore**. Klicka på **Filer** och sedan på **Ny mapp**.
    ![vCenter ISO](media/vciso00.png)

3. Skapa en mapp med titeln "Iso-filer och mallar".

4. Navigera till isos-mappen i Isoos och mallar och klicka på **Ladda upp filer**. Följ instruktionerna på skärmen för att ladda upp ISO.Follow the on-screen instructions to upload the ISO.

## <a name="create-a-virtual-machine-in-vcenter"></a>Skapa en virtuell dator i vCenter

1. Klicka på ikonen **Värdar och kluster i** vCenter.

2. Högerklicka på **Arbetsbelastningen** och välj **Ny virtuell dator**.
    ![Ny virtuell dator](media/vcvm01.png)

3. Välj **Skapa ny virtuell dator** och klicka på **Nästa**.
    ![Ny virtuell dator](media/vcvm02.png)

4. Namnge datorn, välj den **virtuella arbetsbelastningens** plats och klicka på **Nästa**.
    ![Ny virtuell dator](media/vcvm03.png)

5. Välj **beräkningsresurs** för arbetsbelastning och klicka på **Nästa**.
    ![Ny virtuell dator](media/vcvm04.png)

6. Välj **vsanDatastore** och klicka på **Nästa**.
    ![Ny virtuell dator](media/vcvm05.png)

7. Behåll standardvalet för KOMPATIBILITETEN ESXi 6.5 och klicka på **Nästa**.
    ![Ny virtuell dator](media/vcvm06.png)

8. Välj gäst-OS för ISO för den virtuella datorn som du skapar och klicka på **Nästa**.
    ![Ny virtuell dator](media/vcvm07.png)

9. Välj hårddisk- och nätverksalternativ. För ny CD/DVD-enhet väljer du **Datastore ISO-fil**.  Om du vill tillåta trafik från den offentliga IP-adressen till den här virtuella datorn väljer du nätverket som **vm-1**.
    ![Ny virtuell dator](media/vcvm08.png)

10. Ett markeringsfönster öppnas. Markera filen som du tidigare har laddat upp till mappen Iso-filer och mallar och klicka på **OK**.
    ![Ny virtuell dator](media/vcvm10.png)

11. Granska inställningarna och klicka på **OK** för att skapa den virtuella datorn.
    ![Ny virtuell dator](media/vcvm11.png)

Den virtuella datorn läggs nu till i beräkningsresurserna för arbetsbelastning och är klar för användning. 
![Ny virtuell dator](media/vcvm12.png)

Den grundläggande inställningen är nu klar. Du kan börja använda ditt privata moln på samma sätt som du skulle använda din lokala VM-infrastruktur.

Följande avsnitt innehåller valfri information om hur du konfigurerar DNS- och DHCP-servrar för arbetsbelastningar i privata moln och ändrar standardkonfigurationen för nätverk.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>Lägga till användare och identitetskällor i vCenter (valfritt)

CloudSimple tilldelar ett standardkonto för `cloudowner@cloudsimple.local`vCenter-användarkonto med användarnamn . Ingen ytterligare kontokonfiguration krävs för att du ska komma igång.  CloudSimple tilldelar normalt administratörer de privilegier de behöver för att utföra normala åtgärder.  Konfigurera din lokala active directory eller Azure AD som ytterligare [en identitetskälla](set-vcenter-identity.md) i ditt privata moln.

## <a name="create-a-dns-and-dhcp-server-optional"></a>Skapa en DNS- och DHCP-server (valfritt)

Program och arbetsbelastningar som körs i en privat molnmiljö kräver namnmatchning och DHCP-tjänster för uppslag och IP-adresstilldelning. En korrekt DHCP- och DNS-infrastruktur krävs för att tillhandahålla dessa tjänster. Du kan konfigurera en virtuell dator i vCenter för att tillhandahålla dessa tjänster i din privata molnmiljö.

Krav

* En distribuerad portgrupp med VLAN konfigurerad

* Dirigera installation till lokala eller Internetbaserade DNS-servrar

* Mall för virtuell dator eller ISO för att skapa en virtuell dator

Följande länkar ger vägledning om hur du konfigurerar DHCP- och DNS-servrar på Linux och Windows.

#### <a name="linux-based-dns-server-setup"></a>Linux-baserad DNS-serverinstallation

Linux erbjuder olika paket för att konfigurera DNS-servrar.  Här är en länk till instruktioner för att konfigurera en BIND DNS-server med öppen källkod.

[Exempelinställning](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

#### <a name="windows-based-setup"></a>Installation av Windows-baserad

I de här avsnitten i Microsoft beskrivs hur du konfigurerar en Windows-server som DNS-server och som DHCP-server.

[Windows Server som DNS-server](https://docs.microsoft.com/windows-server/networking/dns/dns-top)

[Windows Server som DHCP-server](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>Anpassa nätverkskonfiguration (valfritt)

Med nätverkssidorna i CloudSimple-portalen kan du ange konfigurationen för brandväggstabeller och offentliga IP-adresser för virtuella datorer.

### <a name="allocate-public-ips"></a>Tilldela offentliga IPs

1. Navigera till **Network > Public IP** i CloudSimple-portalen.
2. Klicka på **Allokera offentlig IP**.
3. Ange ett namn för att identifiera IP-adressposten.
4. Behåll standardplatsen.
5. Använd skjutreglaget för att ändra tidsgränsen för inaktiv tid om så önskas.
6. Ange den lokala IP-adress som du vill tilldela en offentlig IP-adress för.
7. Ange ett associerat DNS-namn om så önskas.
8. Klicka på **Klar**.

    ![Offentlig IP-adress](media/quick-create-pc-public-ip.png)

Uppgiften att allokera den offentliga IP-adressen börjar. Du kan kontrollera status för aktiviteten på sidan **Aktivitet > Aktiviteter.** När allokeringen är klar visas den nya transaktionen på sidan Offentliga IPs.

Den virtuella datorn som den här IP-adressen måste mappas till måste konfigureras med den lokala adressen som anges ovan. Proceduren för att konfigurera en IP-adress är specifik för operativsystemet VM. Mer information om hur du gör finns i dokumentationen för operativsystemet VM.

#### <a name="example"></a>Exempel

Här är till exempel detaljerna för Ubuntu 16.04.

Lägg till den statiska metoden i inet-adressfamiljens konfiguration i filen /etc/network/interfaces. Ändra värden för adress, netmask och gateway. I det här exemplet använder vi eth0-gränssnittet, intern IP-adress 192.168.24.10, gateway-adress 192.168.24.1 och netmask 255.255.255.0. För din miljö finns den tillgängliga nätinformationen i välkomstmeddelandet.

```
sudo vi /etc/network/interfaces
```

```
auto eth0
Iface eth0 inet static
iface eth0 inet static
address 192.168.24.10
netmask 255.255.255.0
gateway 192.168.24.1
dns-nameservers 8.8.8.8
dns-domain acme.com
dns-search acme.com
```
Inaktivera gränssnittet manuellt.

```
sudo ifdown eth0
```
Aktivera gränssnittet manuellt igen.

```
sudo ifup eth0
```

Som standard **nekas**all inkommande trafik från Internet . Om du vill öppna en annan port skapar du en [brandväggstabell](firewall.md).

När du har konfigurerat en intern IP-adress som statisk IP-adress kontrollerar du att du kan nå Internet inifrån den virtuella datorn.

```
ping 8.8.8.8
```
Kontrollera också att du kan nå den virtuella datorn från Internet med den offentliga IP-adressen.

Kontrollera att alla iptable regler på den virtuella datorn inte blockerar port 80 inkommande.
        
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
Starta en webbläsare på skrivbordet och peka den på port 80 för den offentliga IP-adressen för att bläddra i filerna på den virtuella datorn.

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>Standardregler för CloudSimple-brandvägg för offentlig IP

* VPN-trafik: All trafik mellan (från/till) VPN och alla arbetsbelastningsnätverk och hanteringsnätverk är tillåtna.
* Privat intern molntrafik: All öst-västlig trafik mellan (från/till) arbetsbelastningsnätverk och hanteringsnätverket (visas ovan) är tillåtet.
* Internettrafik:
  * All inkommande trafik från Internet nekas arbetsbelastningsnätverk och hanteringsnätverk.
  * All utgående trafik till Internet från arbetsbelastningsnätverk eller hanteringsnätverket är tillåten.

Du kan också ändra hur trafiken är skyddad med hjälp av brandväggsregler funktionen. Mer information finns i [Konfigurera brandväggstabeller och regler](firewall.md).

## <a name="install-solutions-optional"></a>Installera lösningar (valfritt)

Du kan installera lösningar på ditt CloudSimple Private Cloud för att dra full nytta av din privata cloud vCenter-miljö. Du kan ställa in säkerhetskopiering, haveriberedskap, replikering och andra funktioner för att skydda dina virtuella datorer. Exempel på detta är VMware Site Recovery Manager (VMware SRM) och Veeam Backup & Replication.

Om du vill installera en lösning måste du begära ytterligare privilegier under en begränsad period. Se [Behörigheten Eskalera](escalate-private-cloud-privileges.md).

## <a name="next-steps"></a>Nästa steg

* [Använda virtuella VMware-datorer på Azure](quickstart-create-vmware-virtual-machine.md)
* [Ansluta till lokalt nätverk med Azure ExpressRoute](on-premises-connection.md)
* [Konfigurera VPN-gateways i CloudSimple-nätverket](vpn-gateway.md)
