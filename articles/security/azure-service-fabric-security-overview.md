---
title: Säkerhetsöversikt för Azure Service Fabric | Microsoft Docs
description: Den här artikeln innehåller en översikt över Azure Service Fabric-säkerhet.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.openlocfilehash: c5b5f80a43530fe6d0b90e65c3aef89a815157e4
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2019
ms.locfileid: "57241399"
---
# <a name="azure-service-fabric-security-overview"></a>Säkerhetsöversikt för Azure Service Fabric
[Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) är en distribuerad systemplattform som gör det enkelt att paketera, distribuera och hantera skalbara och tillförlitliga mikrotjänster. Service Fabric hanterar utmaningarna i att utveckla och hantera molnprogram. Utvecklare och administratörer kan undvika komplicerade infrastrukturproblem och fokusera på att implementera verksamhetskritiska och krävande arbetsbelastningar som är skalbara och tillförlitliga.

Den här artikeln är en översikt över säkerhetsaspekter för en Service Fabric-distribution.

## <a name="secure-your-cluster"></a>Säkra ditt kluster
Azure Service Fabric dirigerar tjänster i ett kluster med datorer. Kluster måste skyddas för att förhindra att obehöriga användare från att ansluta till dem, särskilt när de kör produktionsarbetsbelastningar. Även om det är möjligt att skapa ett oskyddat kluster, kan det att anonyma användare att ansluta till klustret (om den exponerar hanteringsslutpunkter till det offentliga internet).

För kluster som kör antingen fristående eller i Azure finns två scenarier för att tänka på nod till nod-säkerhet och klient-till-nod-säkerhet. Du kan använda olika tekniker för att implementera dessa scenarier.

### <a name="node-to-node-security"></a>Nod-till-nod-säkerhet
Nod-till-nod-säkerhet gäller för kommunikation mellan virtuella datorer eller datorer i ett kluster. Med nod-till-nod-säkerhet, kan endast datorer som har behörighet att ansluta till klustret delta i som är värd för program och tjänster i klustret.

Kluster som körs på Azure eller fristående kluster som körs på Windows kan använda antingen [certifikat security](https://msdn.microsoft.com/library/ff649801.aspx) eller [Windows security](https://msdn.microsoft.com/library/ff649396.aspx) för Windows Server-datorer.

#### <a name="node-to-node-certificate-security"></a>För nod-till-nod-Certifikatsäkerhet

Service Fabric använder X.509-servercertifikat som du anger när du skapar ett kluster. En snabb överblick över vad de här certifikaten är och hur du kan hämta eller skapa dem, se [arbeta med certifikat](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates).

Du kan konfigurera Certifikatsäkerhet när du skapar kluster via Azure portal, Azure Resource Manager-mallar eller en fristående JSON-mall. Du kan ange ett certifikat för primär och en valfri sekundärt certifikat som används för certifikatet överrullningar. De primära och sekundära certifikat som du anger ska skilja sig från admin-klienten och skrivskyddade klientcertifikat som du anger för [klient-till-nod-säkerhet](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).

### <a name="client-to-node-security"></a>Klient-till-nod-säkerhet
Du konfigurerar klient-till-nod-säkerhet med hjälp av identiteter för klienten. För att upprätta förtroende mellan en klient och ett kluster, måste du konfigurera klustret om du vill veta vilken klient identiteter som den kan lita på.

Service Fabric stöder två typer av åtkomstkontroll för klienter som är anslutna till ett Service Fabric-kluster:

-   **Administratör**: Fullständig åtkomst till funktioner för hantering, inklusive Läs-och skrivbehörighet.
-   **Användaren**: Endast läsbehörighet till funktioner för hantering (till exempel frågefunktioner) och möjligheten att lösa program och tjänster.

Klusteradministratörer kan begränsa åtkomsten till vissa typer av klusteråtgärder med hjälp av åtkomstkontroll. På så sätt blir klustret säkrare.

#### <a name="client-to-node-certificate-security"></a>För klient-till-nod-Certifikatsäkerhet

Du kan konfigurera för klient-till-nod-Certifikatsäkerhet när du skapar ett kluster via Azure portal, Resource Manager-mallar eller en fristående JSON-mall. Du måste ange ett klientcertifikat för administratör och/eller ett klientcertifikat. Kontrollera att certifikaten skiljer sig från de primära och sekundära certifikat som du anger för nod-till-nod-säkerhet.

Klienter som ansluter till klustret med hjälp av admin-certifikatet har fullständig åtkomst till funktioner för hantering. Klienter som ansluter till klustret med hjälp av klientcertifikatet skrivskyddad användare har bara läsbehörighet till hanteringsfunktioner. Med andra ord används dessa certifikat för rollbaserad åtkomstkontroll (RBAC).

Läs hur du konfigurerar certifikat säkerheten i ett kluster i [konfigurera ett kluster med hjälp av en Azure Resource Manager-mall](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

#### <a name="client-to-node-azure-active-directory-security"></a>Azure Active Directory-säkerhetsgrupp för klient-till-nod

Kluster som körs på Azure kan även skydda åtkomst till management-slutpunkter med hjälp av Azure Active Directory (AD Azure). Information om hur du skapar nödvändiga Azure Active Directory-artefakter, hur du fylla dem när klustret skapas och hur du ansluter till dessa kluster finns i [konfigurera ett kluster med hjälp av en Azure Resource Manager-mall](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

Med Azure AD kan organisationer (som kallas klientorganisationer) hantera användaråtkomst till program. Det finns program med en webbaserad inloggning användargränssnitt och program med en intern klient-upplevelse.

Service Fabric-kluster erbjuder flera startpunkter för dess hanteringsfunktioner, inklusive webbaserade Service Fabric Explorer och Visual Studio. Därför kan du skapa två Azure AD-program för att styra åtkomsten till klustret: ett webbprogram och ett internt program.

Azure-kluster kan rekommenderar vi att du använder Azure AD-säkerhetsgrupper för att autentisera klienter och certifikat för nod-till-nod-säkerhet.

För fristående Windows Server-kluster med Windows Server 2012 R2 och Active Directory rekommenderar vi att du använder Windows-säkerhet med grupphanterade tjänstkonton (gMSAs). Annars kan du använda Windows-säkerhet med Windows-konton.

## <a name="understand-monitoring-and-diagnostics-in-service-fabric"></a>Förstå övervakning och diagnostik i Service Fabric
[Övervakning och diagnostik](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) är viktiga för att utveckla, testa och distribuera program och tjänster i alla miljöer. Service Fabric lösningar fungerar bäst när du implementerar övervakning och diagnostik så att program och tjänster fungerar som förväntat i en lokal utvecklingsmiljö eller i produktion.

Från ett säkerhetsperspektiv är de huvudsakliga målen med övervakning och diagnostik

-   Identifiera och diagnostisera problem med maskinvara och infrastruktur som kan orsakas av en säkerhetshändelse.
-   Identifiera programvara och app-problem som kan vara ett tecken på kompromettering (IoC).
-   Förstå resursförbrukning för att förhindra oavsiktlig DOS-attack.

Arbetsflöde för övervakning och diagnostik består av tre steg:

1.  **Händelsegenerering**: Händelsegenerering inkluderar händelser (loggar, spårningar, anpassade händelser) på både infrastrukturnivå (kluster) och program/tjänst-nivå. Läs mer om [infrastrukturnivå händelser](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-infra) och [händelser på programnivå](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) att förstå vad som tillhandahålls och hur du lägger till ytterligare instrumentation.

2.  **Händelsen aggregering**: Händelser som genereras måste samlas in och sammanställs innan de kan visas. Bör du vanligtvis använda [Azure Diagnostics](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) (liknar agentbaserad Logginsamling) eller [EventFlow](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-eventflow) (pågående Logginsamling).

3.  **Analysis**: Händelser måste vara visualiserade och är tillgänglig i vissa format, för analys och visning. Det finns flera plattformar för analys och visualisering av data för övervakning och diagnostik. Vi rekommenderar att [Azure Monitor loggar](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-oms) och [Azure Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights) eftersom de kan integreras även med Service Fabric.

Du kan också använda [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) att övervaka många av de Azure-resurser som Service Fabric-kluster har skapats.

En watchdog är en separat tjänst som kan titta på hälsotillståndet och läsa in i tjänster och rapporten hälsotillstånd för vad som helst i hierarkin hälsotillstånd modellen. Med hjälp av en watchdog kan du förhindra fel som inte skulle kunna identifieras baserat på vyn för en enskild tjänst. 

Watchdogs är också ett bra ställe att värdhantera kod som utför korrigerande åtgärder utan användaråtgärder. Ett exempel rensa loggfiler i storage vid visst tidsintervall. Du hittar en exemplet watchdog implementering på [Azure Service Fabric watchdog exempel](https://azure.microsoft.com/resources/samples/service-fabric-watchdog-service/).

## <a name="secure-communication-by-using-certificates"></a>Säker kommunikation med hjälp av certifikat
Certifikat för att skydda kommunikationen mellan de olika noderna i ditt fristående Windows-kluster. Du kan också autentisera klienter som ansluter till det här klustret genom att använda X.509-certifikat. Detta säkerställer att endast behöriga användare har åtkomst till klustret. Vi rekommenderar att du aktiverar ett certifikat på klustret när du skapar den.

Digitala X.509-certifikat används ofta för att autentisera klienter och servrar. De också används för att kryptera och digitalt signera meddelanden.

I följande tabell visas de certifikat som du behöver på din konfiguration:

|Inställning för certifikat |Beskrivning|
|-------------------------------|-----------|
|ClusterCertificate|    Detta certifikat krävs för att skydda kommunikationen mellan noderna i ett kluster. Du kan använda två klustercertifikat: ett certifikat för primär och sekundär för uppgradering.|
|ServerCertificate| Det här certifikatet visas för klienten när den försöker ansluta till det här klustret. Du kan använda två servercertifikat: ett certifikat för primär och sekundär för uppgradering.|
|ClientCertificateThumbprints|  Det här är en uppsättning certifikat som installeras på de autentiserade klienterna.|
|ClientCertificateCommonNames|  Det här är vanliga namnet på det första klientcertifikatet för CertificateCommonName. CertificateIssuerThumbprint är tumavtrycket för den här certifikatutfärdaren.|
|ReverseProxyCertificate|   Det här är ett valfritt certifikat som du kan ange om du vill skydda din [omvänd proxy](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy).|

Mer information om hur du skyddar certifikat finns i [skydda ett fristående kluster i Windows genom att använda X.509-certifikat](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security).

## <a name="understand-role-based-access-control"></a>Förstå rollbaserad åtkomstkontroll
Du anger klientroller administratörs- och vid tidpunkten för klustret skapas med separata identiteter (inklusive certifikat) för var och en. Mer information om standardinställningarna för kontroll av åtkomst och hur du ändrar standardinställningarna finns i [rollbaserad åtkomstkontroll för Service Fabric-klienter](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

## <a name="secure-standalone-clusters-by-using-windows-security"></a>Skydda fristående kluster med hjälp av Windows-säkerhet
För att förhindra obehörig åtkomst till Service Fabric-kluster, måste du skydda klustret. Säkerhet är särskilt viktigt när klustret kör produktionsarbetsbelastningar. Du kan konfigurera säkerheten för nod-till-nod och klient-till-nod med hjälp av Windows-säkerhet i filen ClusterConfig.JSON.

När Service Fabric måste köras under ett gMSA, konfigurerar du nod till nod-säkerhet genom att ange [ClustergMSAIdentity](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security). Om du vill skapa betrodda relationer mellan noder, måste du se dem medveten om varandra.

Om du vill använda en datorgrupp i en Active Directory-domän kan konfigurera du nod till nod-säkerhet genom att ange ClusterIdentity. Mer information finns i [skapar en datorgrupp i Active Directory](https://msdn.microsoft.com/library/aa545347).

Du konfigurerar klient-till-nod-säkerhet med hjälp av ClientIdentities. Du måste konfigurera klustret för att identifiera vilka identiteter för klienten som den kan lita på. Du kan upprätta förtroende på två sätt:

-   Ange de gruppanvändare som kan ansluta.
-   Ange noden domänanvändare som kan ansluta.

## <a name="configure-application-security-in-service-fabric"></a>Konfigurera programsäkerhet i Service Fabric
### <a name="manage-secrets-in-service-fabric-applications"></a>Hantera hemligheter i Service Fabric-program
Hemligheter kan vara känslig information, till exempel storage-anslutningssträngar, lösenord och andra värden som inte ska hanteras i oformaterad text.

Du kan använda [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) att hantera nycklar och hemligheter. Men användningen av hemligheter i ett program inte förlita dig på en specifik plattform. Du kan distribuera program till ett kluster som är värdbaserade på olika platser. Det finns fyra steg i det här flödet:

1.  Få en chiffreringscertifikatet för data.
2.  Installera certifikatet i klustret.
3.  Kryptera hemliga värden när du distribuerar ett program med certifikatet och placera dem i en tjänst Settings.xml konfigurationsfilen.
4.  Läsa krypterade värden utanför Settings.xml genom att dekryptera dem med samma chiffreringscertifikatet.

Mer information finns i [hantera hemligheter i Service Fabric-program](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="configure-security-policies-for-an-application"></a>Ställer in säkerhetsprinciper för ett program
Med hjälp av Azure Service Fabric-säkerhet kan du säkra program som körs i klustret under olika användarkonton. Service Fabric-säkerhet kan du också skydda resurser som program använder vid tidpunkten för distribution under användarkonton – till exempel, filer, kataloger och certifikat. Detta gör program som körs, även i en delad miljö, säkrare.

Uppgifterna för att konfigurera säkerhetsprinciper är:

-   Konfigurera principen för en tjänstens konfigurationsstartpunkt
-   Starta PowerShell-kommandon från en startpunkt för installationen
-   Använda omdirigering av konsol för lokal felsökning
-   Konfigurera en princip för tjänsten kodpaket
-   Tilldela en säkerhetsåtkomstprincip för HTTP och HTTPS-slutpunkter

## <a name="secure-communication-for-services"></a>Säker kommunikation för tjänster
Säkerhet är en av de viktigaste aspekterna av kommunikation. Reliable Services application framework innehåller några fördefinierade kommunikation stackar och verktyg som du kan använda för att förbättra säkerheten. Mer information finns i [säkra kommunikationer för fjärrkommunikation för en tjänst](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication).

## <a name="next-steps"></a>Nästa steg
- Konceptuell information om Klustersäkerhet finns i [skapa Service Fabric-kluster med hjälp av Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) och [skapa Service Fabric-kluster med hjälp av Azure-portalen](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal).
- Mer information om Klustersäkerhet i Service Fabric finns [säkerhetsscenarier för Service Fabric-kluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).
