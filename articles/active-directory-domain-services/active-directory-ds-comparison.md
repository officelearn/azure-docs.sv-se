---
title: 'Azure AD Domain Services: Jämför Azure AD Domain Services gör det själv domänkontrollanter | Microsoft Docs'
description: Jämföra Azure Active Directory Domain Services för gör det själv-domänkontrollanter
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 165249d5-e0e7-4ed1-aa26-91a05a87bdc9
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/07/2017
ms.author: ergreenl
ms.openlocfilehash: fbd876ad46342dec7e2e73b92d3aafbd37cc205b
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58892304"
---
# <a name="how-to-decide-if-azure-ad-domain-services-is-right-for-your-use-case"></a>Hur du avgör om Azure AD Domain Services som passar ditt användningsområde
Du kan distribuera dina arbetsbelastningar i Azure Infrastructure Services, utan att behöva bekymra dig om hur du underhåller infrastruktur för Identitetshantering i Azure med Azure AD Domain Services. Den här hanterade tjänsten skiljer sig från en typisk distribution för Windows Server Active Directory som du distribuerar och administrera på egen hand. Tjänsten är enkla att distribuera och ger automatiserade hälsoövervakning och reparation. Vi utvecklas ständigt tjänsten för att lägga till stöd för vanliga scenarier för distribution.

Vi rekommenderar följande läsning material för att bestämma om du vill använda Azure AD Domain Services:

* Se en lista över [funktioner som erbjuds av Azure AD Domain Services](active-directory-ds-features.md).
* Granska gemensamma [distributionsscenarier för Azure AD Domain Services](active-directory-ds-scenarios.md).
* Slutligen [jämför Azure AD Domain Services till ett AD-alternativ som gör det själv](active-directory-ds-comparison.md#compare-azure-ad-domain-services-to-diy-ad-domain-in-azure).

## <a name="compare-azure-ad-domain-services-to-diy-ad-domain-in-azure"></a>Jämför Azure AD Domain Services till gör det själv AD-domänen i Azure
I följande tabell kan du välja mellan med hjälp av Azure AD Domain Services och hantera dina egna AD-infrastruktur i Azure.

| **Funktion** | **Azure AD Domain Services** | **”Gör-det-själv” AD i Azure virtuella datorer** |
| --- |:---:|:---:|
| [**Hanterad tjänst**](active-directory-ds-comparison.md#managed-service) |**&#x2713;** |**&#x2715;** |
| [**Säker distribution**](active-directory-ds-comparison.md#secure-deployments) |**&#x2713;** |Administratören måste skydda distributionen. |
| [**DNS-server (Domain Name System)**](active-directory-ds-comparison.md#dns-server) |**&#x2713;**(hanterad tjänst) |**&#x2713;** |
| [**Administratörsbehörighet för domänen eller Enterprise**](active-directory-ds-comparison.md#domain-or-enterprise-administrator-privileges) |**&#x2715;** |**&#x2713;** |
| [**Domänanslutning**](active-directory-ds-comparison.md#domain-join) |**&#x2713;** |**&#x2713;** |
| [**Domain-autentisering med hjälp av NTLM och Kerberos**](active-directory-ds-comparison.md#domain-authentication-using-ntlm-and-kerberos) |**&#x2713;** |**&#x2713;** |
| [**Kerberos-begränsad delegering**](active-directory-ds-comparison.md#kerberos-constrained-delegation)|Resursbaserad|Resursbaserad & baserade|
| [**Anpassade organisationsenhetsstruktur**](active-directory-ds-comparison.md#custom-ou-structure) |**&#x2713;** |**&#x2713;** |
| [**Schematillägg**](active-directory-ds-comparison.md#schema-extensions) |**&#x2715;** |**&#x2713;** |
| [**Litar på AD-domän/skog**](active-directory-ds-comparison.md#ad-domain-or-forest-trusts) |**&#x2715;** |**&#x2713;** |
| [**Läsa LDAP**](active-directory-ds-comparison.md#ldap-read) |**&#x2713;** |**&#x2713;** |
| [**Secure LDAP (LDAPS)**](active-directory-ds-comparison.md#secure-ldap) |**&#x2713;** |**&#x2713;** |
| [**LDAP-write**](active-directory-ds-comparison.md#ldap-write) |**&#x2715;** |**&#x2713;** |
| [**Grupprincip**](active-directory-ds-comparison.md#group-policy) |**&#x2713;** |**&#x2713;** |
| [**Geodistribution**](active-directory-ds-comparison.md#geo-dispersed-deployments) |**&#x2715;** |**&#x2713;** |

#### <a name="managed-service"></a>Hanterad tjänst
Azure AD Domain Services-domäner som hanteras av Microsoft. Du behöver inte bekymra dig om korrigeringar, uppdateringar, övervakning, säkerhetskopiering, och att säkerställa tillgängligheten för din domän. Dessa hanteringsaktiviteter erbjuds som en tjänst av Microsoft Azure för dina hanterade domäner.

#### <a name="secure-deployments"></a>Säker distribution
Den hanterade domänen är säkert låst enligt Microsofts säkerhetsrekommendationer för AD-distributioner. De här rekommendationerna som härrör från AD-produktteamet lång erfarenhet av tekniska och stöd för AD-distributioner. För distributioner som gör det själv, som du behöver och vidta åtgärder för specifik distribution låsa ned/skydda din distribution.

#### <a name="dns-server"></a>DNS-server (Domain Name System)
En Azure AD Domain Services-hanterad domän innehåller hanterade DNS-tjänster. Medlemmar i gruppen ”AAD DC-administratörer” kan hantera DNS på den hanterade domänen. Medlemmar i gruppen ges fullständig DNS-administratörsrättigheter för den hanterade domänen. DNS-hantering kan utföras via ”DNS-administrationskonsolen” ingår i paketet Remote verktyg för fjärrserveradministration (RSAT).
[Mer information](active-directory-ds-admin-guide-administer-dns.md)

#### <a name="domain-or-enterprise-administrator-privileges"></a>Domän- eller företagsadministratör privilegier
Dessa utökade privilegier erbjuds inte på en hanterad AAD-DS-domän. Program som kräver dessa utökade privilegier inte kan distribueras mot AAD DS hanterade domäner. En mindre deluppsättning av administrativa privilegier är tillgängliga för medlemmar i gruppen delegerad administration som kallas ”AAD DC-administratörer”. Dessa privilegier inkludera behörighet för att konfigurera DNS, konfigurera en Grupprincip, få administratörsbehörighet på domänanslutna datorer osv.

#### <a name="domain-join"></a>Domänanslutning
Du kan ansluta till virtuella datorer till den hanterade domänen som liknar hur du ansluter datorer till en AD-domän.

#### <a name="domain-authentication-using-ntlm-and-kerberos"></a>Domain-autentisering med hjälp av NTLM och Kerberos
Du kan använda företagets autentiseringsuppgifter för autentisering med den hanterade domänen med Azure AD Domain Services. Autentiseringsuppgifter hålls synkroniserade med Azure AD-klienten. För synkroniserade klienter säkerställer Azure AD Connect att ändringar av autentiseringsuppgifter som gjorts på lokala synkroniseras till Azure AD. Du behöva konfigurera en AD-domän med en gör det själv domänkonfiguration förtroende med din lokala AD för användare att autentisera med sina företagsuppgifter. Alternativt kan behöva du konfigurera AD-replikering för att se till att användarlösenord synkroniseras till din Azure-domän nätverksstyrenhetens virtuella datorer.

#### <a name="kerberos-constrained-delegation"></a>Kerberos-begränsad delegering
Du har inte ' domän ' administratörsbehörighet på en Active Directory Domain Services-hanterad domän. Därför kan du konfigurera baserade (traditionell) Kerberos-begränsad delegering. Du kan dock konfigurera säkrare Resursbaserad begränsad delegering.
[Mer information](active-directory-ds-enable-kcd.md)

#### <a name="custom-ou-structure"></a>Anpassade organisationsenhetsstruktur
Medlemmar i gruppen AAD DC-administratörer kan skapa anpassade organisationsenheter i den hanterade domänen. Användare som skapar anpassade organisationsenheter beviljas fullständig administratörsbehörighet över Organisationsenheten.
[Mer information](active-directory-ds-admin-guide-create-ou.md)

#### <a name="schema-extensions"></a>Schematillägg
Du kan inte utöka basschemat i en Azure AD Domain Services-hanterad domän. Därför kan inte program som förlitar sig på tillägg till AD-schema (t.ex, nya attribut under användarobjektet) lyfts och växlas till AAD-DS-domäner.

#### <a name="ad-domain-or-forest-trusts"></a>AD-domän eller skogsförtroenden
Hanterade domäner kan inte konfigureras för att ställa in (inkommande/utgående) förtroenderelationer med andra domäner. Distributionsscenarier för resursen skog kan därför kan inte använda Azure AD Domain Services. Distributioner där du inte vill synkronisera lösenord till Azure AD kan inte på samma sätt kan använda Azure AD Domain Services.

#### <a name="ldap-read"></a>LDAP-läsning
Den hanterade domänen stöder LDAP läsa arbetsbelastningar. Därför kan du distribuera program som utför LDAP läsåtgärder mot den hanterade domänen.

#### <a name="secure-ldap"></a>Säkert LDAP
Du kan konfigurera Azure AD Domain Services för att ge åtkomst med säkert LDAP till din hanterade domän, inklusive via internet.
[Mer information](active-directory-ds-admin-guide-configure-secure-ldap.md)

#### <a name="ldap-write"></a>LDAP-Write
Den hanterade domänen är skrivskyddad för användarobjekt. Därför fungerar inte program som utför LDAP skrivåtgärder mot attribut för användarobjektet i en hanterad domän. Dessutom kan användarlösenord inte ändras från inom den hanterade domänen. Ett annat exempel är ändring av gruppmedlemskap eller attributen i den hanterade domänen, vilket inte är tillåtet. Dock ändra på användarattribut eller lösenord som görs i Azure AD (via PowerShell/Azure-portalen) eller en lokal AD synkroniseras till den hanterade AAD-DS-domänen.

#### <a name="group-policy"></a>Grupprincip
Det finns en inbyggd GPO varje för behållarna ”AADDC-datorer” och ”AADDC-användare”. Du kan anpassa dessa inbyggda grupprincipobjekt för att konfigurera en Grupprincip. Medlemmar i gruppen ”AAD DC-administratörer” kan också skapa anpassade grupprincipobjekt och koppla dem till befintliga organisationsenheter (inklusive anpassade organisationsenheter).
[Mer information](active-directory-ds-admin-guide-administer-group-policy.md)

#### <a name="geo-dispersed-deployments"></a>Geografiskt spridda distributioner
Azure AD Domain Services-hanterade domäner är tillgängliga i ett enda virtuellt nätverk i Azure. För scenarier som kräver domänkontrollanter ska vara tillgängliga i flera Azure-regioner över hela världen, kan det vara bättre alternativ att konfigurera domänkontrollanter i Azure IaaS-VM.


## <a name="do-it-yourself-diy-ad-deployment-options"></a>”Gör-det-själv” (DIY) AD-distributionsalternativ
Du kan ha distribution användningsfall där du behöver några av de funktioner som erbjuds av en Windows Server AD-installation. I dessa fall kan du överväga att något av alternativen nedan gör det själv (DIY):

* **Fristående cloud domän:** Du kan ställa in en fristående ”molnet domän' med hjälp av Azure-datorer som har konfigurerats som domänkontrollanter. Den här infrastrukturen är inte integrerat med din lokala AD-miljö. Det här alternativet kräver en annan uppsättning molnautentiseringsuppgifter för att inloggning/administrera virtuella datorer i molnet.
* **Resursdistributionen skog:** Du kan ställa in en domän i skogstopologi resurs med hjälp av Azure virtuella datorer som konfigurerats som domänkontrollanter. Därefter kan du konfigurera ett AD-förtroende med din lokala AD-miljö. Domänanslutning datorer (Azure virtuella datorer) kan du till den här resursskog i molnet. Autentisering av användare som sker med antingen en VPN/ExpressRoute-anslutning till din lokala katalog.
* **Utöka din lokala domän till Azure:** Du kan ansluta Azure-nätverk till ditt lokala nätverk via en VPN/ExpressRoute-anslutning. Den här konfigurationen gör det möjligt för virtuella Azure-datorer är ansluten till din lokala AD. Ett annat alternativ är att höja upp domänkontrollanter för repliken för den lokala domänen i Azure som en virtuell dator. Du kan sedan konfigurera den för att replikera via en VPN/ExpressRoute-anslutning till din lokala katalog. Det här distributionsläget utökar effektivt din lokala domän till Azure.

> [!NOTE]
> Du kan bestämma att ett alternativ som gör det själv passar bättre för din distribution-användningsfall. Överväg att [delning feedback](active-directory-ds-contact-us.md) som hjälper oss att förstå vilka funktioner vill hjälpa du har valt Azure AD Domain Services i framtiden. Denna feedback hjälper oss att utveckla tjänsten så att den bättre passar dina distributionsbehov och användningsfall.
>
>

Vi har publicerat [riktlinjer för att distribuera Windows Server Active Directory på Azure Virtual Machines](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) för att underlätta gör det själv-installationer.

## <a name="related-content"></a>Relaterat innehåll
* [Funktioner – Azure AD Domain Services](active-directory-ds-features.md)
* [-Scenarier för Azure AD Domain Services](active-directory-ds-scenarios.md)
* [Riktlinjer för att distribuera Windows Server Active Directory på virtuella Azure-datorer](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100)
