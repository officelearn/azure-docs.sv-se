---
title: Skydda en Azure Service Fabric-kluster | Microsoft Docs
description: Läs mer om säkerhetsscenarier för ett Azure Service Fabric-kluster och de olika tekniker som du kan använda för att implementera dem.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 26b58724-6a43-4f20-b965-2da3f086cf8a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/14/2018
ms.author: aljo
ms.openlocfilehash: 6a568fa724d0d403833e938ae8b01556fe96cf1f
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56428645"
---
# <a name="service-fabric-cluster-security-scenarios"></a>Säkerhetsscenarier för Service Fabric-kluster
Ett Azure Service Fabric-kluster är en resurs som du äger. Det är ditt ansvar att skydda dina kluster för att förhindra att obehöriga användare från att ansluta till dem. Ett säkert kluster är särskilt viktigt när du kör produktionsarbetsbelastningar i klustret. Även om det är möjligt att skapa ett oskyddat kluster om klustret exponerar hanteringsslutpunkter till det offentliga internet, kan anonyma användare ansluta till den. Oskyddade kluster stöds inte för produktionsarbetsbelastningar. 

Den här artikeln är en översikt över säkerhetsscenarier för Azure-kluster och fristående kluster och de olika tekniker som du kan använda för att implementera dem:

* Nod-till-nod-säkerhet
* Klient-till-nod-säkerhet
* Rollbaserad åtkomstkontroll (RBAC)

## <a name="node-to-node-security"></a>Nod-till-nod-säkerhet
Nod-till-nod security hjälper att säkra kommunikationen mellan virtuella datorer eller datorer i ett kluster. Det här scenariot security säkerställer att endast de datorer som har behörighet att ansluta till klustret kan delta i som är värd för program och tjänster i klustret.

![Diagram över nod till nod-kommunikation][Node-to-Node]

Kluster som körs på Azure och fristående kluster som körs på Windows både kan använda antingen [certifikat security](https://msdn.microsoft.com/library/ff649801.aspx) eller [Windows security](https://msdn.microsoft.com/library/ff649396.aspx) för Windows Server-datorer.

### <a name="node-to-node-certificate-security"></a>För nod-till-nod-Certifikatsäkerhet
Service Fabric använder X.509-servercertifikat som du anger som en del av nodtyp konfigurationen när du skapar ett kluster. Du kan se en kort översikt över dessa certifikat är och hur du kan hämta eller skapa dem i slutet av den här artikeln.

Ställ in Certifikatsäkerhet när du skapar klustret, antingen i Azure-portalen med hjälp av en Azure Resource Manager-mall eller genom att använda en fristående JSON-mall. Standardbeteendet för Service Fabric SDK är att distribuera och installera certifikatet med längst fram i framtida utgångna certifikatet. beteendet klassiska tillåtna definiera primära och sekundära certifikat för att tillåta manuellt initierad överrullningar och rekommenderas inte för användning över de nya funktionerna. De primära certifikat som har längst fram till det utgående datumet i framtiden, ska skilja sig från admin-klienten och skrivskyddade klientcertifikat som du anger för [klient-till-nod-säkerhet](#client-to-node-security).

Läs hur du ställer in Certifikatsäkerhet i ett kluster för Azure i [konfigurera ett kluster med hjälp av en Azure Resource Manager-mall](service-fabric-cluster-creation-via-arm.md).

Läs hur du ställer in Certifikatsäkerhet i ett kluster för ett fristående Windows Server-kluster i [skydda ett fristående kluster i Windows genom att använda X.509-certifikat](service-fabric-windows-cluster-x509-security.md).

### <a name="node-to-node-windows-security"></a>Nod-till-nod Windows-säkerhet
Läs hur du konfigurerar Windows-säkerhet för ett fristående Windows Server-kluster i [skydda ett fristående kluster i Windows med hjälp av Windows security](service-fabric-windows-cluster-windows-security.md).

## <a name="client-to-node-security"></a>Klient-till-nod-säkerhet
Klient-till-nod-säkerhet autentiserar klienter och hjälper till att säker kommunikation mellan en klient och enskilda noder i klustret. Den här typen av security hjälper till att säkerställa att endast behöriga användare har åtkomst till klustret och de program som distribueras i klustret. Klienter kan identifieras unikt genom sina Windows-autentiseringsuppgifter eller deras säkerhetsreferenser för certifikatet.

![Diagram över klient-till-nod-kommunikation][Client-to-Node]

Kluster som körs på Azure och fristående kluster som körs på Windows både kan använda antingen [certifikat security](https://msdn.microsoft.com/library/ff649801.aspx) eller [Windows security](https://msdn.microsoft.com/library/ff649396.aspx).

### <a name="client-to-node-certificate-security"></a>För klient-till-nod-Certifikatsäkerhet
Konfigurera för klient-till-nod-Certifikatsäkerhet när du skapar klustret, antingen i Azure-portalen med hjälp av Resource Manager-mall eller genom att använda en fristående JSON-mall. Ange ett klientcertifikat för administratör eller ett klientcertifikat för att skapa certifikatet. Som bästa praxis, admin klient- och klientcertifikat du anger ska skilja sig från de primära och sekundära certifikat som du anger för [nod till nod-säkerhet](#node-to-node-security). Som standard läggs klustercertifikat för nod-till-nod-säkerhet till listan över tillåtna klienter admin certifikat.

Klienter som ansluter till klustret med hjälp av admin-certifikatet har fullständig åtkomst till funktioner för hantering. Klienter som ansluter till klustret med hjälp av klientcertifikatet skrivskyddad användare har bara läsbehörighet till hanteringsfunktioner. Dessa certifikat används för RBAC som beskrivs senare i den här artikeln.

Läs hur du ställer in Certifikatsäkerhet i ett kluster för Azure i [konfigurera ett kluster med hjälp av en Azure Resource Manager-mall](service-fabric-cluster-creation-via-arm.md).

Läs hur du ställer in Certifikatsäkerhet i ett kluster för ett fristående Windows Server-kluster i [skydda ett fristående kluster i Windows genom att använda X.509-certifikat](service-fabric-windows-cluster-x509-security.md).

### <a name="client-to-node-azure-active-directory-security-on-azure"></a>Klient-till-nod Azure Active Directory-säkerhetsgrupp i Azure
Azure AD kan organisationer (kallas även klienter) för att hantera åtkomst till program. Program är indelade i dem med en webbaserad Användargränssnittet för inloggning och personer med en intern klient-upplevelse. Om du inte redan har skapat en klient som börjar med att läsa [skaffa en Azure Active Directory-klient][active-directory-howto-tenant].

Service Fabric-kluster erbjuder flera startpunkter för dess hanteringsfunktioner, inklusive den webbaserade [Service Fabric Explorer] [ service-fabric-visualizing-your-cluster] och [Visual Studio] [ service-fabric-manage-application-in-visual-studio]. Därför kan skapa du två Azure AD-program för att styra åtkomsten till klustret, ett webbprogram och ett internt program.

För kluster som körs på Azure kan skydda du också åtkomst till hanteringsslutpunkter genom att använda Azure Active Directory (AD Azure). Lär dig hur du skapar de nödvändiga Azure AD-artefakter och hur du kan fylla dem när du skapar klustret, se [ställa in Azure AD för att autentisera klienter](service-fabric-cluster-creation-setup-aad.md).

## <a name="security-recommendations"></a>Säkerhetsrekommendationer
För Service Fabric-kluster som distribueras i ett offentligt nätverk finns i Azure, är rekommendationen för klient-till-nod ömsesidig autentisering:
*   Använd Azure Active Directory för klientens identitet
*   Ett certifikat för serveridentitet och SSL-kryptering av http-kommunikation

Rekommendationen för nod-till-nod-säkerhet är att använda ett klustercertifikat för att autentisera noder för Service Fabric-kluster som distribueras i ett offentligt nätverk finns i Azure. 


Om du har Windows Server 2012 R2 och Windows Active Directory, fristående Windows Server-kluster kan rekommenderar vi att du använder Windows-säkerhet med grupphanterade tjänstkonton. Annars kan du använda Windows-säkerhet med Windows-konton.

## <a name="role-based-access-control-rbac"></a>Rollbaserad åtkomstkontroll (RBAC)
Du kan använda åtkomstkontroll för att begränsa åtkomsten till vissa klusteråtgärder för olika grupper av användare. Detta hjälper att skydda klustret. Två typer av access control har stöd för klienter som ansluter till ett kluster: Administratörsrollen och användarrollen.

Användare som har tilldelats rollen administratör har fullständig åtkomst till funktioner för hantering, inklusive läsa och skriva funktioner. Användare som har tilldelats rollen, som standard har bara läsbehörighet till funktioner för hantering (till exempel frågefunktioner). De kan också lösa program och tjänster.

Ange administratörs- och klienten roller när du skapar klustret. Tilldela roller genom att tillhandahålla separata identiteter (till exempel genom att använda certifikat eller Azure AD) för varje rolltyp. Mer information om standardinställningar för åtkomstkontroll och hur du ändrar standardinställningarna finns i [rollbaserad åtkomstkontroll för Service Fabric-klienter](service-fabric-cluster-security-roles.md).

## <a name="x509-certificates-and-service-fabric"></a>X.509-certifikat och Service Fabric
Digitala X.509-certifikat används ofta att autentisera klienter och servrar. De används också för att kryptera och digitalt signera meddelanden. Service Fabric använder X.509-certifikat för att skydda ett kluster och säkerhetsfunktioner för programmet. Läs mer om X.509 digitala certifikat, [arbeta med certifikat](https://msdn.microsoft.com/library/ms731899.aspx). Du använder [Key Vault](../key-vault/key-vault-overview.md) att hantera certifikat för Service Fabric-kluster i Azure.

Några viktiga saker att tänka på:

* Du kan skapa certifikat för kluster som kör produktionsarbetsbelastningar med en korrekt konfigurerad Windows Server-certifikat eller en från en godkänd [certifikatutfärdare (CA)](https://en.wikipedia.org/wiki/Certificate_authority).
* Aldrig använda någon tillfälliga eller testa certifikat som du skapar med hjälp av verktyg som MakeCert.exe i en produktionsmiljö.
* Du kan använda ett självsignerat certifikat, men endast i ett testkluster. Använd inte ett självsignerat certifikat i produktion.
* När du genererar tumavtrycket för certifikatet, måste du generera ett SHA1-tumavtryck. SHA1 är vad som används när du konfigurerar certifikattumavtryck för klienten och kluster.

### <a name="cluster-and-server-certificate-required"></a>Klustret och server-certifikat (krävs)
Dessa certifikat (en primär och eventuellt en sekundär) krävs för att skydda ett kluster och förhindra obehörig åtkomst till den. Dessa certifikat autentisering med klustret och server.

Klustret autentisering autentiserar kommunikation från nod till nod för klustret federation. Endast de noder som kan bevisa sin identitet med det här certifikatet kan ansluta till klustret. Serverautentisering autentiserar slutpunkterna för klusterhantering i en Hanteringsklient så att hanteringsklienten vet att det pratar med verkliga klustret och inte en ”man in the middle”. Det här certifikatet ger också en SSL för HTTPS-hanterings-API och för Service Fabric Explorer över HTTPS. När en klient eller noden autentiserar en nod, en av de inledande kontrollerna är värdet för eget namn i den **ämne** fält. Den här nätverksnamn eller en av certifikat Alternativt ämnesnamn (SAN) måste finnas i listan över tillåtna namn.

Certifikatet måste uppfylla följande krav:

* Certifikatet måste innehålla en privat nyckel. De här certifikaten har vanligtvis tillägg .pfx eller .pem  
* Certifikatet måste skapas för nyckelutbyte som kan exporteras till en Personal Information Exchange (.pfx)-fil.
* Den **certifikatets ämnesnamn måste matcha den domän som du använder för att få åtkomst till Service Fabric-klustret**. Matchningen krävs för att tillhandahålla en SSL för klustrets HTTPS-slutpunkt för hantering och Service Fabric Explorer. Du kan inte hämta ett SSL-certifikat från en certifikatutfärdare (CA) för den *. cloudapp.azure.com domän. Du måste skaffa ett anpassat domännamn för ditt kluster. När du begär ett certifikat från en certifikatutfärdare måste certifikatets ämnesnamn matcha det anpassade domännamn du använder för klustret.

Några andra saker att tänka på:

* Den **ämne** fält kan ha flera värden. Varje värde är föregås av ett initieringen du ange vilken värdetyp. Initieringen är vanligtvis **CN** (för *nätverksnamn*), till exempel **CN = www.contoso.com**. 
* Den **ämne** fältet kan vara tomt. 
* Om den valfria **Alternativt ämnesnamn** fylls, måste den ha både vanliga namn för certifikatet och en post per SAN. Dessa anges som **DNS-namnet** värden. Läs hur du genererar certifikat som har SAN-nätverk i [lägga till ett alternativt namn i certifikatet för säkert LDAP](https://support.microsoft.com/kb/931351).
* Värdet för den **avsedda syften** fält för certifikatet ska innehålla ett lämpligt värde som **serverautentisering** eller **klientautentisering**.

### <a name="application-certificates-optional"></a>Certifikat för programmet (valfritt)
Valfritt antal ytterligare certifikat kan installeras på ett kluster av säkerhetsskäl för programmet. Tänk på applikationssäkerhets-scenarier som kräver ett certifikat installeras på noderna, till exempel innan du skapar klustret:

* Kryptering och dekryptering av konfigurationsvärden för programmet.
* Kryptering av data över noder under replikeringen.

Skapa säkra kluster är samma, oavsett om de är Linux eller Windows-kluster.

### <a name="client-authentication-certificates-optional"></a>Certifikat för klientautentisering (valfritt)
Valfritt antal ytterligare certifikat kan anges för administratör eller användare Klientåtgärder. Klienten kan använda det här certifikatet när ömsesidig autentisering krävs. Klientcertifikaten utfärdas vanligtvis inte av en tredjeparts Certifikatutfärdare. I stället innehåller det personliga arkivet för den aktuella användare platsen normalt klientcertifikat som placerats där av en rotcertifikatutfärdare. Certifikatet måste ha en **avsedda syften** värdet för **klientautentisering**.  

Klustercertifikatet har administratörsrättigheter för klienten som standard. Dessa ytterligare klientcertifikat bör inte installeras i klustret, men har angetts som tillåten i klusterkonfigurationen.  Klientcertifikat måste vara installerad på klientdatorerna så att ansluta till klustret och utföra åtgärder.

> [!NOTE]
> Alla hanteringsåtgärder på Service Fabric-kluster kräver certifikat. Klientcertifikat kan inte användas för hantering.

## <a name="next-steps"></a>Nästa steg
* [Skapa ett kluster i Azure med hjälp av Resource Manager-mall](service-fabric-cluster-creation-via-arm.md) 
* [Skapa ett kluster med hjälp av Azure portal](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
