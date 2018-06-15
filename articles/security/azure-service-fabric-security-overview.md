---
title: Översikt över säkerheten i Azure service fabric | Microsoft Docs
description: Den här artikeln innehåller en översikt över Azure Service Fabric-säkerhet.
services: security
documentationcenter: na
author: unifycloud
manager: mbaldwin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.openlocfilehash: 19e4e55c4bd63e5d7a9ef6ea3b0bf6ae63f929d5
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895671"
---
# <a name="azure-service-fabric-security-overview"></a>Översikt över säkerheten i Azure Service Fabric
[Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) är en plattform för distribuerade system som gör det enkelt att paketera, distribuera och hantera skalbara och tillförlitliga mikrotjänster. Service Fabric löser betydande problem för utveckling och hantering av molnprogram. Utvecklare och administratörer kan undvika komplexa infrastrukturproblem och fokusera på att implementera verksamhetskritiska, krävande arbetsbelastningar som är skalbara, tillförlitliga och hanterbara.

Den här Azure Service Fabric-säkerhet översiktsartikeln fokuserar på följande områden:

-   Skydda ditt kluster
-   Förstå övervakning och diagnostik
-   Skapa säkrare miljöer med hjälp av certifikat
-   Använda rollbaserad åtkomstkontroll (RBAC)
-   Skydda kluster med hjälp av Windows-säkerhet
-   Konfigurera programsäkerhet i Service Fabric
-   Säker kommunikation för tjänster i Azure Service Fabric 

## <a name="secure-your-cluster"></a>Säkra ditt kluster
Azure Service Fabric samordnar tjänster i ett kluster på datorer. Kluster måste skyddas för att förhindra att obehöriga användare från att ansluta till dem, särskilt när de kör produktionsarbetsbelastningar. Även om det är möjligt att skapa ett oskyddat kluster, kan detta att anonyma användare kan ansluta till den (om det visar hanteringsslutpunkter till internet).

Det här avsnittet innehåller en översikt över säkerhetsscenarier för kluster som kör antingen fristående eller i Azure. Här beskrivs också de olika tekniker som används för att implementera de scenarierna. Säkerhetsscenarier för klustret är:

-   Säkerhet för nod till nod
-   Klient-till-nod-säkerhet

### <a name="node-to-node-security"></a>Säkerhet för nod till nod
Nod till nod säkerhet skyddar kommunikationen mellan virtuella datorer eller datorer i ett kluster. Bara datorer som har behörighet att ansluta till klustret kan delta i värd för program och tjänster i klustret med nod till noden säkerhet.

Kluster som körs på Azure eller fristående kluster som körs på Windows kan använda antingen [certifikat säkerhet](https://msdn.microsoft.com/library/ff649801.aspx) eller [Windows-säkerhet](https://msdn.microsoft.com/library/ff649396.aspx) för Windows Server-datorer.

**Förstå nod till nod Certifikatsäkerhet**

Service Fabric använder X.509-servercertifikat som du anger när du skapar ett kluster. En snabb överblick över dessa certifikat är och hur du kan hämta eller skapa dem finns [arbetar med certifikat](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates).

Du konfigurerar Certifikatsäkerhet när du skapar klustret, antingen via Azure-portalen, Azure Resource Manager-mallar eller en fristående JSON-mall. Du kan ange ett certifikat för primär och en valfri sekundärt certifikat som används för certifikatet överrullningar. De primära och sekundära certifikat som du anger bör vara ett annat än administratörsklient och skrivskyddad klientcertifikat som du anger för [klient-till-nod säkerhet](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).

### <a name="client-to-node-security"></a>Klient-till-nod-säkerhet
Du kan konfigurera säkerhet för klient-till-nod med hjälp av klienten identiteter. För att upprätta förtroende mellan en klient och ett kluster, måste du konfigurera klustret så att du vet vilken klient identiteter som den kan lita på. Detta kan göras på två olika sätt:

-   Ange de gruppanvändare som kan ansluta. 
-   Ange nod domänanvändare som kan ansluta.

Service Fabric stöder två typer av olika åtkomstkontroll för klienter som är anslutna till ett Service Fabric-kluster:

-   Administratör
-   Användare

Med hjälp av åtkomstkontroll kan klusteradministratörer begränsa åtkomsten till vissa typer av klusteråtgärder. Detta gör att klustret är säkrare.

 Administratörer har fullständig åtkomst till funktioner för hantering (inklusive funktioner för läsning och skrivning). Användare, har som standard bara läsbehörighet till funktioner för hantering (till exempel frågefunktioner) och möjligheten att lösa program och tjänster.

**Förstå säkerheten för klient-till-nod-certifikat**

Du kan konfigurera Certifikatsäkerhet för klient-till-nod när du skapar ett kluster via Azure portal, Resource Manager-mallar eller en fristående JSON-mall. Du måste ange ett klientcertifikat för admin och/eller ett klientcertifikat. 

Admin klient- och klientcertifikat som du anger måste skilja sig från de primära och sekundära certifikat som du anger för nod till nod säkerhet.

Klienter som ansluter till klustret med hjälp av admin-certifikatet har fullständig åtkomst till hanteringsmöjligheter. Klienter som ansluter till klustret med hjälp av klientcertifikatet skrivskyddade användaren har läsbehörighet till hanteringsmöjligheter. Med andra ord används dessa certifikat för RBAC.

Information om hur du konfigurerar Certifikatsäkerhet i ett kluster, se [ställer in ett kluster med en Azure Resource Manager-mall](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

**Förstå klient-till-nod Azure Active Directory-säkerhet på Azure**

Kluster som körs på Azure kan också säker åtkomst till management-slutpunkter med hjälp av Azure Active Directory (AD Azure). Information om hur du skapar nödvändiga Azure Active Directory-artefakter, hur du fylla i dem när klustret skapas och hur du ansluter till dessa kluster finns i [ställer in ett kluster med en Azure Resource Manager-mall](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

Azure AD kan organisationer (kallas även klienter) för att hantera användarnas åtkomst till program. Det finns program med en webbaserad inloggning användargränssnitt och program med en native client-upplevelse.

Ett Service Fabric-kluster erbjuder flera startpunkter till dess hanteringsfunktioner, inklusive webbaserade Service Fabric Explorer och Visual Studio. Därför kan du skapa två Azure AD-program för att styra åtkomsten till klustret: ett webbprogram och en programspecifika.

För Azure kluster rekommenderar vi att du använder Azure AD-säkerhetsgrupp för att autentisera klienter och certifikat för nod till nod säkerhet.

För fristående Windows Server-kluster med Windows Server 2012 R2 och Active Directory rekommenderar vi att du använder Windows-säkerhet med hanterade konton.  Annars använder du Windows-säkerhet med Windows-konton.

## <a name="understand-monitoring-and-diagnostics-in-azure-service-fabric"></a>Förstå övervakning och diagnostik i Azure Service Fabric
[Övervaknings- och diagnostikfunktionerna](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) är viktigt att utveckla, testa och distribuera program och tjänster i en miljö. Service Fabric-lösningar som fungerar bäst när du implementerar övervakning och diagnostik för att säkerställa att program och tjänster fungerar som förväntat i en miljö för lokal utveckling eller i produktion.

Från ett säkerhetsperspektiv är de huvudsakliga målen med övervakning och diagnostik

-   Identifiera och diagnostisera problem med maskinvaran och infrastrukturen som kan orsakas av en säkerhetshändelse.
-   Identifiera problem med programvara och appar som kan vara en indikator för kompromettering (IoC).
-   Förstå resursanvändningen för att förhindra oavsiktlig DOS-attacker.

Det totala arbetsflödet för övervakning och diagnostik består av tre steg:

-   **Händelsegenerering:** händelsegenerering innefattar händelser (loggar, spårningar, anpassade händelser) på både infrastruktur (kluster) och program/tjänst-nivå. Läs mer om [infrastrukturnivå händelser](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-infra) och [på programnivå händelser](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) att förstå vad som tillhandahålls och hur du lägger till ytterligare instrumentation.

-   **Händelsen aggregering:** Generated händelser behöver samlas in och sammanställs innan de kan visas. Vi rekommenderar vanligtvis använder [Azure Diagnostics](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) (liknar agent-baserade Logginsamling) eller [EventFlow](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-eventflow) (pågående Logginsamling).

-   **Analys:** händelser måste vara visualiserade och tillgänglig i vissa format för analys och visa. Det finns flera plattformar för analys och visualisering av data för övervakning och diagnostik. De två rekommenderar vi är [logganalys](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-oms) och [Azure Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights) på grund av deras bra integrering med Service Fabric.

Du kan också använda [Azure-Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) att övervaka många av de Azure-resurser som en Service Fabric-klustret har skapats.

En watchdog är en separat tjänst som kan titta på hälsa, läsa in tjänster och rapportera hälsa för något i hierarkin hälsa modellen. Med hjälp av en watchdog kan du förhindra fel som inte skulle kunna identifieras baserat på vyn för en enskild tjänst. 

Watchdogs är också bra att värd-kod som utför vidtar åtgärder utan interaktion från användaren (t.ex, rensa loggfiler i lagring vid vissa intervall). Du kan hitta exempel watchdog service genomförandet på [Azure Service Fabric watchdog exempel](https://azure.microsoft.com/resources/samples/service-fabric-watchdog-service/).

## <a name="understand-how-to-secure-communication-by-using-certificates"></a>Förstå hur för säker kommunikation med hjälp av certifikat
Certifikat för att skydda kommunikationen mellan olika noder för din fristående Windows-kluster. Du kan autentisera klienter som ansluter till det här klustret med hjälp av X.509-certifikat. Detta säkerställer att endast behöriga användare har åtkomst till klustret. Vi rekommenderar att du aktiverar ett certifikat på klustret när du skapar den.

### <a name="x509-certificates-and-service-fabric"></a>X.509-certifikat och Service Fabric
Digitala X.509-certifikat används ofta för att autentisera klienter och servrar. De används också för att kryptera och digitalt signera meddelanden.

I följande tabell visas de certifikat som du behöver på din konfiguration:

|Inställning för certifikat |Beskrivning|
|-------------------------------|-----------|
|ClusterCertificate|    Detta certifikat krävs för att skydda kommunikationen mellan noder i ett kluster. Du kan använda två olika certifikat: ett certifikat för primär och sekundär för uppgradering.|
|ServerCertificate| Det här certifikatet visas till klienten när den försöker ansluta till det här klustret. Du kan använda två olika servercertifikat: ett certifikat för primär och sekundär för uppgradering.|
|ClientCertificateThumbprints|  Det här är en uppsättning certifikat som ska installeras på de autentiserade klienterna.|
|ClientCertificateCommonNames|  Detta är nätverksnamnet för det första klientcertifikatet för CertificateCommonName. CertificateIssuerThumbprint är tumavtrycket för den här certifikatutfärdaren.|
|ReverseProxyCertificate|   Detta är ett valfritt certifikat som kan anges för att skydda din [omvänd proxy](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy).|

Mer information om hur du skyddar certifikat finns [skydda ett fristående kluster på Windows med X.509-certifikat](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security).

## <a name="understand-role-based-access-control"></a>Förstå rollbaserad åtkomstkontroll
Åtkomstkontroll kan Klusteradministratören att begränsa åtkomsten till vissa klusteråtgärder för olika grupper av användare, vilket gör klustringen säkrare. Två olika åtkomstkontroll typer stöds för klienter som ansluter till ett kluster: 

- Administratörsrollen
- användarroll

Administratörer har fullständig åtkomst till funktioner för hantering (inklusive funktioner för läsning och skrivning). Användare, har som standard bara läsbehörighet till funktioner för hantering (till exempel frågefunktioner) och möjligheten att lösa program och tjänster.

Du anger rollerna administratörs- och klienten när klustret har skapats med olika identiteter (inklusive certifikat) för varje. Läs mer om inställningar för åtkomstkontroll standard och hur du ändrar standardinställningarna [rollbaserad åtkomstkontroll för Service Fabric-klienter](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

## <a name="secure-standalone-cluster-by-using-windows-security"></a>Skydda fristående kluster med hjälp av Windows-säkerhet
För att förhindra obehörig åtkomst till ett Service Fabric-kluster, måste du skydda klustret. Säkerhet är särskilt viktigt när klustret körs produktionsarbetsbelastningar. Det beskriver hur du konfigurerar säkerhet nod till nod och klient-till-nod med hjälp av Windows-säkerhet i filen ClusterConfig.JSON.

**Konfigurera Windows-säkerhet med hjälp av gMSA**

När Service Fabric måste köras under gMSA, konfigurerar du nod till nod säkerhet genom att ange [ClustergMSAIdentity](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security). Om du vill skapa betrodda relationer mellan noder, måste de göras medveten om varandra.

Du kan konfigurera säkerhet för klient-till-nod med hjälp av ClientIdentities. Om du vill upprätta förtroende mellan en klient och klustret måste du konfigurera klustret för att identifiera vilka klienten identiteter som den kan lita på.

**Konfigurera Windows-säkerhet med hjälp av en grupp datorer**

Om du vill använda en grupp datorer inom en Active Directory-domän kan du konfigurera säkerhet för nod till nod genom att ange ClusterIdentity. Mer information finns i [skapar en datorgruppen i Active Directory](https://msdn.microsoft.com/library/aa545347).

Du kan konfigurera säkerhet för klient-till-nod med hjälp av ClientIdentities. Om du vill upprätta förtroende mellan en klient och klustret måste du konfigurera klustret för att identifiera klienten identiteter som kan lita på klustret. Du kan upprätta förtroende på två olika sätt:

-   Ange de gruppanvändare som kan ansluta.
-   Ange nod domänanvändare som kan ansluta.

## <a name="configure-application-security-in-service-fabric"></a>Konfigurera programsäkerhet i Service Fabric
### <a name="manage-secrets-in-service-fabric-applications"></a>Hantera hemligheter i Service Fabric-program
Den här metoden kan hantera hemligheter i ett Service Fabric-program. Hemligheter kan vara känslig information, till exempel storage-anslutningssträngar, lösenord eller andra värden som inte ska hanteras i oformaterad text.

Den här metoden använder [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) att hantera nycklar och hemligheter. Med hjälp av hemligheter i ett program är dock plattformsoberoende i molnet. Detta innebär att program kan distribueras till ett kluster som är värd för var som helst. Det finns fyra steg i det här flödet:

-   Skaffa ett certifikat för chiffrering av data.
-   Installera certifikatet på klustret.
-   Kryptera hemliga värden när du distribuerar ett program med certifikatet och mata in dem i en tjänst Settings.xml konfigurationsfilen.
-   Läsa krypterade värden utanför Settings.xml genom att dekryptera dem med samma chiffrering av certifikat.

>[!NOTE]
>Lär dig mer om [hantera hemligheter i Service Fabric program](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="configure-security-policies-for-your-application"></a>Konfigurera säkerhetsprinciper för ditt program
Med hjälp av Azure Service Fabric-säkerhet kan du säkra program som körs i kluster under olika användarkonton. Service Fabric säkerhet hjälper också till att skydda resurser som används av program vid tidpunkten för distribution under användarkonton – till exempel, filer, kataloger och certifikat. Det gör att program som körs, även i en delad värdmiljö säkrare.

Stegen omfattar:

-   Konfigurera principen för en startpunkt för installationen av tjänsten.
-   Starta PowerShell-kommandon från en startpunkt för installationen.
-   Med hjälp av konsolomdirigering för lokala felsökning.
-   Konfigurera en princip för servicepaket kod.
-   Tilldela en säkerhetsprincip åtkomst för HTTP och HTTPS-slutpunkter.

## <a name="secure-communication-for-services-in-azure-service-fabric-security"></a>Säker kommunikation för tjänster i Azure Service Fabric-säkerhet
Säkerhet är en av de viktigaste aspekterna av kommunikation. Application framework Reliable Services innehåller några fördefinierade kommunikation stackar och verktyg som kan användas för att förbättra säkerheten.

-   [Skydda en tjänst när du använder tjänsten fjärrkommunikation](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication)
-   [Skydda en tjänst när du använder en WCF-baserad kommunikation stack](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication#help-secure-a-service-when-youre-using-a-wcf-based-communication-stack)

## <a name="next-steps"></a>Nästa steg
- Konceptuell information om säkerhet i klustret finns [skapa ett Service Fabric-kluster med hjälp av Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) och [Azure-portalen](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal).
- Mer information om Klustersäkerhet i Service Fabric finns [Service Fabric-Klustersäkerhet](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).
