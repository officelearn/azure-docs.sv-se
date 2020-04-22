---
title: 'Snabbstart: Skapa ett privat moln'
titleSuffix: Azure VMware Solutions by CloudSimple
description: Lär dig hur du skapar och konfigurerar ett privat moln med Azure VMware Solutions by CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 10831c0dc39fa241a43541850afae39593ae033b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678273"
---
# <a name="quickstart---configure-a-private-cloud-environment"></a>Snabbstart - Konfigurera en privat molnmiljö

I den här artikeln får du lära dig hur du skapar ett CloudSimple Private Cloud och konfigurerar din privata molnmiljö.

## <a name="before-you-begin"></a>Innan du börjar

Granska [nätverksförutsättning .](cloudsimple-network-checklist.md)

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-private-cloud"></a>Skapa ett privat moln

Ett privat moln är en isolerad VMware-stack som stöder ESXi-värdar, vCenter, vSAN och NSX.

Privata moln hanteras via CloudSimple-portalen. De har sin egen vCenter-server i sin egen hanteringsdomän. Stacken körs på dedikerade noder och isolerade bare metal-maskinvarunoder.

1. Välj **Alla tjänster**.
2. Sök efter **CloudSimple Services**.
3. Välj den CloudSimple-tjänst som du vill skapa ditt privata moln på.
4. Klicka på **Skapa privat moln i** **översikten**om du vill öppna en ny webbläsarflik för CloudSimple-portalen.  Om du uppmanas till det loggar du in med dina Azure-inloggningsuppgifter.  

    ![Skapa privat moln från Azure](media/create-private-cloud-from-azure.png)

5. Ange ett namn för ditt privata moln i CloudSimple-portalen.
6. Välj **plats** för ditt privata moln.
7. Välj **Nodtyp**, i enlighet med vad du etablerat på Azure.
8. Ange **antal nod**.  Minst tre noder krävs för att skapa ett privat moln.

    ![Skapa privat moln – grundläggande information](media/create-private-cloud-basic-info.png)

9. Klicka på **Nästa: Avancerade alternativ**.
10. Ange CIDR-intervallet för vSphere/vSAN-undernät. Kontrollera att CIDR-intervallet inte överlappar något av dina lokala eller andra Azure-undernät (virtuella nätverk) eller med gateway-undernätet.

    **CIDR-intervallalternativ:** /24, /23, /22 eller /21. Ett /24 CIDR-intervall stöder upp till 26 noder, ett /23 CIDR-intervall stöder upp till 58 noder och ett /22 och /21 CIDR-intervall stöder 64 noder (det maximala antalet noder i ett privat moln).  Mer information och VLAN och undernät finns i [översikt över VLAN och undernät](cloudsimple-vlans-subnets.md).

      > [!IMPORTANT]
      > IP-adresser i vSphere/vSAN CIDR-intervallet är reserverade för användning av infrastrukturen Private Cloud.  Använd inte IP-adressen i det här intervallet på en virtuell dator.

11. Klicka på **Nästa: Granska och skapa**.
12. Granska inställningarna. Om du behöver ändra några inställningar klickar du på **Föregående**.
13. Klicka på **Skapa**.

Etableringsprocessen för privata moln startar.  Det kan ta upp till två timmar innan det privata molnet etableras.

## <a name="launch-cloudsimple-portal"></a>Starta CloudSimple-portalen

Du kan komma åt CloudSimple-portalen från Azure-portalen.  CloudSimple-portalen startas med dina Azure-inloggningsuppgifter med enkel inloggning (SSO).  Åtkomst till CloudSimple-portalen kräver att du godkänner **CloudSimple-tjänstauktoriseringsprogrammet.**  Mer information om hur du beviljar behörigheter finns i [Samtycke till CloudSimple Service Authorization application](access-cloudsimple-portal.md#consent-to-cloudsimple-service-authorization-application).

1. Välj **Alla tjänster**.
2. Sök efter **CloudSimple Services**.
3. Välj den CloudSimple-tjänst som du vill skapa ditt privata moln på.
4. Från översikt klickar du på **Gå till CloudSimple-portalen** för att öppna en ny webbläsarflik för CloudSimple-portalen.  Om du uppmanas till det loggar du in med dina Azure-inloggningsuppgifter.  

    ![Starta CloudSimple-portalen](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>Skapa punkt-till-plats-VPN

En VPN-anslutning mellan punkt och plats är det enklaste sättet att ansluta till ditt privata moln från datorn. Använd Punkt-till-plats VPN-anslutning om du ansluter till det privata molnet på distans.  Följ stegen nedan för snabb åtkomst till ditt privata moln.  Åtkomst till CloudSimple-regionen från ditt lokala nätverk kan göras med hjälp av [Site-to-Site VPN](vpn-gateway.md) eller [Azure ExpressRoute](on-premises-connection.md).

### <a name="create-gateway"></a>Skapa gateway

1. Starta CloudSimple-portalen och välj **Nätverk**.
2. Välj **VPN Gateway**.
3. Klicka på **Ny VPN-gateway**.

    ![Skapa VPN-gateway](media/create-vpn-gateway.png)

4. För **gateway-konfiguration**anger du följande inställningar och klickar på **Nästa**.

    * Välj **Punkt-till-plats-VPN** som gatewaytyp.
    * Ange ett namn för att identifiera gatewayen.
    * Välj den Azure-plats där din CloudSimple-tjänst distribueras.
    * Ange klientundernätet för point-to-site-gatewayen.  DHCP-adresser kommer att ges från det här undernätet när du ansluter.

5. För **Anslutning/Användare**anger du följande inställningar och klickar på **Nästa**.

    * Om du automatiskt vill att alla aktuella och framtida användare ska kunna komma åt det privata molnet via den här point-to-site-gatewayen väljer du **Lägg automatiskt till alla användare**. När du väljer det här alternativet markeras alla användare i listan Användare automatiskt. Du kan åsidosätta det automatiska alternativet genom att avmarkera enskilda användare i listan.
    * Om du bara vill markera enskilda användare klickar du på kryssrutorna i listan Användare.

6. Med avsnittet VLAN/Undernät kan du ange hanterings- och användar-VLAN/undernät för gatewayen och anslutningarna.

    * Alternativen **Lägg till automatiskt** anger den globala principen för den här gatewayen. Inställningarna gäller för den aktuella gatewayen. Inställningarna kan åsidosättas i området **Välj.**
    * Välj **Lägg till hanterings-VLAN/undernät för privata moln**.
    * Om du vill lägga till alla användardefinierade VLAN/-undernät klickar du på **Lägg till användardefinierade VLAN/-undernät**.
    * **Välj-inställningarna** åsidosätter de globala inställningarna under **Lägg automatiskt till**.

7. Klicka på **Nästa** om du vill granska inställningarna. Klicka på redigera ikoner för att göra eventuella ändringar.
8. Klicka på **Skapa** för att skapa VPN-gatewayen.

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Ansluta till CloudSimple med VPN från punkt till plats

VPN-klient behövs för att ansluta till CloudSimple från din dator.  Ladda ned [OpenVPN-klienten](https://openvpn.net/community-downloads/) för Windows eller [Viskositet](https://www.sparklabs.com/viscosity/download/) för macOS och OS X.

1. Starta CloudSimple-portalen och välj **Nätverk**.
2. Välj **VPN Gateway**.
3. Klicka på VPN-gatewayen pekar du på VPN-gatewayen från listan över VPN-gatewayer.
4. Välj **Användare**.
5. Klicka på **Hämta min VPN-konfiguration**.

    ![Ladda ned VPN-konfiguration](media/download-p2s-vpn-configuration.png)

6. Importera konfigurationen på DIN VPN-klient.

    * Instruktioner för [import av konfiguration på Windows-klienten](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Instruktioner för [import av konfiguration på macOS eller OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Anslut till CloudSimple.

## <a name="create-a-vlan-for-your-workload-vms"></a>Skapa ett VLAN för dina virtuella arbetsbelastnings virtuella datorer

När du har skapat ett privat moln skapar du ett VLAN där du ska distribuera virtuella datorer för arbetsbelastning/program.

1. Välj **Nätverk**i CloudSimple-portalen .
2. Klicka på **VLAN/undernät**.
3. Klicka på **Skapa VLAN/Undernät**.

    ![Skapa VLAN/undernät](media/create-new-vlan-subnet.png)

4. Välj det **privata molnet** för det nya VLAN/-undernätet.
5. Välj ett VLAN-ID i listan.  
6. Ange ett undernätsnamn för att identifiera undernätet.
7. Ange CIDR-området och masken för undernätet.  Det här intervallet får inte överlappa med befintliga undernät.
8. Klicka på **Skicka**.

    ![Skapa VLAN/undernätsinformation](media/create-new-vlan-subnet-details.png)

VLAN/-undernätet skapas.  Du kan nu använda det här VLAN-ID:t för att skapa en distribuerad portgrupp på ditt privata moln vCenter.

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>Ansluta din miljö till ett virtuellt Azure-nätverk

CloudSimple ger dig en ExpressRoute-krets för ditt privata moln. Du kan ansluta ditt virtuella nätverk på Azure till ExpressRoute-kretsen. Om du vill ha fullständig information om hur du konfigurerar anslutningen följer du stegen i [Azure Virtual Network Connection med ExpressRoute](https://docs.azure.cloudsimple.com/cloudsimple-azure-network-connection/).

## <a name="sign-in-to-vcenter"></a>Logga in på vCenter

Du kan nu logga in på vCenter för att ställa in virtuella datorer och principer.

1. Om du vill komma åt vCenter startar du från CloudSimple-portalen. Klicka på **Starta vSphere-klient**under **Vanliga uppgifter**på startsidan.  Markera det privata molnet och klicka sedan på **Starta vSphere-klienten** i det privata molnet.

    ![Starta vSphere-klient](media/launch-vcenter-from-cloudsimple-portal.png)

2. Välj önskad vSphere-klient för att komma åt vCenter och logga in med ditt användarnamn och lösenord.  Standardvärdena är:
    * Användarnamn:`CloudOwner@cloudsimple.local`
    * Lösenord: `CloudSimple123!`

VCenter-skärmarna i nästa procedurer kommer från HTML5-klienten (vSphere).

## <a name="change-your-vcenter-password"></a>Ändra ditt vCenter-lösenord

CloudSimple rekommenderar att du ändrar ditt lösenord första gången du loggar in på vCenter.  
Lösenordet du anger måste uppfylla följande krav:

* Maximal livslängd: Lösenordet måste ändras var 365:e dag
* Begränsa återanvändning: Användare kan inte återanvända något av de föregående fem lösenorden
* Längd: 8 - 20 tecken
* Specialtecken: Minst ett specialtecken
* Alfabetiska tecken: Minst ett versaler, A-Ö och minst ett gemener, a-z
* Siffror: Minst ett numeriskt tecken, 0-9
* Maximalt identiska intilliggande tecken: Tre

    Exempel: CC eller CCC är acceptabelt som en del av lösenordet, men CCCC är det inte.

Om du anger ett lösenord som inte uppfyller kraven:

* Om du använder vSphere Flash Client, rapporterar det ett fel
* Om du använder HTML5-klienten rapporterar den inte ett fel. Klienten accepterar inte ändringen och det gamla lösenordet fortsätter att fungera.

## <a name="access-nsx-manager"></a>Få tillgång till NSX-hanterare

NSX-hanteraren distribueras med ett standardlösenord. 

* Användarnamn: **admin**
* Lösenord: **CloudSimple123!**

Du hittar det fullständigt kvalificerade domännamnet (FQDN) och IP-adressen för NSX-chefen på CloudSimple-portalen.

1. Starta CloudSimple-portalen och välj **Resurser**.
2. Klicka på det privata molnet, som du vill använda.
3. Välj **vSphere management network**
4. Använd FQDN eller IP-adressen för **NSX Manager** och anslut med en webbläsare.

    ![Hitta NSX Manager FQDN](media/private-cloud-nsx-manager-fqdn.png)

## <a name="create-a-port-group"></a>Skapa en portgrupp

Så här skapar du en distribuerad portgrupp i vSphere:

1. Följ instruktionerna i "Lägg till en distribuerad portgrupp" i [vSphere Networking Guide](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf).
2. När du konfigurerar den distribuerade portgruppen anger du VLAN-ID:t som skapats i [Skapa en VLAN för virtuella arbetsbelastnings-datorer.](#create-a-vlan-for-your-workload-vms)

## <a name="next-steps"></a>Nästa steg

* [Använda virtuella VMware-datorer på Azure](quickstart-create-vmware-virtual-machine.md)
* [Ansluta till lokalt nätverk med Azure ExpressRoute](on-premises-connection.md)
* [Konfigurera Plats-till-plats-VPN från lokala](vpn-gateway.md)
