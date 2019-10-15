---
title: Azure VMware-lösning av CloudSimple-säkert privat moln
description: Beskriver hur du skyddar Azure VMware-lösningen via CloudSimple privat moln
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c9d3b2858ea3d80836b280b795025f2ce2eb85c7
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311768"
---
# <a name="how-to-secure-your-private-cloud-environment"></a>Skydda din privata moln miljö

Definiera rollbaserad åtkomst kontroll (RBAC) för CloudSimple-tjänsten, CloudSimple-portalen och det privata molnet från Azure.  Användare, grupper och roller för åtkomst till vCenter för privat moln anges med VMware SSO.  

## <a name="rbac-for-cloudsimple-service"></a>RBAC för CloudSimple-tjänst

Skapandet av CloudSimple-tjänsten kräver rollen **ägare** eller **deltagare** i Azure-prenumerationen.  Som standard kan alla ägare och deltagare skapa en CloudSimple-tjänst och få åtkomst till CloudSimple-portalen för att skapa och hantera privata moln.  Det går bara att skapa en CloudSimple-tjänst per region.  Om du vill begränsa åtkomsten till vissa administratörer följer du stegen nedan.

1. Skapa en CloudSimple-tjänst i en ny **resurs grupp** på Azure Portal
2. Ange RBAC för resurs gruppen.
3. Köpa noder och använda samma resurs grupp som CloudSimple-tjänsten

Endast användare som har behörighet som **ägare** eller **deltagare** i resurs gruppen kommer att se CloudSimple-tjänsten och starta CloudSimple-portalen.

Mer information om RBAC finns i [Vad är rollbaserad åtkomst kontroll (RBAC) för Azure-resurser](../role-based-access-control/overview.md).

## <a name="rbac-for-private-cloud-vcenter"></a>RBAC för privat moln vCenter

En standard användare `CloudOwner@cloudsimple.local` skapas i vCenter SSO-domänen när ett privat moln skapas.  CloudOwner-användaren har behörighet att hantera vCenter. Ytterligare identitets källor läggs till i vCenter SSO för att ge åtkomst till olika användare.  Fördefinierade roller och grupper konfigureras på vCenter som kan användas för att lägga till ytterligare användare.

### <a name="add-new-users-to-vcenter"></a>Lägg till nya användare i vCenter

1. [Eskalera privilegier](escalate-private-cloud-privileges.md) för **CloudOwner@cloudsimple.local-** användare i det privata molnet.
2. Logga in på vCenter med **CloudOwner@cloudsimple.local**
3. [Lägg till användare av vCenter enkel inloggning](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html).
4. Lägg till användare i [grupper med enkel inloggning för vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

Mer information om fördefinierade roller och grupper finns i [CloudSimple Private Cloud permission Model of VMware vCenter](learn-private-cloud-permissions.md) artikeln.

### <a name="add-new-identity-sources"></a>Lägg till nya identitets källor

Du kan lägga till ytterligare identitets leverantörer för vCenter SSO-domänen för ditt privata moln.  Identitets leverantörer tillhandahåller autentiserings-och vCenter SSO-grupper som ger auktorisering för användare.

* [Använd Active Directory som identitets leverantör](set-vcenter-identity.md) i privat moln vCenter.
* [Använda Azure AD som identitets leverantör](azure-ad.md) i privat moln vCenter

1. [Eskalera privilegier](escalate-private-cloud-privileges.md) för **CloudOwner@cloudsimple.local-** användare i det privata molnet.
2. Logga in på vCenter med **CloudOwner@cloudsimple.local**
3. Lägg till användare från identitets leverantören till [grupper med enkel inloggning för vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

## <a name="secure-network-on-your-private-cloud-environment"></a>Skydda nätverket i din privata moln miljö

Den privata moln miljöns nätverks säkerhet styrs genom att säkra nätverks åtkomsten och kontrol lera nätverks trafiken mellan resurserna.

### <a name="access-to-private-cloud-resources"></a>Åtkomst till privata moln resurser

Åtkomst till det privata molnet vCenter och resurser är över en säker nätverks anslutning:

* **[ExpressRoute-anslutning](on-premises-connection.md)** . ExpressRoute tillhandahåller en säker anslutning med hög bandbredd och låg latens från din lokala miljö.  Med hjälp av anslutningen kan dina lokala tjänster, nätverk och användare få åtkomst till ditt privata moln vCenter.
* **[Plats-till-plats-VPN-gateway](vpn-gateway.md)** . Plats-till-plats-VPN ger åtkomst till dina privata moln resurser från lokala platser via en säker tunnel.  Du anger vilka lokala nätverk som kan skicka och ta emot nätverks trafik till ditt privata moln.
* **[Punkt-till-plats-VPN-gateway](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)** . Använd punkt-till-plats-VPN-anslutning för snabb fjärråtkomst till ditt privata moln vCenter.

### <a name="control-network-traffic-in-private-cloud"></a>Kontrol lera nätverks trafik i privat moln

Brand Väggs tabeller och regler styr nätverks trafiken i det privata molnet.  I brand Väggs tabellen kan du kontrol lera nätverks trafik mellan ett käll nätverk eller en IP-adress och ett mål nätverk eller en IP-adress baserat på den kombination av regler som definierats i tabellen.

1. Skapa en [brand Väggs tabell](firewall.md#add-a-new-firewall-table).
2. [Lägg till regler](firewall.md#create-a-firewall-rule) i brand Väggs tabellen.
3. [Koppla en brand Väggs tabell till ett VLAN/undernät] (brand vägg. MD # Attach-vlanssubnet.
