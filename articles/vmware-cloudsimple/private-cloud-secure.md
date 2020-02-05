---
title: Azure VMware-lösningar (AVS) – säkert AVS-moln
description: Beskriver hur du skyddar Azure VMware-lösningar (AVS) privat moln
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b053f1493c2380153711176a4748ebf90b479a6c
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77020088"
---
# <a name="how-to-secure-your-avs-private-cloud-environment"></a>Skydda din AVS-miljö för privata moln

Definiera rollbaserad åtkomst kontroll (RBAC) för AVS-service, AVS-Portal och AVS-privat moln från Azure. Användare, grupper och roller för åtkomst till vCenter för det privata AVS-molnet anges med VMware SSO. 

## <a name="rbac-for-avs-service"></a>RBAC för AVS-tjänst

För att AVS-tjänsten ska kunna skapas krävs rollen **ägare** eller **deltagare** i Azure-prenumerationen. Som standard kan alla ägare och deltagare skapa en AVS-tjänst och komma åt en AVS-Portal för att skapa och hantera privata moln i molnet. Det går bara att skapa en AVS-tjänst per region. Om du vill begränsa åtkomsten till vissa administratörer följer du stegen nedan.

1. Skapa en AVS-tjänst i en ny **resurs grupp** på Azure Portal
2. Ange RBAC för resurs gruppen.
3. Köpa noder och använda samma resurs grupp som AVS-tjänst

Endast användare som har behörigheten **ägare** eller **deltagare** i resurs gruppen kommer att se AVS-tjänsten och starta AVS-portalen.

Mer information om RBAC finns i [Vad är rollbaserad åtkomst kontroll (RBAC) för Azure-resurser](../role-based-access-control/overview.md).

## <a name="rbac-for-avs-private-cloud-vcenter"></a>RBAC för molnets privata moln vCenter

En standard användare `CloudOwner@AVS.local` skapas i vCenter-domänen för vCenter när ett moln privat moln skapas. CloudOwner-användaren har behörighet att hantera vCenter. Ytterligare identitets källor läggs till i vCenter SSO för att ge åtkomst till olika användare. Fördefinierade roller och grupper konfigureras på vCenter som kan användas för att lägga till ytterligare användare.

### <a name="add-new-users-to-vcenter"></a>Lägg till nya användare i vCenter

1. [Eskalera behörigheter](escalate-private-cloud-privileges.md) för **CloudOwner@AVS.local** användare i det privata AVS-molnet.
2. Logga in på vCenter med **CloudOwner@AVS.local**
3. [Lägg till användare av vCenter enkel inloggning](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html).
4. Lägg till användare i [grupper med enkel inloggning för vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

Mer information om fördefinierade roller och grupper finns i [behörighets modellen för molnets privata moln i VMware vCenter](learn-private-cloud-permissions.md) -artikeln.

### <a name="add-new-identity-sources"></a>Lägg till nya identitets källor

Du kan lägga till ytterligare identitets leverantörer för vCenter SSO-domänen för ditt AVS-privata moln. Identitets leverantörer tillhandahåller autentiserings-och vCenter SSO-grupper som ger auktorisering för användare.

* [Använd Active Directory som identitets leverantör](set-vcenter-identity.md) i molnets privata moln vCenter.
* [Använda Azure AD som identitetsprovider](azure-ad.md) i molnets privata moln vCenter vCenter

1. [Eskalera behörigheter](escalate-private-cloud-privileges.md) för **CloudOwner@AVS.local** användare i det privata AVS-molnet.
2. Logga in på vCenter med **CloudOwner@AVS.local**
3. Lägg till användare från identitets leverantören till [grupper med enkel inloggning för vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

## <a name="secure-network-on-your-avs-private-cloud-environment"></a>Skydda nätverket i din AVS-miljö för privata moln

Molnets privata moln Miljös nätverks säkerhet styrs genom att skydda nätverks åtkomsten och kontrol lera nätverks trafiken mellan resurserna.

### <a name="access-to-avs-private-cloud-resources"></a>Åtkomst till moln resurser för privata moln

Molnets privata moln vCenter-och resurs åtkomst är över en säker nätverks anslutning:

* **[ExpressRoute-anslutning](on-premises-connection.md)** . ExpressRoute tillhandahåller en säker anslutning med hög bandbredd och låg latens från din lokala miljö. Med hjälp av anslutningen kan dina lokala tjänster, nätverk och användare få åtkomst till ditt molns privata moln vCenter.
* **[Plats-till-plats-VPN-gateway](vpn-gateway.md)** . Plats-till-plats-VPN ger åtkomst till dina moln resurser från molnet lokalt via en säker tunnel. Du anger vilka lokala nätverk som kan skicka och ta emot nätverks trafik till ditt moln privata moln.
* **[Punkt-till-plats-VPN-gateway](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)** . Använd punkt-till-plats-VPN-anslutning för snabb fjärråtkomst till ditt molns privata moln vCenter.

### <a name="control-network-traffic-in-avs-private-cloud"></a>Kontrol lera nätverks trafik i ett privat AVS-moln

Brand Väggs tabeller och regler styr nätverks trafiken i det privata AVS-molnet. I brand Väggs tabellen kan du kontrol lera nätverks trafik mellan ett käll nätverk eller en IP-adress och ett mål nätverk eller en IP-adress baserat på den kombination av regler som definierats i tabellen.

1. Skapa en [brand Väggs tabell](firewall.md#add-a-new-firewall-table).
2. [Lägg till regler](firewall.md#create-a-firewall-rule) i brand Väggs tabellen.
3. [Koppla en brand Väggs tabell till ett VLAN/undernät] (brand vägg. MD # Attach-vlanssubnet.
