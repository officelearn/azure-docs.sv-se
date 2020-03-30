---
title: Azure VMware Solution by CloudSimple – Konfigurera en VPN-gateway
description: Beskriver hur du konfigurerar VPN-gateway för point-to-site och VPN-gateway för plats till plats och skapar anslutningar mellan ditt lokala nätverk och ditt CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a8b7e238333196381524d189904871fe5933c906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279499"
---
# <a name="set-up-vpn-gateways-on-cloudsimple-network"></a>Konfigurera VPN-gateways i CloudSimple-nätverket

Med VPN-gateways kan du fjärransluta till CloudSimple-nätverket från ditt lokala nätverk och från en klientdator på distans. En VPN-anslutning mellan ditt lokala nätverk och ditt CloudSimple-nätverk ger åtkomst till vCenter och arbetsbelastningar i ditt privata moln. CloudSimple stöder både VPN-gateways för plats till plats och VPN från punkt till plats.

## <a name="vpn-gateway-types"></a>TYPER av VPN-gateway

* **Med VPN-anslutning från plats till plats** kan du konfigurera dina arbetsbelastningar i det privata molnet för att komma åt lokala tjänster. Du kan också använda lokal Active Directory som identitetskälla för autentisering till ditt privata moln vCenter.  För närvarande stöds endast **principbaserad VPN-typ.**
* **Vpn-anslutning mellan punkt och plats** är det enklaste sättet att ansluta till ditt privata moln från datorn. Använd punkt-till-plats-VPN-anslutning för att fjärransluta till det privata molnet. Information om hur du installerar en klient för en PUNKT-till-plats-VPN-anslutning finns i [Konfigurera en VPN-anslutning till ditt privata moln](set-up-vpn.md).

I en region kan du skapa en VPN-gateway för punkt till plats och en VPN-gateway från plats till plats.

## <a name="automatic-addition-of-vlansubnets"></a>Automatisk tillsats av VLAN/undernät

CloudSimple VPN-gateways tillhandahåller principer för att lägga till VLAN/undernät till VPN-gateways.  Med principer kan du ange olika regler för hantering av VLAN/undernät och användardefinierade VLAN/undernät.  Regler för hantering VLAN/undernät gäller för alla nya privata moln som du skapar.  Med regler för användardefinierade VLAN/-undernät kan du automatiskt lägga till nya VLAN/-undernät i befintliga eller nya privata moln För en PLATS-till-plats-VPN-gateway definierar du principen för varje anslutning.

Principerna för att lägga till VLAN/undernät i VPN-gateways gäller för både VPN-gateways för plats till plats och punkt till plats.

## <a name="automatic-addition-of-users"></a>Automatiskt tillägg av användare

Med en VPN-gateway för punkt till plats kan du definiera en automatisk tilläggsprincip för nya användare. Som standard har alla ägare och deltagare i prenumerationen åtkomst till CloudSimple-portalen.  Användare skapas endast när CloudSimple-portalen startas för första gången.  Om du **väljer Lägg till** regler automatiskt kan alla nya användare komma åt CloudSimple-nätverket med vpn-anslutning från punkt till plats.

## <a name="set-up-a-site-to-site-vpn-gateway"></a>Konfigurera en VPN-gateway för plats till plats

1. [Öppna CloudSimple-portalen](access-cloudsimple-portal.md) och välj **Nätverk**.
2. Välj **VPN Gateway**.
3. Klicka på **Ny VPN-gateway**.

    ![Skapa VPN-gateway](media/create-vpn-gateway.png)

4. För **gateway-konfiguration**anger du följande inställningar och klickar på **Nästa**.

    * Välj **Plats-till-plats-VPN** som gatewaytyp.
    * Ange ett namn för att identifiera gatewayen.
    * Välj den Azure-plats där din CloudSimple-tjänst distribueras.
    * Aktivera även hög tillgänglighet.

    ![Skapa VPN-gateway för plats till plats](media/create-vpn-gateway-s2s.png)

    > [!WARNING]
    > För att aktivera hög tillgänglighet krävs att din lokala VPN-enhet stöder anslutning till två IP-adresser. Det går inte att inaktivera det här alternativet när VPN-gatewayen har distribuerats.

5. Skapa den första anslutningen från det lokala nätverket och klicka på **Nästa**.

    * Ange ett namn för att identifiera anslutningen.
    * För peer IP anger du den lokala VPN-gatewayens offentliga IP-adress.
    * Ange peer-identifieraren för din lokala VPN-gateway.  Peer-identifieraren är vanligtvis den offentliga IP-adressen för din lokala VPN-gateway.  Om du har konfigurerat en specifik identifierare på gatewayen anger du identifieraren.
    * Kopiera den delade nyckeln som ska användas för anslutning från din lokala VPN-gateway.  Om du vill ändra standarddigt dela nyckel och ange en ny klickar du på redigeringsikonen.
    * För **lokala prefix**anger du de lokala CIDR-prefix som kommer åt CloudSimple-nätverket.  Du kan lägga till flera CIDR-prefix när du skapar anslutningen.

    ![Skapa VPN-gateway-anslutning från plats till plats](media/create-vpn-gateway-s2s-connection.png)

6. Aktivera VLAN/-undernäten i ditt privata molnnätverk som ska nås från det lokala nätverket och klicka på **Nästa**.

    * Om du vill lägga till ett VLAN/-undernät för hantering aktiverar du **Lägg till hanterings-VLAN/Undernät för privata moln**.  Management undernät krävs för vMotion och vSAN undernät.
    * Om du vill lägga till vMotion-undernät aktiverar du **Add vMotion-nätverk av privata moln**.
    * Om du vill lägga till vSAN-undernät aktiverar du **Add vSAN-undernät i Private Clouds**.
    * Välj eller avmarkera specifika VLAN.Select or de-select specific VLAN.

    ![Skapa anslutning](media/create-vpn-gateway-s2s-connection-vlans.png)

7. Granska inställningarna och klicka på **Skicka**.

    ![Granskning och skapa granskning och skapa vpn-gateway på plats till plats](media/create-vpn-gateway-s2s-review.png)

## <a name="create-point-to-site-vpn-gateway"></a>Skapa VPN-gateway för punkt till plats

1. [Öppna CloudSimple-portalen](access-cloudsimple-portal.md) och välj **Nätverk**.
2. Välj **VPN Gateway**.
3. Klicka på **Ny VPN-gateway**.

    ![Skapa VPN-gateway](media/create-vpn-gateway.png)

4. För **gateway-konfiguration**anger du följande inställningar och klickar på **Nästa**.

    * Välj **Punkt-till-plats-VPN** som gatewaytyp.
    * Ange ett namn för att identifiera gatewayen.
    * Välj den Azure-plats där din CloudSimple-tjänst distribueras.
    * Ange klientundernätet för point-to-site-gatewayen.  DHCP-adresser kommer att ges från klientundernätet när du ansluter.

5. För **Anslutning/Användare**anger du följande inställningar och klickar på **Nästa**.

    * Om du automatiskt vill att alla aktuella och framtida användare ska kunna komma åt det privata molnet via point-to-site-gatewayen väljer du **Lägg automatiskt till alla användare**. När du väljer alternativet markeras alla användare i användarlistan automatiskt. Du kan åsidosätta det automatiska alternativet genom att avmarkera enskilda användare i listan.
    * Om du vill markera enskilda användare klickar du på kryssrutorna i användarlistan.

6. Med avsnittet VLAN/Undernät kan du ange hanterings- och användar-VLAN/undernät för gatewayen och anslutningarna.

    * Alternativen **Lägg till automatiskt** anger den globala principen för gatewayen. Inställningarna gäller för den aktuella gatewayen. Inställningarna kan åsidosättas i området **Välj.**
    * Välj **Lägg till hanterings-VLAN/undernät för privata moln**. 
    * Om du vill lägga till alla användardefinierade VLAN/-undernät klickar du på **Lägg till användardefinierade VLAN/-undernät**.
    * **Välj-inställningarna** åsidosätter de globala inställningarna under **Lägg automatiskt till**.

7. Klicka på **Nästa** om du vill granska inställningarna. Klicka på redigera ikoner för att göra eventuella ändringar.
8. Klicka på **Skapa** för att skapa VPN-gatewayen.

### <a name="client-subnet-and-protocols-for-point-to-site-vpn-gateway"></a>Klientundernät och protokoll för VPN-gateway för punkt till plats

VPN-gatewayen för punkt till plats tillåter TCP- och UDP-anslutningar.  Välj det protokoll som ska användas när du ansluter från datorn genom att välja TCP- eller UDP-konfigurationen.

Det konfigurerade klientundernätet används för både TCP- och UDP-klienter.  CIDR-prefixet är uppdelat i två undernät, ett för TCP och ett för UDP-klienter. Välj prefixmasken baserat på antalet VPN-användare som ska ansluta samtidigt.  

I följande tabell visas antalet samtidiga klientanslutningar för prefixmask.

| Prefixmask | /24 | /25 | /26 | /27 | /28 |
|-------------|-----|-----|-----|-----|-----|
| Antal samtidiga TCP-anslutningar | 124 | 60 | 28 | 12 | 4 |
| Antal samtidiga UDP-anslutningar | 124 | 60 | 28 | 12 | 4 |

Om du vill ansluta med Vpn från Punkt till plats läser du [Anslut till CloudSimple med Point-to-Site VPN](set-up-vpn.md#connect-to-cloudsimple-using-point-to-site-vpn).
