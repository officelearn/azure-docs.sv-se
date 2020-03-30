---
title: Azure VMware-lösningar från CloudSimple – Secure Private Cloud
description: Beskriver hur du skyddar Azure VMware-lösningar via CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4541874a9e8fc4111e5c65d02f07535c4d14f9f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565986"
---
# <a name="how-to-secure-your-private-cloud-environment"></a>Så här skyddar du din privata molnmiljö

Definiera rollbaserad åtkomstkontroll (RBAC) för CloudSimple Service, CloudSimple-portalen och Private Cloud från Azure.  Användare, grupper och roller för åtkomst till vCenter för privat moln anges med VMware SSO.  

## <a name="rbac-for-cloudsimple-service"></a>RBAC för CloudSimple-tjänst

Skapandet av CloudSimple-tjänsten kräver **ägar-** eller **deltagarroll** på Azure-prenumerationen.  Som standard kan alla ägare och bidragsgivare skapa en CloudSimple-tjänst och komma åt CloudSimple-portalen för att skapa och hantera privata moln.  Endast en CloudSimple-tjänst kan skapas per region.  Om du vill begränsa åtkomsten till specifika administratörer följer du proceduren nedan.

1. Skapa en CloudSimple-tjänst i en ny **resursgrupp** på Azure-portalen
2. Ange RBAC för resursgruppen.
3. Köpa noder och använda samma resursgrupp som CloudSimple-tjänsten

Endast de användare som har **behörighet för ägare** eller **deltagare** i resursgruppen ser CloudSimple-tjänsten och startar CloudSimple-portalen.

Mer information om RBAC finns i [Vad är rollbaserad åtkomstkontroll (RBAC) för Azure-resurser](../role-based-access-control/overview.md).

## <a name="rbac-for-private-cloud-vcenter"></a>RBAC för privat moln vCenter

En standardanvändare `CloudOwner@cloudsimple.local` skapas i vCenter SSO-domänen när ett privat moln skapas.  CloudOwner-användare har behörighet för hantering av vCenter. Ytterligare identitetskällor läggs till i vCenter SSO för att ge åtkomst till olika användare.  Fördefinierade roller och grupper ställs in på det vCenter som kan användas för att lägga till ytterligare användare.

### <a name="add-new-users-to-vcenter"></a>Lägga till nya användare i vCenter

1. [Eskalera privilegier](escalate-private-cloud-privileges.md) för **\@CloudOwner cloudsimple.local-användare** i det privata molnet.
2. Logga in på vCenter med **CloudOwner\@cloudsimple.local**
3. [Lägg till vCenter-användare med enkel inloggning](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html).
4. Lägg till användare i [vCenter-enskilda inloggningsgrupper](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

Mer information om fördefinierade roller och grupper finns i [CloudSimple Private Cloud-behörighetsmodellen för VMware vCenter-artikel.](learn-private-cloud-permissions.md)

### <a name="add-new-identity-sources"></a>Lägga till nya identitetskällor

Du kan lägga till ytterligare identitetsleverantörer för vCenter SSO-domänen i ditt privata moln.  Identitetsleverantörer tillhandahåller autentisering och vCenter SSO-grupper ger behörighet för användare.

* [Använd Active Directory som identitetsprovider](set-vcenter-identity.md) på Private Cloud vCenter.
* [Använda Azure AD som identitetsprovider](azure-ad.md) på Private Cloud vCenter

1. [Eskalera privilegier](escalate-private-cloud-privileges.md) för **\@CloudOwner cloudsimple.local-användare** i det privata molnet.
2. Logga in på vCenter med **CloudOwner\@cloudsimple.local**
3. Lägg till användare från identitetsprovidern [i vCenter-enskilda inloggningsgrupper](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

## <a name="secure-network-on-your-private-cloud-environment"></a>Säkert nätverk i din privata molnmiljö

Den privata molnmiljöns nätverkssäkerhet styrs genom att nätverksåtkomsten skyddas och nätverkstrafiken mellan resurser kontrolleras.

### <a name="access-to-private-cloud-resources"></a>Åtkomst till privata molnresurser

Privat cloud vCenter och resursåtkomst är över en säker nätverksanslutning:

* **[ExpressRoute-anslutning](on-premises-connection.md)**. ExpressRoute tillhandahåller en säker anslutning med låg bandbredd med låg latens från din lokala miljö.  Med hjälp av anslutningen kan dina lokala tjänster, nätverk och användare komma åt ditt privata moln vCenter.
* **[Plats-till-plats VPN-gateway](vpn-gateway.md)**. Site-to-Site VPN ger tillgång till dina privata molnresurser från lokala via en säker tunnel.  Du anger vilka lokala nätverk som kan skicka och ta emot nätverkstrafik till ditt privata moln.
* **[VPN-gateway från punkt till plats](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)**. Använd Punkt-till-plats VPN-anslutning för snabb fjärråtkomst till ditt privata moln vCenter.

### <a name="control-network-traffic-in-private-cloud"></a>Styra nätverkstrafiken i Private Cloud

Brandväggstabeller och regler styr nätverkstrafiken i det privata molnet.  Med brandväggstabellen kan du styra nätverkstrafiken mellan ett källnätverk eller en IP-adress och ett målnätverk eller en IP-adress baserat på kombinationen av regler som definierats i tabellen.

1. Skapa en [brandväggstabell](firewall.md#add-a-new-firewall-table).
2. [Lägg till regler](firewall.md#create-a-firewall-rule) i brandväggstabellen.
3. [Koppla en brandväggstabell till ett VLAN/-undernät](firewall.md#attach-vlans-subnet).
