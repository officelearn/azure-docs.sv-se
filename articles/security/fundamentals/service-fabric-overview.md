---
title: Översikt över Azure Service Fabric-säkerhet | Microsoft Docs
description: Den här artikeln innehåller en översikt över Azure Service Fabric Security.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.openlocfilehash: 5329323122d8470b19055040b6f00b6db7c0e75f
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2019
ms.locfileid: "70998955"
---
# <a name="azure-service-fabric-security-overview"></a>Översikt över Azure Service Fabric-säkerhet
[Azure Service Fabric](../../service-fabric/service-fabric-overview.md) är en distribuerad system plattform som gör det enkelt att paketera, distribuera och hantera skalbara och pålitliga mikrotjänster. Service Fabric åtgärdar utmaningarna med att utveckla och hantera moln program. Utvecklare och administratörer kan undvika komplexa infrastruktur problem och fokusera på att implementera verksamhets kritiska, krävande arbets belastningar som är skalbara och tillförlitliga.

Den här artikeln är en översikt över säkerhets överväganden för en Service Fabric distribution.

## <a name="secure-your-cluster"></a>Säkra ditt kluster
Azure Service Fabric dirigerar tjänster i ett kluster med datorer. Kluster måste skyddas för att förhindra att obehöriga användare ansluter till dem, särskilt när de kör produktions arbets belastningar. Även om det är möjligt att skapa ett oskyddat kluster kan detta tillåta att anonyma användare ansluter till klustret (om det exponerar hanterings slut punkter för det offentliga Internet).

För kluster som kör antingen fristående eller på Azure är två scenarier som ska beaktas nod-till-nod-säkerhet och klient-till-nod-säkerhet. Du kan använda olika tekniker för att implementera dessa scenarier.

### <a name="node-to-node-security"></a>Säkerhet från nod till nod
Säkerhet från nod till nod gäller för kommunikation mellan de virtuella datorerna eller datorerna i ett kluster. Med nod-till-nod-säkerhet kan endast datorer som har behörighet att ansluta till klustret delta i värdbaserade program och tjänster i klustret.

Kluster som körs på Azure eller fristående kluster som körs på Windows kan använda antingen [certifikat säkerhet](https://msdn.microsoft.com/library/ff649801.aspx) eller [Windows-säkerhet](https://msdn.microsoft.com/library/ff649396.aspx) för Windows Server-datorer.

#### <a name="node-to-node-certificate-security"></a>Säkerhet för nod-till-nod-certifikat

Service Fabric använder de X. 509-Server certifikat som du anger när du skapar ett kluster. En snabb översikt över vad dessa certifikat är och hur du kan förvärva eller skapa dem finns i [arbeta med certifikat](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates).

Du konfigurerar certifikat säkerhet när du skapar klustret via Azure Portal, Azure Resource Manager mallar eller en fristående JSON-mall. Du kan ange ett primärt certifikat och ett valfritt sekundärt certifikat som används för certifikat förnyelser. De primära och sekundära certifikat som du anger bör skilja sig från administratörs klienten och de skrivskyddade klient certifikat som du anger för [säkerhet från klient till nod](../../service-fabric/service-fabric-cluster-security.md).

### <a name="client-to-node-security"></a>Säkerhet från klient till nod
Du konfigurerar säkerhet från klient till nod med hjälp av klient identiteter. Om du vill upprätta förtroende mellan en klient och ett kluster måste du konfigurera klustret för att veta vilka klient identiteter det kan lita på.

Service Fabric stöder två åtkomst kontroll typer för klienter som är anslutna till ett Service Fabric-kluster:

-   **Administratör**: Fullständig åtkomst till hanterings funktioner, inklusive Läs-och skriv funktioner.
-   **Användare**: Läs åtkomst till hanterings funktioner (till exempel fråge funktioner) och möjlighet att lösa program och tjänster.

Med hjälp av åtkomst kontroll kan kluster administratörer begränsa åtkomsten till vissa typer av kluster åtgärder. Detta gör klustret säkrare.

#### <a name="client-to-node-certificate-security"></a>Säkerhet för klient-till-nod-certifikat

Du konfigurerar säkerheten för klient-till-nod-certifikat när du skapar ett kluster via Azure Portal, Resource Manager-mallar eller en fristående JSON-mall. Du måste ange ett administratörs klient certifikat och/eller ett användar klient certifikat. Se till att dessa certifikat skiljer sig från de primära och sekundära certifikat som du anger för nod-till-nod-säkerhet.

Klienter som ansluter till klustret med hjälp av administratörs certifikatet har fullständig åtkomst till hanterings funktionerna. Klienter som ansluter till klustret med hjälp av det skrivskyddade användar klient certifikatet har bara Läs behörighet till hanterings funktionerna. Dessa certifikat används med andra ord för rollbaserad åtkomst kontroll (RBAC).

Information om hur du konfigurerar certifikat säkerhet i ett kluster finns i [Konfigurera ett kluster med hjälp av en Azure Resource Manager mall](../../service-fabric/service-fabric-cluster-creation-via-arm.md).

#### <a name="client-to-node-azure-active-directory-security"></a>Säkerhet från klient till nod Azure Active Directory

Kluster som körs på Azure kan också säkra åtkomst till hanterings slut punkter med hjälp av Azure Active Directory (Azure AD). Information om hur du skapar nödvändiga Azure Active Directory artefakter, hur du fyller dem när du skapar kluster och hur du ansluter till dessa kluster finns i [Konfigurera ett kluster med hjälp av en Azure Resource Manager-mall](../../service-fabric/service-fabric-cluster-creation-via-arm.md).

Med Azure AD kan organisationer (som kallas klientorganisationer) hantera användaråtkomst till program. Det finns program med ett webbaserat inloggnings gränssnitt och program med en inbyggd klient miljö.

Ett Service Fabric-kluster erbjuder flera start punkter till hanterings funktionerna, inklusive den webbaserade Service Fabric Explorer och Visual Studio. Därför kan du skapa två Azure AD-program för att styra åtkomsten till klustret: ett webbprogram och ett internt program.

För Azure-kluster rekommenderar vi att du använder Azure AD-säkerhet för att autentisera klienter och certifikat för nod-till-nod-säkerhet.

För fristående Windows Server-kluster med Windows Server 2012 R2 och Active Directory rekommenderar vi att du använder Windows-säkerhet med grupphanterade tjänst konton (gMSAs). Annars använder du Windows-säkerhet med Windows-konton.

## <a name="understand-monitoring-and-diagnostics-in-service-fabric"></a>Förstå övervakning och diagnostik i Service Fabric
[Övervakning och diagnostik](../../service-fabric/service-fabric-diagnostics-overview.md) är avgörande för att utveckla, testa och distribuera program och tjänster i valfri miljö. Service Fabric lösningar fungerar bäst när du implementerar övervakning och diagnostik för att säkerställa att program och tjänster fungerar som förväntat i en lokal utvecklings miljö eller i produktion.

I ett säkerhets perspektiv är huvud målen för övervakning och diagnostik följande:

-   Identifiera och diagnostisera maskinvaru-och infrastruktur problem som kan ha orsakats av en säkerhets händelse.
-   Identifiera problem med program vara och appar som kan vara en indikation på kompromisser (IoC).
-   Lär dig mer om resursförbrukning för att förhindra oavsiktlig denial of service.

Arbets flödet för övervakning och diagnostik består av tre steg:

1.  **Generering av händelse**: Event Generation inkluderar händelser (loggar, spårningar, anpassade händelser) på både infrastruktur nivå (kluster nivå) och program-/tjänst nivå. Läs mer om händelser på [infrastruktur nivå](../../service-fabric/service-fabric-diagnostics-event-generation-infra.md) och händelser på [program nivå](../../service-fabric/service-fabric-diagnostics-event-generation-app.md) för att förstå vad som tillhandahålls och hur du lägger till ytterligare Instrumentation.

2.  **Händelse agg regering**: Genererade händelser måste samlas in och aggregeras innan de kan visas. Vi rekommenderar vanligt vis att använda [Azure-diagnostik](../../service-fabric/service-fabric-diagnostics-event-aggregation-wad.md) (liknar agent-baserad logg insamling) eller [EventFlow](../../service-fabric/service-fabric-diagnostics-event-aggregation-eventflow.md) (bearbetning av loggar).

3.  **Analys**: Händelser behöver visualiseras och vara tillgängliga i viss form för att tillåta analys och visning. Det finns flera plattformar för analys och visualisering av övervaknings-och diagnostikdata. Vi rekommenderar [Azure Monitor loggar](../../service-fabric/service-fabric-diagnostics-event-analysis-oms.md) och [Azure Application insikter](../../service-fabric/service-fabric-diagnostics-event-analysis-appinsights.md) eftersom de integreras bra med Service Fabric.

Du kan också använda [Azure Monitor](../../azure-monitor/overview.md) för att övervaka många av de Azure-resurser där ett Service Fabric-kluster har skapats.

En övervaknings enhet är en separat tjänst som kan se hälso tillstånd och belastning mellan tjänster och rapportera hälsa för allt i hälso modell hierarkin. Om du använder en övervaknings enhet kan du förhindra fel som inte kan identifieras baserat på en enskild tjänsts visning. 

Övervaknings enheter är också en bra plats för att vara värd för kod som utför åtgärds åtgärder utan användar interaktion. Ett exempel är att rensa loggfiler i lagring vid vissa tidsintervall. Du hittar en exempel på en övervaknings tjänst implementering i [Azure Service Fabrics övervaknings exempel](https://azure.microsoft.com/resources/samples/service-fabric-watchdog-service/).

## <a name="secure-communication-by-using-certificates"></a>Säker kommunikation med hjälp av certifikat
Certifikat hjälper dig att skydda kommunikationen mellan de olika noderna i det fristående Windows-klustret. Genom att använda X. 509-certifikat kan du även autentisera klienter som ansluter till det här klustret. Detta säkerställer att endast behöriga användare kan komma åt klustret. Vi rekommenderar att du aktiverar ett certifikat i klustret när du skapar det.

X. 509 digitala certifikat används ofta för att autentisera klienter och servrar. De används också för att kryptera och signera meddelanden digitalt.

I följande tabell visas de certifikat som du behöver i kluster konfigurationen:

|Inställning av certifikat information |Beskrivning|
|-------------------------------|-----------|
|ClusterCertificate|    Detta certifikat krävs för att skydda kommunikationen mellan noderna i ett kluster. Du kan använda två kluster certifikat: ett primärt certifikat och en sekundär för uppgradering.|
|ServerCertificate| Det här certifikatet presenteras för klienten när det försöker ansluta till det här klustret. Du kan använda två server certifikat: ett primärt certifikat och en sekundär för uppgradering.|
|ClientCertificateThumbprints|  Detta är en uppsättning certifikat som ska installeras på de autentiserade klienterna.|
|ClientCertificateCommonNames|  Detta är nätverks namnet för det första klient certifikatet för CertificateCommonName. CertificateIssuerThumbprint är tumavtrycket för utfärdaren av det här certifikatet.|
|ReverseProxyCertificate|   Det här är ett valfritt certifikat som du kan ange för att skydda den [omvända proxyn](../../service-fabric/service-fabric-reverseproxy.md).|

Mer information om hur du skyddar certifikat finns i [skydda ett fristående kluster i Windows med hjälp av X. 509-certifikat](../../service-fabric/service-fabric-windows-cluster-x509-security.md).

## <a name="understand-role-based-access-control"></a>Förstå rollbaserad åtkomst kontroll
Du anger administratörs-och användar klient roller när klustret skapas genom att tillhandahålla separata identiteter (inklusive certifikat) för var och en. Mer information om standard inställningarna för åtkomst kontroll och hur du ändrar standardinställningarna finns i [rollbaserad åtkomst kontroll för Service Fabric klienter](../../service-fabric/service-fabric-cluster-security-roles.md).

## <a name="secure-standalone-clusters-by-using-windows-security"></a>Säkra fristående kluster med hjälp av Windows-säkerhet
För att förhindra obehörig åtkomst till ett Service Fabric-kluster måste du skydda klustret. Säkerhet är särskilt viktigt när klustret kör produktions arbets belastningar. Du konfigurerar nod-till-nod-och klient-till-nod-säkerhet genom att använda Windows-säkerhet i ClusterConfig. JSON-filen.

När Service Fabric behöver köras under en gMSA konfigurerar du Node-to-Node-säkerhet genom att ange [ClustergMSAIdentity](../../service-fabric/service-fabric-windows-cluster-windows-security.md). Om du vill skapa förtroende relationer mellan noder måste du göra dem medvetna om varandra.

Om du vill använda en dator grupp i en Active Directory domän konfigurerar du Node-to-Node-säkerhet genom att ange ClusterIdentity. Mer information finns i [skapa en dator grupp i Active Directory](https://msdn.microsoft.com/library/aa545347).

Du konfigurerar säkerhet från klient till nod med hjälp av ClientIdentities. Du måste konfigurera klustret för att identifiera vilka klient identiteter som det kan lita på. Du kan upprätta förtroende på två sätt:

-   Ange de domän grupps användare som kan ansluta.
-   Ange de domän-Node-användare som kan ansluta.

## <a name="configure-application-security-in-service-fabric"></a>Konfigurera program säkerhet i Service Fabric
### <a name="manage-secrets-in-service-fabric-applications"></a>Hantera hemligheter i Service Fabric program
Hemligheter kan vara vilken känslig information som helst, till exempel lagrings anslutnings strängar, lösen ord eller andra värden som inte ska hanteras i oformaterad text.

Du kan använda [Azure Key Vault](../../key-vault/key-vault-overview.md) för att hantera nycklar och hemligheter. Användningen av hemligheter i ett program är dock inte beroende av en specifik moln plattform. Du kan distribuera program till ett kluster som finns var som helst. Det finns fyra huvudsakliga steg i det här flödet:

1.  Hämta ett certifikat för data kryptering.
2.  Installera certifikatet på klustret.
3.  Kryptera hemliga värden när du distribuerar ett program med certifikatet och mata in dem i en tjänsts konfigurations fil för inställningar. xml.
4.  Läsa krypterade värden från Settings. xml genom att dekryptera dem med samma krypterings certifikat.

Mer information finns i [Hantera hemligheter i Service Fabric-program](../../service-fabric/service-fabric-application-secret-management.md).

### <a name="configure-security-policies-for-an-application"></a>Konfigurera säkerhets principer för ett program
Genom att använda Azure Service Fabric Security kan du skydda program som körs i klustret under olika användar konton. Service Fabric säkerhet skyddar också resurserna som program använder vid tidpunkten för distributionen under användar konton, till exempel filer, kataloger och certifikat. Detta gör att program körs, även i en delad värd miljö, säkrare.

Uppgifter för att konfigurera säkerhets principer är:

-   Konfigurera principen för en start punkt för tjänst konfigurationen
-   Starta PowerShell-kommandon från en installations start punkt
-   Använda omdirigering av konsol för lokal fel sökning
-   Konfigurera en princip för service kods paket
-   Tilldela en säkerhets åtkomst princip för HTTP-och HTTPS-slutpunkter

## <a name="secure-communication-for-services"></a>Säker kommunikation för tjänster
Säkerhet är en av de viktigaste aspekterna av kommunikationen. Reliable Services Application Framework innehåller några inbyggda kommunikations stackar och verktyg som du kan använda för att förbättra säkerheten. Mer information finns i [Secure service Remoting-kommunikation för en tjänst](../../service-fabric/service-fabric-reliable-services-secure-communication.md).

## <a name="next-steps"></a>Nästa steg
- För konceptuell information om kluster säkerhet, se [skapa ett Service Fabric kluster med Azure Resource Manager](../../service-fabric/service-fabric-cluster-creation-via-arm.md) och [skapa ett Service Fabric kluster med hjälp av Azure Portal](../../service-fabric/service-fabric-cluster-creation-via-portal.md).
- Mer information om kluster säkerhet i Service Fabric finns [Service Fabric kluster säkerhets scenarier](../../service-fabric/service-fabric-cluster-security.md).
