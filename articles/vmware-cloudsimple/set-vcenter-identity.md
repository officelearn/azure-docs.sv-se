---
title: Azure VMware Solution by CloudSimple – Konfigurera vCenter-identitetskällor i Private Cloud
description: Beskriver hur du konfigurerar ditt privata moln vCenter för att autentisera med Active Directory för VMware-administratörer för att komma åt vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5355e43ca6ac075e76a76ceb51be135cf4b62b0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564031"
---
# <a name="set-up-vcenter-identity-sources-to-use-active-directory"></a>Konfigurera vCenter-identitetskällor för att använda Active Directory

## <a name="about-vmware-vcenter-identity-sources"></a>Om VMware vCenter-identitetskällor

VMware vCenter stöder olika identitetskällor för autentisering av användare som har åtkomst till vCenter.  CloudSimple Private Cloud vCenter kan konfigureras för att autentisera med Active Directory för att dina VMware-administratörer ska komma åt vCenter. När installationen är klar kan **molnägaren** användaren lägga till användare från identitetskällan till vCenter.  

Du kan konfigurera Active Directory-domän- och domänkontrollanterna på något av följande sätt:

* Active Directory-domän- och domänkontrollanter som körs lokalt
* Active Directory-domän- och domänkontrollanter som körs på Azure som virtuella datorer i din Azure-prenumeration
* Nya Active Directory-domän- och domänkontrollanter som körs i det privata molnet
* Azure Active Directory-tjänst

I den här guiden beskrivs uppgifterna för att konfigurera Active Directory-domän- och domänkontrollanter som körs antingen lokalt eller som virtuella datorer i dina prenumerationer.  Om du vill använda Azure AD som identitetskälla läser du [Använda Azure AD som identitetsprovider för vCenter på CloudSimple Private Cloud](azure-ad.md) för detaljerade instruktioner när du konfigurerar identitetskällan.

Innan [du lägger till en identitetskälla](#add-an-identity-source-on-vcenter)ska du tillfälligt [eskalera dina vCenter-privilegier](escalate-private-cloud-privileges.md).

> [!CAUTION]
> Nya användare får endast läggas till *i Cloud-Owner-Group,* *Cloud-Global-Cluster-Admin-Group,* *Cloud-Global-Storage-Admin-Group,* *Cloud-Global-Network-Admin-Group* eller *Cloud-Global-VM-Admin-Group*.  Användare som läggs till i gruppen *Administratörer* tas bort automatiskt.  Endast tjänstkonton får läggas till *i grupp-* och tjänstkonton för administratörer får inte användas för att logga in på vSphere-webbgränssnittet.   


## <a name="identity-source-options"></a>Alternativ för identitetskälla

* [Lägga till lokal Active Directory som en enda inloggningsidentitetskälla](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)
* [Konfigurera ny Active Directory i ett privat moln](#set-up-new-active-directory-on-a-private-cloud)
* [Konfigurera Active Directory på Azure](#set-up-active-directory-on-azure)

## <a name="add-on-premises-active-directory-as-a-single-sign-on-identity-source"></a>Lägga till lokal Active Directory som en enda inloggningsidentitetskälla

Om du vill konfigurera den lokala Active Directory som en identitetskälla för enkel inloggning behöver du:

* [Plats-till-plats VPN-anslutning](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) från ditt lokala datacenter till ditt privata moln.
* Lokal DNS-server-IP som lagts till i PSC (VCenter och Platform Services Controller).

Använd informationen i följande tabell när du konfigurerar Active Directory-domänen.

| **Alternativet** | **Beskrivning** |
|------------|-----------------|
| **Namn** | Identitetskällans namn. |
| **Bas DN för användare** | Grundläggande unikt namn för användare. |
| **Domännamn** | FQDN för domänen, till exempel example.com. Ange inte en IP-adress i den här textrutan. |
| **Domänalias** | Domänen NetBIOS namn. Lägg till NetBIOS-namnet på Active Directory-domänen som ett alias för identitetskällan om du använder SSPI-autentiseringar. |
| **Basera DN för grupper** | Basen framstående namn för grupper. |
| **Url till primär server** | Primär domänkontrollant LDAP-server för domänen.<br><br>Använd formatet `ldap://hostname:port`  `ldaps://hostname:port`eller . Porten är vanligtvis 389 för LDAP-anslutningar och 636 för LDAPS-anslutningar. För distributioner med flera domänkontrollanter i Active Directory är porten vanligtvis 3268 för LDAP och 3269 för LDAPS.<br><br>Ett certifikat som skapar förtroende för LDAPS-slutpunkten för Active `ldaps://` Directory-servern krävs när du använder den primära eller sekundära LDAP-URL:en. |
| **Url till sekundär server** | Adress till en sekundär LDAP-server för domänkontrollant som används för redundans. |
| **Välj certifikat** | Om du vill använda LDAPS med Active Directory LDAP Server eller OpenLDAP Server-identitetskälla visas knappen Välj certifikat när du har angett `ldaps://` textrutan URL. En sekundär URL krävs inte. |
| **Användarnamn** | ID för en användare i domänen som har ett minimum av skrivskyddad åtkomst till Base DN för användare och grupper. |
| **Lösenord** | Lösenord för den användare som anges av användarnamn. |

När du har informationen i föregående tabell kan du lägga till din lokala Active Directory som en identitetskälla för enkel inloggning i vCenter.

> [!TIP]
> Du hittar mer information om identitetskällor för enkel inloggning på [sidan VMware-dokumentation](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.psc.doc/GUID-B23B1360-8838-4FF2-B074-71643C4CB040.html).

## <a name="set-up-new-active-directory-on-a-private-cloud"></a>Konfigurera ny Active Directory i ett privat moln

Du kan konfigurera en ny Active Directory-domän i ditt privata moln och använda den som identitetskälla för enkel inloggning.  Active Directory-domänen kan vara en del av en befintlig Active Directory-skog eller kan ställas in som en oberoende skog.

### <a name="new-active-directory-forest-and-domain"></a>Ny Active Directory-skog och -domän

Om du vill konfigurera en ny Active Directory-skog och -domän behöver du:

* En eller flera virtuella datorer som kör Microsoft Windows Server och som ska användas som domänkontrollanter för den nya Active Directory-skogen och -domänen.
* En eller flera virtuella datorer som kör DNS-tjänsten för namnmatchning.

Mer information finns i [Installera en ny Active Directory-skog för Windows Server 2012.](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-forest--level-200-)

> [!TIP]
> För hög tillgänglighet av tjänster rekommenderar vi att du konfigurerar flera domänkontrollanter och DNS-servrar.

När du har konfigurerat Active Directory-skogen och active directory-domänen kan du [lägga till en identitetskälla på vCenter](#add-an-identity-source-on-vcenter) för den nya Active Directory.

### <a name="new-active-directory-domain-in-an-existing-active-directory-forest"></a>Ny Active Directory-domän i en befintlig Active Directory-skog

Om du vill konfigurera en ny Active Directory-domän i en befintlig Active Directory-skog behöver du:

* Plats-till-plats VPN-anslutning till din Active Directory-skogsplats.
* DNS-servern för att matcha namnet på din befintliga Active Directory-skog.

Mer information finns i [Installera en ny underordnad Windows Server 2012 Active Directory- eller träddomän.](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-child-or-tree-domain--level-200-)

När du har konfigurerat Active Directory-domänen kan du [lägga till en identitetskälla på vCenter](#add-an-identity-source-on-vcenter) för den nya Active Directory.

## <a name="set-up-active-directory-on-azure"></a>Konfigurera Active Directory på Azure

Active Directory som körs på Azure liknar Active Directory som körs lokalt.  Om du vill konfigurera Active Directory som körs på Azure som en enda inloggningsidentitetskälla på vCenter måste vCenter-servern och PSC ha nätverksanslutning till Det virtuella Azure-nätverket där Active Directory-tjänster körs.  Du kan upprätta den här anslutningen med [Azure Virtual Network Connection med ExpressRoute](azure-expressroute-connection.md) från det virtuella Azure-nätverket där Active Directory Services körs till CloudSimple Private Cloud.

När nätverksanslutningen har upprättats följer du stegen i [Lägg till lokal Active Directory som en identitetskälla](#add-on-premises-active-directory-as-a-single-sign-on-identity-source) för enkel inloggning för att lägga till den som en identitetskälla.  

## <a name="add-an-identity-source-on-vcenter"></a>Lägga till en identitetskälla i vCenter

1. [Eskalera privilegier](escalate-private-cloud-privileges.md) i ditt privata moln.

2. Logga in på vCenter för ditt privata moln.

3. Välj **> administration**.

    ![Administration](media/OnPremAD01.png)

4. Välj **enkel inloggning > konfiguration**.

    ![Enkel inloggning](media/OnPremAD02.png)

5. Öppna fliken **Identitetskällor** och klicka för **+** att lägga till en ny identitetskälla.

    ![Identitetskällor](media/OnPremAD03.png)

6. Välj **Active Directory som LDAP-server** och klicka på **Nästa**.

    ![Active Directory](media/OnPremAD04.png)

7. Ange identitetskällans parametrar för din miljö och klicka på **Nästa**.

    ![Active Directory](media/OnPremAD05.png)

8. Granska inställningarna och klicka på **Slutför**.
