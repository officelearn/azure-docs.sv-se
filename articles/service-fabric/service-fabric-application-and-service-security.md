---
title: Lär dig mer om Azure Service Fabric programsäkerhet | Microsoft Docs
description: En översikt över hur du kör på ett säkert sätt mikrotjänster program på Service Fabric. Lär dig hur du kör tjänster och Start-skript under olika konton, autentisera och auktorisera användare, hantera program hemligheter, skydda servicemeddelanden, använda en API-gateway och säker programdata i vila.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/16/2018
ms.author: ryanwi
ms.openlocfilehash: a84e42d3a0254c90bfad2d54eda1aa8e5e35650a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="service-fabric-application-and-service-security"></a>Service Fabric-program och tjänster
Arkitektur för mikrotjänster kan ta [många fördelar](service-fabric-overview-microservices.md). Hantera säkerheten för mikrotjänster, är dock en utmaning och annat än traditionella gigantiska program säkerhetshantering. 

Programmet körs vanligtvis på en eller flera servrar i ett nätverk med en monolitvolym och det är lättare att identifiera synliga portar och API: er och IP-adressen. Det är ofta en perimeter eller gräns och en databas för att skydda. Om systemet äventyras på grund av ett säkerhetsintrång eller attack, är det troligt att allt i systemet kan angripare. Med mikrotjänster är systemet mer komplexa.  Tjänster decentraliserad och fördelade på flera värdar och migrera från en värd till en värd.  Med rätt säkerhet begränsa du behörigheter för en angripare kan få och mängden data som är tillgängliga i en enda attack av brott mot en tjänst.  Kommunikation är inte internt, men det sker över ett nätverk och det finns många exponerade portar och samverkan mellan tjänster. Känna till vilka dessa interaktioner som tjänsten är och när de inträffar är avgörande för ditt programsäkerhet.

Den här artikeln är inte en guide till mikrotjänster säkerhet, det finns många resurser online, men beskrivs hur olika aspekter på säkerheten kan utföras i Service Fabric.

## <a name="authentication-and-authorization"></a>Autentisering och auktorisering
Det är ofta nödvändigt för resurser och API: er för en tjänst ska begränsas till vissa betrodda användare eller klienter. Autentisering är processen för att fastställa användarens identitet på ett tillförlitligt sätt.  Auktorisering är den process som gör API: er eller tjänster tillgängliga för några autentiserade användare, men inte andra.

### <a name="authentication"></a>Autentisering
Det första steget att API-nivå förtroende beslutsfattande är autentisering. Autentisering är processen för att fastställa användarens identitet på ett tillförlitligt sätt.  I scenarier med mikrotjänster hanteras autentisering vanligtvis centralt. Om du använder en API-Gateway, kan du [offload autentisering](/azure/architecture/patterns/gateway-offloading) till gatewayen. Om du använder den här metoden kan du se till att enskilda tjänster inte kan nås direkt (utan API-Gateway) om inte ytterligare säkerhet för att autentisera meddelanden om de kommer från gatewayen och eller inte.

Om tjänster kan nås direkt är en autentiseringstjänst som Azure Active Directory eller en dedikerad autentisering mikrotjänster fungerar som en säkerhets-och token-tjänsten (STS) kan användas för att autentisera användare. Förtroendebeslut delas mellan tjänster med säkerhetstoken eller cookies. 

För ASP.NET Core, den primära mekanismen för [autentisering av användare](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/) är ASP.NET Core identitet medlemskapssystem. ASP.NET Core identitet lagrar information om användare (inklusive inloggningsinformation, roller och anspråk) i ett dataarkiv som konfigurerats av utvecklaren. ASP.NET Core identitet stöder tvåfaktorsautentisering.  Externa autentiseringsproviders stöds också, så att användare kan logga in med befintliga Autentiseringsprocesser från leverantörer som Microsoft, Google, Facebook och Twitter. 

### <a name="authorization"></a>Auktorisering
Efter autentisering-tjänsterna måste ger användarbehörighet eller ta reda på vad användaren kan göra. Den här processen kan en tjänst att API: er tillgängliga vissa autentiserade användare, men inte till alla. Auktorisering är ortogonala och oberoende av autentisering, vilket är en process för att fastställa som en användare. Autentisering kan skapa en eller flera identiteter för den aktuella användaren.

[ASP.NET Core auktorisering](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/authorization-net-microservices-web-applications) kan göra baserat på användarnas roller eller baserat på anpassad policy som kan innehålla undersöks anspråk eller andra heuristik.

## <a name="restrict-and-secure-access-using-an-api-gateway"></a>Begränsa och säker åtkomst med hjälp av en API-gateway
Molnprogram behöver ofta en klientdelsgateway som enda åtkomstpunkt för ingång för användare, enheter och andra program. En [API gateway](/azure/architecture/microservices/gateway) placeras mellan klienter och tjänster och är startpunkten för att alla tjänster som tillhandahåller ditt program. Det fungerar som en omvänd proxy routning begäranden från klienter till tjänster. Det kan också utföra olika övergripande åtgärder som autentisering och auktorisering, SSL-avslutning och hastighetsbegränsning. Om du inte distribuera en gateway, måste klienter skicka begäranden direkt till frontend-tjänster.

I Service Fabric en gateway kan alla tillståndslösa tjänster som en [ASP.NET Core programmet](service-fabric-reliable-services-communication-aspnetcore.md), eller en annan tjänst som utformats för trafik ingång [Træfik](https://docs.traefik.io/), [Händelsehubbar](https://docs.microsoft.com/azure/event-hubs/), [IoT-hubb](https://docs.microsoft.com/azure/iot-hub/), eller [Azure API Management](https://docs.microsoft.com/azure/api-management).

API Management integreras direkt med Service Fabric, så att du kan publicera API: er med en omfattande uppsättning regler för routning till backend-Service Fabric-tjänster.  Du kan skydda åtkomst till backend-tjänster, förhindra DOS-attacker med hjälp av begränsning eller verifiera API-nycklar, JWT-token, certifikat och andra autentiseringsuppgifter. Mer information, [Service Fabric med översikt över Azure API Management](service-fabric-api-management-overview.md).

## <a name="manage-application-secrets"></a>Hantera programhemligheter
Hemligheter kan vara känslig information, till exempel storage-anslutningssträngar, lösenord eller andra värden som inte ska hanteras i oformaterad text. Den här artikeln använder Azure Key Vault för att hantera nycklar och hemligheter. Dock *med* hemligheter i ett program är molnet plattformsoberoende så att program som ska distribueras till ett kluster som värd var som helst.

Det rekommenderade sättet att hantera konfigurationsinställningar för tjänsten sker via [tjänsten konfigurationspaket][config-package]. Konfigurationspaket namnges och uppdateras via hanterade samlade uppgraderingar med hälsovalideringsutdrag och automatisk återställning. Detta är att föredra global konfiguration eftersom det minskar risken för ett avbrott i tjänsten för global. Krypterade hemligheter är inget undantag. Service Fabric har inbyggda funktioner för att kryptera och dekryptera värden i en paketet Settings.xml konfigurationsfil med hjälp av för certifikatkryptering.

Följande diagram illustrerar det grundläggande flödet för hemliga hantering i ett Service Fabric-program:

![Översikt över hemliga management][overview]

Det finns fyra steg i det här flödet:

1. Skaffa ett certifikat för chiffrering av data.
2. Installera certifikatet i klustret.
3. Kryptera hemliga värden när du distribuerar ett program med certifikatet och mata in dem i en tjänst Settings.xml konfigurationsfilen.
4. Läsa krypterade värden utanför Settings.xml genom att dekryptera med samma chiffrering av certifikat. 

[Azure Key Vault] [ key-vault-get-started] används här som en säker lagringsplats för certifikat och som ett sätt att få certifikat installeras på Service Fabric-kluster i Azure. Om du inte distribuerar till Azure, behöver du inte använda Nyckelvalv för att hantera hemligheter i Service Fabric-program.

Ett exempel finns [hantera program hemligheter](service-fabric-application-secret-management.md).

## <a name="secure-the-hosting-environment"></a>Skydda värdmiljö.
Du kan skydda program som körs i kluster under olika användarkonton med hjälp av Azure Service Fabric. Service Fabric hjälper också till att skydda resurser som används av program vid tidpunkten för distribution under användarkonton – till exempel, filer, kataloger och certifikat. Det gör att program som körs, även i en delad värdmiljö säkrare från varandra.

Applikationsmanifestet deklarerar säkerhetsobjekt (användare och grupper) krävs kör tjänster och säkra resurser.  Dessa säkerhetsobjekt som refereras i principer, till exempel slutpunkten för Kör som-, bindning, säkerhetspaket dela eller åtkomst säkerhetsprinciper.  Principer tillämpas sedan på tjänsten resurser i den **ServiceManifestImport** avsnitt i programmanifestet.

När deklarerar säkerhetsobjekt kan också definiera och skapa användargrupper så att en eller flera användare kan läggas till varje grupp som ska hanteras tillsammans. Detta är användbart när det finns flera användare för olika startpunkter och de måste ha vissa vanliga behörigheter som är tillgängliga på gruppnivå.

Service Fabric-program körs under kontot som Fabric.exe-processen körs under som standard. Service Fabric ger också möjlighet att köra program under ett lokalt användarkonto eller kontot Lokalt system som anges i programmanifestet. Mer information finns i [kör en tjänst som ett lokalt användarkonto eller kontot Lokalt system](service-fabric-application-runas-security.md).  Du kan också [köra ett skript för start av tjänsten som ett lokalt konto för användaren eller systemet](service-fabric-run-script-at-service-startup.md).

När du kör Service Fabric i ett fristående Windows-kluster, kan du köra en tjänst under [Active Directory-domänkonton](service-fabric-run-service-as-ad-user-or-group.md) eller [grupphanterade tjänstkonton](service-fabric-run-service-as-gmsa.md).

## <a name="secure-containers"></a>Säkra behållare
Service Fabric är en mekanism för tjänster i en behållare för att komma åt ett certifikat som är installerad på noder i ett Windows- eller Linux-kluster (version 5.7 eller högre). Den här PFX-certifikat kan användas för att autentisera program eller tjänst eller säker kommunikation med andra tjänster. Mer information finns i [importera ett certifikat till en behållare](service-fabric-securing-containers.md).

Service Fabric stöder dessutom också gMSA (grupphanterade tjänstkonton) för Windows-behållare. Mer information finns i [ställa in gMSA för Windows-behållare](service-fabric-setup-gmsa-for-windows-containers.md).

## <a name="secure-service-communication"></a>Säker kommunikation
I Service Fabric körs en tjänst någonstans i ett Service Fabric-kluster, vanligtvis fördelade på flera virtuella datorer. Service Fabric erbjuder flera alternativ för att skydda din service-kommunikation.

Du kan aktivera HTTPS-slutpunkterna i din [ASP.NET Core eller Java](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) webbtjänster.

Du kan upprätta säker anslutning mellan omvänd proxy och tjänster, vilket gör att en säker kanal för slutpunkt till slutpunkt. Ansluter till säker tjänster stöds endast när omvänd proxy har konfigurerats för att lyssna på HTTPS. Information om hur du konfigurerar omvänd proxy finns [omvänd proxy i Azure Service Fabric](service-fabric-reverseproxy.md).  [Ansluta till en säker tjänst](service-fabric-reverseproxy-configure-secure-communication.md) beskriver hur du upprättar säker anslutning mellan omvänd proxy och tjänster.

Application framework Reliable Services innehåller några fördefinierade kommunikation stackar och verktyg som du kan använda för att förbättra säkerheten. Lär dig att förbättra säkerheten när du använder tjänsten fjärrkommunikation (i [C#](service-fabric-reliable-services-secure-communication.md) eller [Java](service-fabric-reliable-services-secure-communication-java.md)) eller med hjälp av [WCF](service-fabric-reliable-services-secure-communication-wcf.md).

## <a name="encrypt-application-data-at-rest"></a>Kryptera programmet data i vila
Varje [nodtypen](service-fabric-cluster-nodetypes.md) i ett Service Fabric-kluster körs i Azure backas upp av en [virtuella datorns skaluppsättning](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Med en Azure Resource Manager-mall kan du koppla datadiskar till skala uppsättning som utgör Service Fabric-klustret.  Om dina tjänster sparar data till en disk med bifogade data, kan du [kryptera dessa datadiskar](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md) att skydda dina programdata.

<!--TO DO: Enable BitLocker on Windows standalone clusters?
TO DO: Encrypt disks on Linux clusters?-->


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg
* [Kör ett installationsskript när tjänsten startar](service-fabric-run-script-at-service-startup.md)
* [Ange resurser i en tjänstmanifestet](service-fabric-service-manifest-resources.md)
* [Distribuera ett program](service-fabric-deploy-remove-applications.md)
* [Lär dig mer om Klustersäkerhet](service-fabric-cluster-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[config-package]: service-fabric-application-and-service-manifests.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-and-service-security/overview.png