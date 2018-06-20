---
title: 'Azure AD Domain Services: Jämföra Azure AD Domain Services till själv domänkontrollanter | Microsoft Docs'
description: Jämföra Azure Active Directory Domain Services till själv domänkontrollanter
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 165249d5-e0e7-4ed1-aa26-91a05a87bdc9
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2017
ms.author: maheshu
ms.openlocfilehash: 550354ceab3026cfd724cd77c3266b22682e4431
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36212579"
---
# <a name="how-to-decide-if-azure-ad-domain-services-is-right-for-your-use-case"></a>Hur du avgör om Azure AD Domain Services som passar dina användningsfall
Du kan distribuera dina arbetsbelastningar i Azure Infrastructure Services utan att behöva bekymra dig om att hantera identitetsinfrastrukturen i Azure med Azure AD Domain Services. Den här hanterade tjänsten skiljer sig från en typisk distribution för Windows Server Active Directory som du kan distribuera och administrera på egen hand. Tjänsten är enkel att distribuera och ger automatisk hälsoövervakning och reparation. Vi utvecklas ständigt tjänsten om du vill lägga till stöd för vanliga scenarier för distribution.

Vi rekommenderar följande läsning material för att bestämma om du vill använda Azure AD Domain Services:

* Visa en lista över [funktioner som erbjuds av Azure AD Domain Services](active-directory-ds-features.md).
* Granska gemensamma [distributionsscenarier för Azure AD Domain Services](active-directory-ds-scenarios.md).
* Slutligen [jämföra Azure AD Domain Services till ett själv AD-alternativ](active-directory-ds-comparison.md#compare-azure-ad-domain-services-to-diy-ad-domain-in-azure).

## <a name="compare-azure-ad-domain-services-to-diy-ad-domain-in-azure"></a>Jämföra Azure AD Domain Services själv AD-domän i Azure
I följande tabell kan du välja mellan att använda Azure AD Domain Services och hantera din egen infrastruktur för AD i Azure.

| **Funktion** | **Azure AD Domain Services** | **'Själv' AD i virtuella Azure-datorer** |
| --- |:---:|:---:|
| [**Hanterad tjänst**](active-directory-ds-comparison.md#managed-service) |**&#x2713;** |**&#x2715;** |
| [**Säker distribution**](active-directory-ds-comparison.md#secure-deployments) |**&#x2713;** |Administratören måste skydda distributionen. |
| [**DNS-server**](active-directory-ds-comparison.md#dns-server) |**&#x2713;**(hanterade service) |**&#x2713;** |
| [**Administratörsbehörighet för domänen eller Enterprise**](active-directory-ds-comparison.md#domain-or-enterprise-administrator-privileges) |**&#x2715;** |**&#x2713;** |
| [**Anslut till domän**](active-directory-ds-comparison.md#domain-join) |**&#x2713;** |**&#x2713;** |
| [**Med hjälp av NTLM och Kerberos-domänautentisering**](active-directory-ds-comparison.md#domain-authentication-using-ntlm-and-kerberos) |**&#x2713;** |**&#x2713;** |
| [**Kerberos-begränsad delegering**](active-directory-ds-comparison.md#kerberos-constrained-delegation)|Resursbaserad|Resursbaserad & konto-baserade|
| [**Anpassade organisationsenhetsstruktur**](active-directory-ds-comparison.md#custom-ou-structure) |**&#x2713;** |**&#x2713;** |
| [**Schemautökningar**](active-directory-ds-comparison.md#schema-extensions) |**&#x2715;** |**&#x2713;** |
| [**AD-domän/skogsförtroenden**](active-directory-ds-comparison.md#ad-domain-or-forest-trusts) |**&#x2715;** |**&#x2713;** |
| [**Läsa LDAP**](active-directory-ds-comparison.md#ldap-read) |**&#x2713;** |**&#x2713;** |
| [**Säkert LDAP (LDAPS)**](active-directory-ds-comparison.md#secure-ldap) |**&#x2713;** |**&#x2713;** |
| [**LDAP-skrivåtgärder**](active-directory-ds-comparison.md#ldap-write) |**&#x2715;** |**&#x2713;** |
| [**Grupprincip**](active-directory-ds-comparison.md#group-policy) |**&#x2713;** |**&#x2713;** |
| [**Fördelade distributioner**](active-directory-ds-comparison.md#geo-dispersed-deployments) |**&#x2715;** |**&#x2713;** |

#### <a name="managed-service"></a>Hanterad tjänst
Azure AD Domain Services-domäner som hanteras av Microsoft. Du behöver inte bry dig om korrigering, uppdateringar, övervakning, säkerhetskopiering, och säkerställa tillgängligheten för din domän. Dessa hanteringsaktiviteter erbjuds som en tjänst av Microsoft Azure för hanterade domäner.

#### <a name="secure-deployments"></a>Säker distribution
Den hanterade domänen har låsts enligt Microsofts säkerhetsrekommendationer för AD-distributioner på ett säkert sätt. De här rekommendationerna härrör från AD-produktteamet åren erfarenhet av tekniker och stöd för AD-distributioner. Du behöver och vidta åtgärder för viss distribution att låsa ned/secure distributionen för själv distributioner.

#### <a name="dns-server"></a>DNS-server
En Azure AD Domain Services-hanterad domän innehåller hanterade DNS-tjänster. Medlemmar i gruppen AAD DC-administratörer kan hantera DNS på den hanterade domänen. Medlemmar i gruppen ges fullständig DNS administratörsbehörighet för den hanterade domänen. DNS-hantering kan utföras via ' DNS-administrationskonsolen' ingår i paketet Remote verktyg för fjärrserveradministration (RSAT).
[Mer information](active-directory-ds-admin-guide-administer-dns.md)

#### <a name="domain-or-enterprise-administrator-privileges"></a>Domän eller en företagsadministratör privilegier
Dessa utökade privilegier erbjuds inte på en hanterad AAD-DS-domän. Program som kräver dessa förhöjd behörighet inte kan distribueras mot AAD DS hanterade domäner. En mindre deluppsättning av administratörsbehörighet är tillgängliga för medlemmar i gruppen delegerad administration kallas AAD DC-administratörer. Dessa behörigheter innefattar behörigheter om du vill konfigurera DNS, konfigurera en Grupprincip, får administratörsbehörighet på domänanslutna datorer osv.

#### <a name="domain-join"></a>Anslut till domän
Du kan ansluta till virtuella datorer i den hanterade domänen som liknar hur du ansluter datorer till en AD-domän.

#### <a name="domain-authentication-using-ntlm-and-kerberos"></a>Med hjälp av NTLM och Kerberos-domänautentisering
Du kan använda företagets inloggningsuppgifter för autentisering med den hanterade domänen med Azure AD Domain Services. Autentiseringsuppgifter hålls synkroniserade med Azure AD-klienten. För synkroniserade klienter säkerställer Azure AD Connect att ändringar av autentiseringsuppgifter som har gjorts lokala synkroniseras till Azure AD. Du behöva konfigurera en AD-domän med en själv domän installationen förtroende med din lokala AD för användare att autentisera med sina företagsuppgifter. Alternativt kan behöva du konfigurera AD-replikering för att se till att synkronisera användarlösenord till din Azure virtuella domänkontroller-datorer.

#### <a name="kerberos-constrained-delegation"></a>Kerberos-begränsad delegering
Du har inte ' domän ' administratörsbehörighet på en hanterad domän i Active Directory Domain Services. Därför kan du konfigurera kontot-baserade (traditionell) Kerberos-begränsad delegering. Du kan dock konfigurera säkrare Resursbaserad begränsad delegering.
[Mer information](active-directory-ds-enable-kcd.md)

#### <a name="custom-ou-structure"></a>Anpassade organisationsenhetsstruktur
Medlemmar i gruppen AAD DC-administratörer kan skapa anpassade organisationsenheter i den hanterade domänen. Användare som skapar anpassade organisationsenheter beviljas fullständig administratörsbehörighet över Organisationsenheten.
[Mer information](active-directory-ds-admin-guide-create-ou.md)

#### <a name="schema-extensions"></a>Schematillägg
Du kan inte utöka grundläggande schemat för en Azure AD Domain Services-hanterad domän. Därför kan program som förlitar sig på tillägg till AD-schema (t.ex, nya attribut under användarobjektet) lyfts och flyttat till AAD-DS-domäner.

#### <a name="ad-domain-or-forest-trusts"></a>AD-domän eller skogsförtroenden
Hanterade domäner kan inte konfigureras för att ställa in (inkommande/utgående) förtroenderelationer med andra domäner. Därför kan använda inte resursen skog distributionsscenarier Azure AD Domain Services. Distributioner där du inte föredrar att synkronisera lösenord till Azure AD kan inte på samma sätt kan använda Azure AD Domain Services.

#### <a name="ldap-read"></a>LDAP-läsning
Den hanterade domänen stöder LDAP läsa arbetsbelastningar. Därför kan du distribuera program som utför LDAP läsåtgärder mot den hanterade domänen.

#### <a name="secure-ldap"></a>Säkert LDAP
Du kan konfigurera Azure AD Domain Services för att tillhandahålla säker LDAP-åtkomst till din hanterade domän, inklusive via internet.
[Mer information](active-directory-ds-admin-guide-configure-secure-ldap.md)

#### <a name="ldap-write"></a>LDAP-skrivåtgärder
Den hanterade domänen är skrivskyddad för användarobjekt. Därför fungerar inte program som utför LDAP-skrivåtgärder mot attribut för användarobjektet i en hanterad domän. Dessutom kan lösenord inte ändras från inom den hanterade domänen. Ett annat exempel är ändring av gruppmedlemskap eller Gruppattribut i den hanterade domänen, vilket inte är tillåtet. Dock ändringar användarattribut eller lösenord som gjorts i Azure AD (via PowerShell/Azure portal) eller lokal AD synkroniseras till den hanterade AAD-DS-domänen.

#### <a name="group-policy"></a>Grupprincip
Det finns en inbyggd GPO varje för behållare ”AADDC datorer” och ”AADDC-användare. Du kan anpassa dessa inbyggda grupprincipobjekt för att konfigurera Grupprincip. Medlemmar i gruppen AAD DC-administratörer kan också skapa anpassade grupprincipobjekt och länka dem till befintliga organisationsenheter (inklusive anpassade organisationsenheter).
[Mer information](active-directory-ds-admin-guide-administer-group-policy.md)

#### <a name="geo-dispersed-deployments"></a>GEO-spridda distributioner
Azure AD Domain Services-hanterade domäner finns i ett enda virtuellt nätverk i Azure. För scenarier som kräver domänkontrollanter ska vara tillgängliga i Azure-regioner över hela världen, kan det vara bättre alternativ att konfigurera domänkontrollanter i Azure IaaS-VM.


## <a name="do-it-yourself-diy-ad-deployment-options"></a>'Själv' (DIY) AD-distributionsalternativ
Du kan ha distribution användningsfall där du behöver några av de funktioner som erbjuds av en Windows Server AD-installation. Överväg att något av följande alternativ för själv (DIY) i dessa fall:

* **Fristående molnet domän:** kan du ställa in en fristående 'molnet domän' med hjälp av Azure virtuella datorer som har konfigurerats som domänkontrollanter. Den här infrastrukturen är inte integrerat med din lokala AD-miljö. Det här alternativet kräver en annan uppsättning autentiseringsuppgifter för molnet inloggning/administrera virtuella datorer i molnet.
* **Distribution i skogar:** du kan konfigurera en domän i skogstopologi resursen med hjälp av Azure virtuella datorer som konfigurerats som domänkontrollanter. Därefter kan du konfigurera ett AD-förtroende med din lokala AD-miljö. Du kan domänanslutning datorer (Azure VM: ar) till den här resursskogen i molnet. Användarautentisering sker via antingen en VPN-/ ExpressRoute-anslutning till din lokala katalog.
* **Utöka din lokal domän till Azure:** du kan ansluta ett virtuellt Azure-nätverk till lokalt nätverk via en VPN-/ ExpressRoute-anslutning. Denna inställning kan virtuella Azure-datorer kan anslutas till din lokala AD. Ett annat alternativ är att höja upp domänkontrollanter för repliken av din lokala domän i Azure som en virtuell dator. Du kan sedan konfigurera den för att replikera via en VPN-/ ExpressRoute-anslutning till din lokala katalog. Det här distributionsläget utökar effektivt lokal domän till Azure.

> [!NOTE]
> Du kan bestämma att ett själv alternativ passar bättre för din distribution användningsfall. Överväg att [delning feedback](active-directory-ds-contact-us.md) som hjälper oss att förstå vad funktioner hjälper du har valt Azure AD Domain Services i framtiden. Denna feedback hjälper oss att utveckla tjänsten så att den passar dina behov och användningsfall.
>
>

Vi har publicerat [riktlinjer för att distribuera Windows Server Active Directory på Azure Virtual Machines](https://msdn.microsoft.com/library/azure/jj156090.aspx) gör det lättare själv installationer.

## <a name="related-content"></a>Relaterat innehåll
* [Funktioner – Azure AD Domain Services](active-directory-ds-features.md)
* [Distributionsscenarier - Azure AD Domain Services](active-directory-ds-scenarios.md)
* [Riktlinjer för att distribuera Windows Server Active Directory på Azure Virtual Machines](https://msdn.microsoft.com/library/azure/jj156090.aspx)
