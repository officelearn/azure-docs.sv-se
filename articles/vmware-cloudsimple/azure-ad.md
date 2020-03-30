---
title: Azure VMware Solution by CloudSimple – Använd Azure AD som identitetskälla i Private Cloud
description: Beskriver hur du lägger till Azure AD som identitetsleverantör i cloudsimple private cloud för att autentisera användare som använder CloudSimple från Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 674ca8bea110d60557d1e50e7b68c9c3f7a92bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564592"
---
# <a name="use-azure-ad-as-an-identity-provider-for-vcenter-on-cloudsimple-private-cloud"></a>Använda Azure AD som identitetsleverantör för vCenter på CloudSimple Private Cloud

Du kan konfigurera ditt CloudSimple Private Cloud vCenter för att autentisera med Azure Active Directory (Azure AD) så att dina VMware-administratörer kan komma åt vCenter. När den enda inloggningsidentitetskällan har konfigurerats kan **molnägarens** användare lägga till användare från identitetskällan till vCenter.  

Du kan konfigurera Active Directory-domän- och domänkontrollanterna på något av följande sätt:

* Active Directory-domän- och domänkontrollanter som körs lokalt
* Active Directory-domän- och domänkontrollanter som körs på Azure som virtuella datorer i din Azure-prenumeration
* Nya Active Directory-domän- och domänkontrollanter som körs i cloudsimple private cloud
* Azure Active Directory-tjänst

I den här guiden beskrivs de uppgifter som krävs för att konfigurera Azure AD som identitetskälla.  Information om hur du använder lokal Active Directory eller Active Directory som körs i Azure finns i [Konfigurera vCenter-identitetskällor som du vill använda Active Directory](set-vcenter-identity.md) för detaljerade instruktioner när du konfigurerar identitetskällan.

## <a name="about-azure-ad"></a>Om Azure AD

Azure AD är Microsofts tjänst för flera innehavare, molnbaserad katalog och identitetshantering.  Azure AD tillhandahåller en skalbar, konsekvent och tillförlitlig autentiseringsmekanism för användare att autentisera och komma åt olika tjänster på Azure.  Det ger också säkra LDAP-tjänster för alla tjänster från tredje part att använda Azure AD som en autentiserings-/identitetskälla.  Azure AD kombinerar grundläggande katalogtjänster, avancerad identitetsstyrning och hantering av programåtkomst, som kan användas för att ge åtkomst till ditt privata moln för användare som administrerar det privata molnet.

Om du vill använda Azure AD som identitetskälla med vCenter måste du konfigurera Azure AD- och Azure AD-domäntjänster. Följ dessa instruktioner:

1. [Konfigurera Azure AD- och Azure AD-domäntjänster](#set-up-azure-ad-and-azure-ad-domain-services)
2. [Konfigurera en identitetskälla på ditt privata moln vCenter](#set-up-an-identity-source-on-your-private-cloud-vcenter)

## <a name="set-up-azure-ad-and-azure-ad-domain-services"></a>Konfigurera Azure AD- och Azure AD-domäntjänster

Innan du börjar behöver du åtkomst till din Azure-prenumeration med globala administratörsbehörighet.  Följande steg innehåller allmänna riktlinjer. Information finns i Azure-dokumentationen.

### <a name="azure-ad"></a>Azure AD

> [!NOTE]
> Om du redan har Azure AD kan du hoppa över det här avsnittet.

1. Konfigurera Azure AD på din prenumeration enligt beskrivningen i [Azure AD-dokumentationen](../active-directory/fundamentals/get-started-azure-ad.md).
2. Aktivera Azure Active Directory Premium på din prenumeration enligt beskrivningen i [Registrera dig för Azure Active Directory Premium](../active-directory/fundamentals/active-directory-get-started-premium.md).
3. Konfigurera ett anpassat domännamn och verifiera det anpassade domännamnet enligt beskrivningen i [Lägg till ett anpassat domännamn i Azure Active Directory](../active-directory/fundamentals/add-custom-domain.md).
    1. Konfigurera en DNS-post på domänregistraren med informationen på Azure.
    2. Ange att det anpassade domännamnet ska vara den primära domänen.

Du kan också konfigurera andra Azure AD-funktioner.  Dessa krävs inte för att aktivera vCenter-autentisering med Azure AD.

### <a name="azure-ad-domain-services"></a>Azure AD-domäntjänster

> [!NOTE]
> Det här är ett viktigt steg för att aktivera Azure AD som identitetskälla för vCenter.  Undvik problem genom att se till att alla steg utförs korrekt.

1. Aktivera Azure AD-domäntjänster enligt beskrivningen i [Aktivera Azure Active Directory-domäntjänster med Azure-portalen](../active-directory-domain-services/active-directory-ds-getting-started.md).
2. Konfigurera nätverket som ska användas av Azure AD-domäntjänster enligt beskrivningen i [Aktivera Azure Active Directory Domain Services med Azure-portalen](../active-directory-domain-services/active-directory-ds-getting-started-network.md).
3. Konfigurera administratörsgrupp för hantering av Azure AD-domäntjänster enligt beskrivningen i [Aktivera Azure Active Directory Domain Services med Azure-portalen](../active-directory-domain-services/active-directory-ds-getting-started-admingroup.md).
4. Uppdatera DNS-inställningar för dina Azure AD-domäntjänster enligt beskrivningen i [Aktivera Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-dns.md).  Om du vill ansluta till AD via Internet konfigurerar du DNS-posten för den offentliga IP-adressen för Azure AD-domäntjänsterna till domännamnet.
5. Aktivera synkronisering av lösenord hash för användare.  Det här steget möjliggör synkronisering av lösenordshöns som krävs för NT LAN Manager (NTLM) och Kerberos-autentisering till Azure AD Domain Services. När du har konfigurerat lösenordshashsynkronisering kan användarna logga in till den hanterade domänen med sina företagsuppgifter. Se [Aktivera synkronisering av lösenordshÃ¤nder till Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md).
    1. Om molnanvändare finns måste de ändra sitt lösenord med hjälp av <a href="http://myapps.microsoft.com/" target="_blank">Azure AD-åtkomstpanelen</a> för att säkerställa att lösenordsh hashar lagras i det format som krävs av NTLM eller Kerberos.  Följ instruktionerna i [Aktivera synkronisering av lösenordshã¤nder till din hanterade domän för molnbaserade användarkonton](../active-directory-domain-services/tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).  Det här steget måste göras för enskilda användare och alla nya användare som skapas i din Azure AD-katalog med azure-portalen eller Azure AD PowerShell-cmdletar. Användare som behöver åtkomst till Azure AD-domäntjänster måste använda <a href="http://myapps.microsoft.com/" target="_blank">Azure AD-åtkomstpanelen</a> och komma åt sin profil för att ändra lösenordet.

        > [!NOTE]
        > Om organisationen endast har molnbaserade användarkonton måste användare som behöver använda Azure Active Directory Domain Services ändra sina lösenord. Ett endast molnbaserat användarkonto är ett konto som skapats i Azure AD-katalogen med antingen Azure Portal eller Azure AD PowerShell-cmdletar. Dessa användarkonton är inte synkroniserade från en lokal katalog.

    2. Om du synkroniserar lösenord från den lokala Active-katalogen följer du stegen i [Active Directory-dokumentationen](../active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md).

6.  Konfigurera säker LDAP på dina Azure Active Directory Domain Services enligt beskrivningen i [Konfigurera säker LDAP (LDAPS) för en hanterad Azure AD-domäntjänst.](../active-directory-domain-services/tutorial-configure-ldaps.md)
    1. Ladda upp ett certifikat för användning av säker LDAP enligt beskrivningen i Azure-avsnittet [hämta ett certifikat för säker LDAP](../active-directory-domain-services/tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap).  CloudSimple rekommenderar att du använder ett signerat certifikat som utfärdats av en certifikatutfärdare för att säkerställa att vCenter kan lita på certifikatet.
    2. Aktivera säker LDAP enligt beskrivningen [Aktivera säker LDAP (LDAPS) för en hanterad Azure AD Domain Services-domän](../active-directory-domain-services/tutorial-configure-ldaps.md).
    3. Spara den offentliga delen av certifikatet (utan den privata nyckeln) i .cer-format för användning med vCenter när du konfigurerar identitetskällan.
    4. Om Internet-åtkomst till Azure AD-domäntjänster krävs aktiverar du alternativet Tillåt säker åtkomst till LDAP via internet.
    5. Lägg till den inkommande säkerhetsregeln för Azure AD Domain services NSG för TCP-port 636.

## <a name="set-up-an-identity-source-on-your-private-cloud-vcenter"></a>Konfigurera en identitetskälla på ditt privata moln vCenter

1. [Eskalera privilegier](escalate-private-cloud-privileges.md) för ditt privata moln vCenter.
2. Samla in de konfigurationsparametrar som krävs för att konfigurera identitetskällan.

    | **Alternativet** | **Beskrivning** |
    |------------|-----------------|
    | **Namn** | Identitetskällans namn. |
    | **Bas DN för användare** | Grundläggande unikt namn för användare.  För Azure AD `OU=AADDC Users,DC=<domain>,DC=<domain suffix>` använder `OU=AADDC Users,DC=cloudsimplecustomer,DC=com`du: Exempel: .|
    | **Domännamn** | FQDN för domänen, till exempel example.com. Ange inte en IP-adress i den här textrutan. |
    | **Domänalias** | *(valfritt)* Domänen NetBIOS namn. Lägg till NetBIOS-namnet på Active Directory-domänen som ett alias för identitetskällan om du använder SSPI-autentiseringar. |
    | **Basera DN för grupper** | Basen framstående namn för grupper. För Azure AD `OU=AADDC Users,DC=<domain>,DC=<domain suffix>` använder du: Exempel:`OU=AADDC Users,DC=cloudsimplecustomer,DC=com`|
    | **Url till primär server** | Primär domänkontrollant LDAP-server för domänen.<br><br>Använd formatet `ldaps://hostname:port`. Porten är vanligtvis 636 för LDAPS-anslutningar. <br><br>Ett certifikat som skapar förtroende för LDAPS-slutpunkten för Active `ldaps://` Directory-servern krävs när du använder den primära eller sekundära LDAP-URL:en. |
    | **Url till sekundär server** | Adress till en sekundär LDAP-server för domänkontrollant som används för redundans. |
    | **Välj certifikat** | Om du vill använda LDAPS med Active Directory LDAP Server eller OpenLDAP Server-identitetskälla visas knappen Välj certifikat när du har angett `ldaps://` textrutan URL. En sekundär URL krävs inte. |
    | **Användarnamn** | ID för en användare i domänen som har ett minimum av skrivskyddad åtkomst till Base DN för användare och grupper. |
    | **Lösenord** | Lösenord för den användare som anges av användarnamn. |

3. Logga in på ditt privata moln vCenter när privilegierna har eskalerats.
4. Följ instruktionerna i [Lägg till en identitetskälla på vCenter](set-vcenter-identity.md#add-an-identity-source-on-vcenter) med hjälp av värdena från föregående steg för att konfigurera Azure Active Directory som identitetskälla.
5. Lägg till användare/grupper från Azure AD i vCenter-grupper enligt beskrivningen i [VMware-avsnittet Lägg till medlemmar i en vCenter Single Sign-On Group](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

> [!CAUTION]
> Nya användare får endast läggas till *i Cloud-Owner-Group,* *Cloud-Global-Cluster-Admin-Group,* *Cloud-Global-Storage-Admin-Group,* *Cloud-Global-Network-Admin-Group* eller *Cloud-Global-VM-Admin-Group*.  Användare som läggs till i gruppen *Administratörer* tas bort automatiskt.  Endast tjänstkonton måste läggas till i gruppen *Administratörer.*

## <a name="next-steps"></a>Nästa steg

* [Läs mer om private cloud-behörighetsmodell](learn-private-cloud-permissions.md)
