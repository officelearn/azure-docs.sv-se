---
title: Azure VMware-lösning av CloudSimple Quickstart – skapa ett privat moln
description: Lär dig hur du skapar och konfigurerar ett privat moln med Azure VMware-lösning genom CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: efeadd2e1600e5ee572c4a69dde0ff4c53a13cd7
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577667"
---
# <a name="quickstart---configure-a-private-cloud-environment"></a>Snabbstart – konfigurera en privat molnmiljö

I den här artikeln lär du dig hur du skapar ett privat moln CloudSimple och konfigurera din miljö för privata moln.

## <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-private-cloud"></a>Skapa ett privat moln

1. Välj **Alla tjänster**.
2. Sök efter **CloudSimple Services**.
3. Välj tjänsten CloudSimple där du vill skapa ditt privata moln.
4. Översikt över, klickar du på **skapa privata moln** att öppna en ny webbläsarflik för CloudSimple-portalen.  Om du uppmanas logga in med ditt Azure inloggningsuppgifter.  

    ![Skapa privat moln från Azure](media/create-private-cloud-from-azure.png)

5. Ange ett namn för ditt privata moln i CloudSimple-portalen
6. Välj den **plats** för privat moln
7. Välj den **nodtyp** du har köpt på Azure.  Du kan välja den [CS28 eller CS36](cloudsimple-node.md#vmware-solution-by-cloudsimple-nodes-sku). Det senare alternativet innehåller maximal kapacitet för beräkning och minne.
8. Ange den **nodantal**.  Minst tre noder krävs för att skapa ett privat moln

    ![Skapa privat moln – grundläggande information](media/create-private-cloud-basic-info.png)

9. Klicka på **Nästa: Avancerade alternativ**.
10. Ange CIDR-intervall för undernät för virtuella vSphere/SAN-nätverket. Se till att CIDR-intervall inte överlappar något av dina lokala eller andra Azure-undernät.

    ![Skapa privat moln – avancerade alternativ](media/create-private-cloud-advanced-options.png)

11. Välj **Nästa: Granska och skapa**.
12. Granska inställningarna. Om du behöver ändra eventuella inställningar klickar du på **föregående**.
13. Klicka på **Skapa**.

Privat moln etableringsprocessen kommer att startas.  Det kan ta upp till två timmar för det privata molnet som ska etableras.

## <a name="launch-cloudsimple-portal"></a>Starta CloudSimple-portalen

Du kan komma åt CloudSimple portalen från Azure-portalen.  CloudSimple portal kommer att startas med din Azure inloggningsuppgifter med hjälp av enkel inloggning (SSO).  Åtkomst till CloudSimple portal kräver att du ska kunna bevilja den **CloudSimple Service auktorisering** program.  Mer information om hur du beviljar behörigheter finns i [Samtyck till program CloudSimple Service-auktorisering](https://docs.azure.cloudsimple.com/access-cloudsimple-portal/#consent-to-cloudsimple-service-authorization-application)

1. Välj **Alla tjänster**.
2. Sök efter **CloudSimple Services**.
3. Välj tjänsten CloudSimple där du vill skapa ditt privata moln.
4. Översikt över, klickar du på **gå till portalen CloudSimple** att öppna en ny webbläsarflik för CloudSimple-portalen.  Om du uppmanas logga in med ditt Azure inloggningsuppgifter.  

    ![Starta CloudSimple-portalen](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>Skapa punkt-till-plats-VPN

En punkt-till-plats VPN-anslutning är det enklaste sättet att ansluta till ditt privata moln från datorn. Använd punkt-till-plats VPN-anslutning om du ansluter till det privata molnet via en fjärranslutning.  Följ stegen nedan för snabb åtkomst till ditt privata moln.  Åtkomst till CloudSimple region från ditt lokala nätverk kan göras med hjälp av [plats-till-plats VPN](https://docs.azure.cloudsimple.com/vpn-gateway/) eller [Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/).

### <a name="create-gateway"></a>Skapa gateway

1. Starta CloudSimple portalen och välj **nätverk**.
2. Välj **VPN-Gateway**.
3. Klicka på **nya VPN-Gateway**.

    ![Skapa VPN-gateway](media/create-vpn-gateway.png)

4. För **gatewaykonfiguration**, anger du följande inställningar och klickar på **nästa**.

    * Välj **punkt-till-plats VPN** som gateway-typen.
    * Ange ett namn som identifierar gatewayen.
    * Välj den Azure-plats där CloudSimple tjänsten distribueras.
    * Ange klient-undernät för punkt-till-plats-gateway.  När du ansluter får DHCP-adresser från det här undernätet.

5. För **anslutning/användare**, anger du följande inställningar och klickar på **nästa**.

    * Om du vill tillåta automatiskt alla nuvarande och framtida användare tillgång till det privata molnet genom den här punkt-till-plats-gateway, Välj **Lägg automatiskt till alla användare**. När du väljer det här alternativet väljs automatiskt alla användare i användarlistan. Du kan åsidosätta det automatiska alternativet genom att avmarkera enskilda användare i listan.
    * För att välja endast enskilda användare, klickar du på kryssrutorna i användarlistan.

6. Avsnittet VLAN/undernät kan du ange hanterings- och användaren VLAN/undernät för gateway och anslutningar.

    * Den **Lägg automatiskt till** alternativ Ställ in den globala principen för den här gatewayen. Inställningarna tillämpas på den nuvarande gatewayen. Inställningarna kan åsidosättas i den **Välj** området.
    * Välj **lägga till hantering av VLAN/undernät för privata moln**. 
    * Lägg till alla användardefinierade VLAN/undernät, klicka på **lägga till egna VLAN/undernät**. 
    * Den **Välj** inställningar åsidosätter de globala inställningarna under **Lägg automatiskt till**. 

7. Klicka på **nästa** till granskar du inställningarna. Klicka på Redigera ikoner för att göra några ändringar.
8. Klicka på **skapa** att skapa VPN-gatewayen.

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Ansluta till CloudSimple med hjälp av punkt-till-plats-VPN

VPN-klient krävs för att ansluta till CloudSimple från datorn.  Ladda ned [OpenVPN klienten](https://openvpn.net/community-downloads/) för Windows eller [minst](https://www.sparklabs.com/viscosity/download/) för macOS- och OS X.

1. Starta CloudSimple portalen och välj **nätverk**.
2. Välj **VPN-Gateway**.
3. Klicka på punkt-till-plats VPN-gateway från listan över VPN-gatewayer.
4. Välj **användare**.
5. Klicka på **hämta min VPN-konfiguration**

    ![Ladda ned VPN-konfiguration](media/download-p2s-vpn-configuration.png)

6. Importera konfigurationen på VPN-klienten

    * Anvisningar om hur [importerar konfiguration på Windows-klienten](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Anvisningar om hur [importerar konfiguration på macOS- eller OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Ansluta till CloudSimple

## <a name="create-a-vlan-for-your-workload-vms"></a>Skapa ett VLAN för din arbetsbelastning virtuella datorer

När du skapar ett privat moln, skapar du ett VLAN där du ska distribuera din arbetsbelastning eller ditt program virtuella datorer.

1. I portalen CloudSimple väljer **nätverk**.
2. Klicka på **VLAN/undernät**.
3. Klicka på **skapa VLAN/undernät**

    ![Skapa VLAN/undernät](media/create-new-vlan-subnet.png)

4. Välj den **privat moln** för de nya VLAN/undernät.
5. Välj ett VLAN-ID i listan.  
6. Ange ett undernätsnamn som identifierar undernätet.
7. Ange CIDR-intervall för undernät och mask.  Det här intervallet får inte överlappa eventuella befintliga undernät.
8. Klicka på **Skicka**.

    ![Skapa information om VLAN/undernät](media/create-new-vlan-subnet-details.png)

VLAN/undernät kommer att skapas.  Du kan nu använda den här VLAN-ID för att skapa en distribuerad portgrupp på privat moln-vCenter. 

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>Anslut din miljö till en Azure-nätverk

CloudSimple ger dig en ExpressRoute-krets för ditt privata moln. Du kan ansluta ditt virtuella nätverk på Azure till ExpressRoute-kretsen. Fullständig information om hur du konfigurerar anslutningen följer du stegen i [Azure virtuellt nätverksanslutning med ExpressRoute](https://docs.azure.cloudsimple.com/azure-er-connection)

## <a name="sign-in-to-vcenter"></a>Logga in till vCenter

Du kan nu logga in till vCenter att konfigurera virtuella datorer och principer.

1. Starta från CloudSimple-portal för att komma åt vCenter. På startsidan, under **vanliga uppgifter**, klickar du på **starta vSphere-klienten**.  Välj privat moln och klickar sedan på **starta vSphere-klienten** på privat moln.

    ![Starta vSphere-klienten](media/launch-vcenter-from-cloudsimple-portal.png)

2. Välj din önskade vSphere-klienten att komma åt vCenter och logga in med ditt användarnamn och lösenord.  Standardvärdena är:
    * Användarnamn: **CloudOwner@cloudsimple.local**
    * Lösenord: **CloudSimple123!**  

VCenter-skärmar i nästa procedurer är från klienten vSphere (HTML5).

## <a name="change-your-vcenter-password"></a>Ändra lösenordet vCenter

CloudSimple rekommenderar att du ändrar lösenordet första gången du loggar in till vCenter.  
Det lösenord som anges måste uppfylla följande krav:

* Högsta livstid: Lösenord måste ändras varje 365 dagar
* Begränsa återanvändning: Användare kan inte återanvända någon av de tidigare fem lösenord
* Längd: 8 – 20 tecken
* Specialtecken: Minst ett specialtecken
* Alfabetiska tecken: Minst en versal, A – Z och minst en gemen, a – z
* Siffror: Minst ett numeriska tecken, 0-9
* Maximal identiska intilliggande tecken: Tre

    Exempel: Kopia eller kopia är acceptabelt som en del av lösenordet, men CCCC inte.

Om du anger ett lösenord som inte uppfyller kraven:

* Om du använder vSphere-Flash-klienten, rapporterar den ett fel
* Om du använder HTML5-klienten, kan den inte rapporterar ett fel. Klienten Acceptera inte ändringen och det gamla lösenordet fortsätter att fungera.

## <a name="change-nsx-administrator-password"></a>Ändra NSX administratörslösenord

NSX manager distribueras med ett standardlösenord.  Vi rekommenderar att du ändrar lösenordet när du har skapat ditt privata moln.

   * Användarnamn: **admin**
   * Lösenord: **CloudSimple123!**

Du hittar den fullständigt kvalificerade domännamnet (FQDN) och IP-adressen för NSX manager på CloudSimple-portalen.

1. Starta CloudSimple portalen och välj **resurser**.
2. Klicka på privata moln, vilket du vill använda.
3. Välj **vSphere hanteringsnätverket**
4. Använd fullständigt domännamn eller IP-adressen för **NSX Manager** och ansluta med hjälp av en webbläsare. 

    ![Hitta NSX Manager FQDN](media/private-cloud-nsx-manager-fqdn.png)

Om du vill ändra lösenordet, följer du anvisningarna i [NSX Manager-installationen](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.2/com.vmware.nsxt.install.doc/GUID-A65FE3DD-C4F1-47EC-B952-DEDF1A3DD0CF.html).

## <a name="create-a-port-group"></a>Skapa en portgrupp

Skapa en distribuerad portgrupp i vSphere:

1. Följ anvisningarna i ”Lägg till en distribuerad portgrupp” i den [vSphere Nätverksguiden för](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf).
2. När du konfigurerar gruppen distribuerade port, ange VLAN-ID som skapats i [skapa ett VLAN för din arbetsbelastning virtuella datorer](#create-a-vlan-for-your-workload-vms).

## <a name="next-steps"></a>Nästa steg

* [Använda virtuella VMware-datorer på Azure](https://docs.azure.cloudsimple.com/quickstart-create-vmware-virtual-machine)
* [Använda virtuella VMware-datorer på Azure](quickstart-create-vmware-virtual-machine.md)
* [Ansluta till lokala nätverk med Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/)
* [Konfigurera VPN för plats-till-plats från en lokal plats](https://docs.azure.cloudsimple.com/vpn-gateway/)
