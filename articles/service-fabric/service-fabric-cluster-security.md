---
title: Skydda ett Azure Service Fabric-kluster
description: Lär dig mer om säkerhetsscenarier för ett Azure Service Fabric-kluster och de olika tekniker som du kan använda för att implementera dem.
ms.topic: conceptual
ms.date: 08/14/2018
ms.custom: sfrev
ms.openlocfilehash: 92d2c4d03075eaafce039f94b4f03c0791985b40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258686"
---
# <a name="service-fabric-cluster-security-scenarios"></a>Säkerhetsscenarier för service fabric-kluster

Ett Azure Service Fabric-kluster är en resurs som du äger. Det är ditt ansvar att skydda dina kluster för att förhindra att obehöriga användare ansluter till dem. Ett säkert kluster är särskilt viktigt när du kör produktionsarbetsbelastningar i klustret. Det är möjligt att skapa ett osäkert kluster, men om klustret exponerar hanteringsslutpunkter för det offentliga internet kan anonyma användare ansluta till det. Oprioriterade kluster stöds inte för produktionsarbetsbelastningar. 

Den här artikeln är en översikt över säkerhetsscenarier för Azure-kluster och fristående kluster, och de olika tekniker som du kan använda för att implementera dem:

* Nod-till-nod säkerhet
* Säkerhet för klient-till-nod
* Rollbaserad åtkomstkontroll (RBAC)

## <a name="node-to-node-security"></a>Nod-till-nod säkerhet

Nod-till-nod-säkerhet hjälper till att skydda kommunikationen mellan virtuella datorer eller datorer i ett kluster. Det här säkerhetsscenariot säkerställer att endast datorer som har behörighet att ansluta till klustret kan delta i värdprogram och tjänster i klustret.

![Diagram över nod-till-nodkommunikation][Node-to-Node]

Kluster som körs på Azure och fristående kluster som körs i Windows kan båda använda antingen [certifikatsäkerhet](https://msdn.microsoft.com/library/ff649801.aspx) eller [Windows-säkerhet](https://msdn.microsoft.com/library/ff649396.aspx) för Windows Server-datorer.

### <a name="node-to-node-certificate-security"></a>Nod-till-nodcertifikatsäkerhet

Service Fabric använder X.509-servercertifikat som du anger som en del av nodkonfigurationen när du skapar ett kluster. I slutet av den här artikeln kan du se en kort översikt över vad dessa certifikat är och hur du kan hämta eller skapa dem.

Konfigurera certifikatsäkerhet när du skapar klustret, antingen i Azure-portalen, med hjälp av en Azure Resource Manager-mall eller genom att använda en fristående JSON-mall. Service Fabric SDK:s standardbeteende är att distribuera och installera certifikatet längst in i det framtida certifikatet som löper ut. Det klassiska beteendet gjorde det möjligt att definiera primära och sekundära certifikat, tillåta manuellt initierade överrullningar och rekommenderas inte för användning via de nya funktionerna. De primära certifikat som ska användas har längst in i det framtida utgångsdatumet, bör skilja sig från administratörsklienten och skrivskyddade klientcertifikat som du anger för [klient-till-nod-säkerhet](#client-to-node-security).

Mer information om hur du konfigurerar certifikatsäkerhet i ett kluster för Azure finns i [Konfigurera ett kluster med hjälp av en Azure Resource Manager-mall](service-fabric-cluster-creation-via-arm.md).

Mer information om hur du konfigurerar certifikatsäkerhet i ett kluster för ett fristående Windows Server-kluster finns i [Skydda ett fristående kluster i Windows med hjälp av X.509-certifikat](service-fabric-windows-cluster-x509-security.md).

### <a name="node-to-node-windows-security"></a>Nod-till-nod Windows-säkerhet

Mer information om hur du konfigurerar Windows-säkerhet för ett fristående Windows Server-kluster finns i [Skydda ett fristående kluster i Windows med hjälp av Windows-säkerhet](service-fabric-windows-cluster-windows-security.md).

## <a name="client-to-node-security"></a>Säkerhet för klient-till-nod

Klient-till-nod-säkerhet autentiserar klienter och hjälper till att skydda kommunikationen mellan en klient och enskilda noder i klustret. Den här typen av säkerhet hjälper till att säkerställa att endast behöriga användare kan komma åt klustret och de program som distribueras i klustret. Klienter identifieras unikt antingen genom sina Windows-säkerhetsreferenser eller sina certifikatsäkerhetsautentiseringsuppgifter.

![Diagram över kommunikation mellan klient och nod][Client-to-Node]

Kluster som körs på Azure och fristående kluster som körs i Windows kan båda använda antingen [certifikatsäkerhet](https://msdn.microsoft.com/library/ff649801.aspx) eller [Windows-säkerhet](https://msdn.microsoft.com/library/ff649396.aspx).

### <a name="client-to-node-certificate-security"></a>Säkerhet för klient-till-nodcertifikat

Konfigurera certifikatsäkerhet för klient-till-nod när du skapar klustret, antingen i Azure-portalen, med hjälp av en Resource Manager-mall eller genom att använda en fristående JSON-mall. Om du vill skapa certifikatet anger du ett administratörsklientcertifikat eller ett användarklientcertifikat. På bästa sätt bör de administratörsklient- och användarklientcertifikat som du anger skilja sig från de primära och sekundära certifikat som du anger för [nod-till-nodsäkerhet](#node-to-node-security). Klustercertifikat har samma rättigheter som klientadministratörscertifikat. De bör dock endast användas av klustret och inte av administrativa användare som en säkerhetspraxis.

Klienter som ansluter till klustret med hjälp av administratörscertifikatet har fullständig åtkomst till hanteringsfunktioner. Klienter som ansluter till klustret med hjälp av det skrivskyddade användarklientcertifikatet har bara läsbehörighet till hanteringsfunktioner. Dessa certifikat används för RBAC som beskrivs senare i den här artikeln.

Mer information om hur du konfigurerar certifikatsäkerhet i ett kluster för Azure finns i [Konfigurera ett kluster med hjälp av en Azure Resource Manager-mall](service-fabric-cluster-creation-via-arm.md).

Mer information om hur du konfigurerar certifikatsäkerhet i ett kluster för ett fristående Windows Server-kluster finns i [Skydda ett fristående kluster i Windows med hjälp av X.509-certifikat](service-fabric-windows-cluster-x509-security.md).

### <a name="client-to-node-azure-active-directory-security-on-azure"></a>Azure Active Directory-säkerhet för klient till nod på Azure

Med Azure AD kan organisationer (som kallas klientorganisationer) hantera användaråtkomst till program. Program är indelade i program med ett webbaserat inloggningsgränssnitt och de med en inbyggd klientupplevelse. Om du inte redan har skapat en klient kan du börja med att läsa [Så här skaffar du en Azure Active Directory-klientorganisation][active-directory-howto-tenant].

Service Fabric-kluster erbjuder flera startpunkter för dess hanteringsfunktioner, däribland den webbaserade [Service Fabric Explorer][service-fabric-visualizing-your-cluster] och [Visual Studio][service-fabric-manage-application-in-visual-studio]. Därför skapar du två Azure AD-program för att styra åtkomsten till klustret, ett webbprogram och ett inbyggt program.

För kluster som körs på Azure kan du också skydda åtkomsten till hanteringsslutpunkter med hjälp av Azure Active Directory (Azure AD). Mer information om hur du skapar de nödvändiga Azure AD-artefakterna och hur du fyller i dem när du skapar klustret finns i [Konfigurera Azure AD för att autentisera klienter](service-fabric-cluster-creation-setup-aad.md).

## <a name="security-recommendations"></a>Säkerhetsrekommendationer

För Service Fabric-kluster som distribueras i ett offentligt nätverk som hanteras i Azure är rekommendationen för ömsesidig klient-till-nod-autentisering:

* Använd Azure Active Directory för klientidentitet
* Ett certifikat för serveridentitet och SSL-kryptering av http-kommunikation

För Service Fabric-kluster som distribueras i ett offentligt nätverk som finns på Azure är rekommendationen för nod-till-nodsäkerhet att använda ett klustercertifikat för att autentisera noder.

Om du har Windows Server 2012 R2 och Windows Active Directory för fristående Windows Server-kluster rekommenderar vi att du använder Windows-säkerhet med grupphanterade tjänstkonton. Annars använder du Windows-säkerhet med Windows-konton.

## <a name="role-based-access-control-rbac"></a>Rollbaserad åtkomstkontroll (RBAC)

Du kan använda åtkomstkontroll för att begränsa åtkomsten till vissa klusteråtgärder för olika användargrupper. Detta bidrar till att göra klustret säkrare. Två åtkomstkontrolltyper stöds för klienter som ansluter till ett kluster: Administratörsroll och Användarroll.

Användare som har tilldelats administratörsrollen har fullständig åtkomst till hanteringsfunktioner, inklusive läs- och skrivfunktioner. Användare som tilldelas användarrollen har som standard endast läsbehörighet till hanteringsfunktioner (till exempel frågefunktioner). De kan också lösa program och tjänster.

Ange klientrollerna administratör och användare när du skapar klustret. Tilldela roller genom att tillhandahålla separata identiteter (till exempel genom att använda certifikat eller Azure AD) för varje rolltyp. Mer information om standardinställningar för åtkomstkontroll och hur du ändrar standardinställningar finns i [Rollbaserad åtkomstkontroll för service fabric-klienter](service-fabric-cluster-security-roles.md).

## <a name="x509-certificates-and-service-fabric"></a>X.509 certifikat och serviceinfrastruktur

X.509 digitala certifikat används ofta för att autentisera klienter och servrar. De används också för att kryptera och digitalt signera meddelanden. Service Fabric använder X.509-certifikat för att skydda ett kluster och tillhandahålla programsäkerhetsfunktioner. Mer information om digitala X.509-certifikat finns i [Arbeta med certifikat](https://msdn.microsoft.com/library/ms731899.aspx). Du använder [Key Vault](../key-vault/key-vault-overview.md) för att hantera certifikat för Service Fabric-kluster i Azure.

Några viktiga saker att tänka på:

* Om du vill skapa certifikat för kluster som kör produktionsarbetsbelastningar använder du en korrekt konfigurerad Windows Server-certifikattjänst eller en från en godkänd [certifikatutfärdare](https://en.wikipedia.org/wiki/Certificate_authority).
* Använd aldrig tillfälliga certifikat eller testcertifikat som du skapar med hjälp av verktyg som MakeCert.exe i en produktionsmiljö.
* Du kan använda ett självsignerat certifikat, men bara i ett testkluster. Använd inte ett självsignerat certifikat i produktionen.
* När du skapar certifikatets tumavtryck ska du se till att generera ett SHA1-tumavtryck. SHA1 är vad som används när du konfigurerar tumavtrycken för klient- och klustercertifikat.

### <a name="cluster-and-server-certificate-required"></a>Kluster- och servercertifikat (obligatoriskt)

Dessa certifikat (en primär och eventuellt sekundär) krävs för att skydda ett kluster och förhindra obehörig åtkomst till det. Dessa certifikat tillhandahåller kluster- och serverautentisering.

Klusterautentisering autentiserar nod-till-nodkommunikation för klusterfederation. Endast noder som kan bevisa sin identitet med det här certifikatet kan ansluta till klustret. Serverautentisering autentiserar slutpunkterna för klusterhantering till en hanteringsklient, så att hanteringsklienten vet att den talar med det verkliga klustret och inte en "man i mitten". Det här certifikatet tillhandahåller också en SSL för HTTPS-hanterings-API:et och för Service Fabric Explorer via HTTPS. När en klient eller nod autentiserar en nod är en av de första kontrollerna värdet för det gemensamma namnet i fältet **Ämne.** Antingen måste det här gemensamma namnet eller något av certifikatens alternativa namn (SN) finnas i listan över tillåtna vanliga namn.

Intyget skall uppfylla följande krav:

* Certifikatet måste innehålla en privat nyckel. Dessa certifikat har vanligtvis tillägg .pfx eller .pem  
* Certifikatet måste skapas för nyckelutbyte, som kan exporteras till en fil för personligt informationsutbyte (.pfx).
* **Certifikatets ämnesnamn måste matcha den domän som du använder för att komma åt service fabric-klustret**. Den här matchningen krävs för att tillhandahålla en SSL för klustrets HTTPS-hanteringsslutpunkt och Service Fabric Explorer. Du kan inte hämta ett SSL-certifikat från en certifikatutfärdarcertifikat för domänen *.cloudapp.azure.com. Du måste skaffa ett anpassat domännamn för ditt kluster. När du begär ett certifikat från en certifikatutfärdare måste certifikatets ämnesnamn matcha det anpassade domännamn du använder för klustret.

Några andra saker att tänka på:

* Fältet **Ämne** kan ha flera värden. Varje värde föregås av en initiering för att ange värdetypen. Vanligtvis är initieringen **CN** (för *vanligt namn);* till exempel **CN\.= www contoso.com**.
* Fältet **Ämne** kan vara tomt.
* Om det valfria fältet **Ämnesalternativnamn** fylls i måste det ha både certifikatets gemensamma namn och en post per SAN. Dessa anges som **DNS-namnvärden.** Mer information om hur du genererar certifikat som har TEN finns i [Så här lägger du till ett alternativt ämnesnamn i ett säkert LDAP-certifikat](https://support.microsoft.com/kb/931351).
* Värdet i fältet **Avsedda ändamål** i certifikatet bör innehålla ett lämpligt värde, till exempel **serverautentisering** eller **klientautentisering**.

### <a name="application-certificates-optional"></a>Programcertifikat (valfritt)

Valfritt antal ytterligare certifikat kan installeras i ett kluster för programsäkerhetsändamål. Innan du skapar klustret bör du tänka på de programsäkerhetsscenarier som kräver att ett certifikat installeras på noderna, till exempel:

* Kryptering och dekryptering av programkonfigurationsvärden.
* Kryptering av data över noder under replikering.

Konceptet att skapa säkra kluster är detsamma, oavsett om det är Linux- eller Windows-kluster.

### <a name="client-authentication-certificates-optional"></a>Certifikat för klientautentisering (valfritt)

Valfritt antal ytterligare certifikat kan anges för administratörs- eller användarklientåtgärder. Klienten kan använda det här certifikatet när ömsesidig autentisering krävs. Klientcertifikat utfärdas vanligtvis inte av en tredjepartscertifikatutfärdare. I stället innehåller det personliga arkivet för den aktuella användarplatsen vanligtvis klientcertifikat som placerats där av en rotutfärdare. Certifikatet bör ha värdet **för avsedda ändamål i** **klientautentisering**.  

Som standard har klustercertifikatet administratörsklientbehörighet. Dessa ytterligare klientcertifikat bör inte installeras i klustret, men anges som tillåtna i klusterkonfigurationen.  Klientcertifikaten måste dock installeras på klientdatorerna för att ansluta till klustret och utföra alla åtgärder.

> [!NOTE]
> Alla hanteringsåtgärder i ett Service Fabric-kluster kräver servercertifikat. Klientcertifikat kan inte användas för hantering.

## <a name="next-steps"></a>Nästa steg

* [Skapa ett kluster i Azure med hjälp av en Resource Manager-mall](service-fabric-cluster-creation-via-arm.md)
* [Skapa ett kluster med Azure Portal](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
