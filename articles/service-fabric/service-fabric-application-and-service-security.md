---
title: Lär dig mer om programsäkerhet i Azure Service Fabric
description: En översikt över hur du korrekt kör mikrotjänstprogram på Service Fabric. Lär dig hur du kör tjänster och startskript under olika säkerhetskonton, autentiserar och auktoriserar användare, hanterar programhemligheter, säker tjänstkommunikation, använder en API-gateway och skyddar programdata i vila.
ms.topic: conceptual
ms.date: 03/16/2018
ms.openlocfilehash: c97c5345a1a18cce8c44508542f12d3642d2b8f9
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461437"
---
# <a name="service-fabric-application-and-service-security"></a>Service Fabric-program- och tjänstsäkerhet
En arkitektur för mikrotjänster kan ge [många fördelar.](service-fabric-overview-microservices.md) Att hantera säkerheten för mikrotjänster är dock en utmaning och annorlunda än att hantera traditionella monolitiska programsäkerhet. 

Med en monolit körs programmet vanligtvis på en eller flera servrar i ett nätverk och det är enklare att identifiera de exponerade portarna och API:erna och IP-adressen. Det finns ofta en omkrets eller gräns och en databas att skydda. Om det systemet äventyras på grund av en säkerhetsöverträdelse eller attack, är det troligt att allt i systemet kommer att vara tillgängliga för angriparen. Med mikrotjänster är systemet mer komplext.  Tjänsterna är decentraliserade och distribuerade över många värdar och migrerar från värd till värd.  Med rätt säkerhet begränsar du de privilegier en angripare kan få och mängden data som är tillgängliga i en enda attack genom att bryta mot en tjänst.  Kommunikation är inte intern, men sker över ett nätverk, och det finns många exponerade portar och interaktioner mellan tjänster. Att veta vad dessa tjänstinteraktioner är och när de inträffar är avgörande för din programsäkerhet.

Den här artikeln är inte en guide till mikrotjänstsäkerhet, det finns många sådana resurser tillgängliga online, men beskriver hur olika aspekter av säkerhet kan utföras i Service Fabric.

## <a name="authentication-and-authorization"></a>Autentisering och auktorisering
Det är ofta nödvändigt att resurser och API:er som exponeras av en tjänst begränsas till vissa betrodda användare eller klienter. Autentisering är processen att på ett tillförlitligt sätt fastställa en användares identitet.  Auktorisering är den process som gör API:er eller tjänster tillgängliga för vissa autentiserade användare men inte för andra.

### <a name="authentication"></a>Autentisering
Det första steget för att fatta förtroendebeslut på API-nivå är autentisering. Autentisering är processen att på ett tillförlitligt sätt fastställa en användares identitet.  I mikrotjänstscenarier hanteras autentisering vanligtvis centralt. Om du använder en API Gateway kan du [avlasta autentisering](/azure/architecture/patterns/gateway-offloading) till gatewayen. Om du använder den här metoden kontrollerar du att de enskilda tjänsterna inte kan nås direkt (utan API-gatewayen) om det inte finns ytterligare säkerhet för att autentisera meddelanden oavsett om de kommer från gatewayen eller inte.

Om tjänster kan nås direkt kan en autentiseringstjänst som Azure Active Directory eller en dedikerad autentiseringsmikrotjänst som fungerar som en sts -tjänst (Security Token Service) användas för att autentisera användare. Förtroendebeslut delas mellan tjänster med säkerhetstoken eller cookies. 

För ASP.NET Core är den primära mekanismen för [att autentisera användare](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/) ASP.NET Core Identity-medlemskapssystem. ASP.NET Core Identity lagrar användarinformation (inklusive inloggningsinformation, roller och anspråk) i ett datalager som konfigurerats av utvecklaren. ASP.NET Core Identity stöder tvåfaktorsautentisering.  Externa autentiseringsleverantörer stöds också, så att användarna kan logga in med befintliga autentiseringsprocesser från leverantörer som Microsoft, Google, Facebook eller Twitter.

### <a name="authorization"></a>Auktorisering
Efter autentisering måste tjänster auktorisera användaråtkomst eller avgöra vad en användare kan göra. Den här processen gör det möjligt för en tjänst att göra API:er tillgängliga för vissa autentiserade användare, men inte för alla. Auktorisering är ortogonal och oberoende av autentisering, vilket är processen att fastställa vem en användare är. Autentisering kan skapa en eller flera identiteter för den aktuella användaren.

[ASP.NET Core-auktorisering](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/authorization-net-microservices-web-applications) kan göras baserat på användarnas roller eller baserat på anpassad princip, vilket kan innefatta att inspektera anspråk eller andra heuristik.

## <a name="restrict-and-secure-access-using-an-api-gateway"></a>Begränsa och skydda åtkomst med hjälp av en API-gateway
Molnprogram behöver ofta en klientdelsgateway som enda åtkomstpunkt för ingång för användare, enheter och andra program. En [API-gateway](/azure/architecture/microservices/gateway) sitter mellan klienter och tjänster och är startpunkten för alla tjänster som ditt program tillhandahåller. Den fungerar som en omvänd proxy, routning begäranden från klienter till tjänster. Det kan också utföra olika övergripande uppgifter som autentisering och auktorisering, TLS-avslutning och hastighetsbegränsning. Om du inte distribuerar en gateway måste klienter skicka begäranden direkt till frontend-tjänster.

I Service Fabric kan en gateway vara vilken tillståndslös tjänst som helst, till exempel ett [ASP.NET Core-program](service-fabric-reliable-services-communication-aspnetcore.md)eller en annan tjänst som är avsedd för trafikinträngning, till exempel [Traefik](https://docs.traefik.io/), [Event Hubs](https://docs.microsoft.com/azure/event-hubs/), [IoT Hub](https://docs.microsoft.com/azure/iot-hub/)eller Azure API [Management](https://docs.microsoft.com/azure/api-management).

API Management integreras direkt med Service Fabric, så att du kan publicera API:er med en omfattande uppsättning routningsregler till dina backend-Tjänst Fabric-tjänster.  Du kan skydda åtkomsten till serverdtjänster, förhindra DOS-attacker med hjälp av begränsning eller verifiera API-nycklar, JWT-token, certifikat och andra autentiseringsuppgifter. Mer information finns i [Översikt över Service Fabric med Azure API Management](service-fabric-api-management-overview.md).

## <a name="manage-application-secrets"></a>Hantera programhemligheter
Hemligheter kan vara känslig information, till exempel lagringsanslutningssträngar, lösenord eller andra värden som inte ska hanteras i oformaterad text. I den här artikeln används Azure Key Vault för att hantera nycklar och hemligheter. Men *att använda* hemligheter i ett program är molnplattformsoberoende för att tillåta att program distribueras till ett kluster som finns var som helst.

Det rekommenderade sättet att hantera inställningar för tjänstkonfiguration är via [tjänstkonfigurationspaket][config-package]. Konfigurationspaket är versionsversionerade och uppdateringsbara genom hanterade rullande uppgraderingar med hälsovalidering och automatisk återställning. Detta är att föredra framför global konfiguration eftersom det minskar risken för ett globalt avbrott i tjänsten. Krypterade hemligheter är inget undantag. Service Fabric har inbyggda funktioner för kryptering och dekryptering av värden i en konfigurationspaket Settings.xml-fil med certifikatkryptering.

Följande diagram illustrerar det grundläggande flödet för hemlig hantering i ett Service Fabric-program:

![hemlig hantering översikt][overview]

Det finns fyra huvudsteg i det här flödet:

1. Skaffa ett datanvisningscertifikat.
2. Installera certifikatet i klustret.
3. Kryptera hemliga värden när du distribuerar ett program med certifikatet och injicera dem i en tjänsts Configuration.xml-konfigurationsfil.
4. Läs krypterade värden ur Settings.xml genom att dekryptera med samma krypteringscertifikat. 

[Azure Key Vault][key-vault-get-started] används här som en säker lagringsplats för certifikat och som ett sätt att få certifikat installerade på Service Fabric-kluster i Azure. Om du inte distribuerar till Azure behöver du inte använda Key Vault för att hantera hemligheter i Service Fabric-program.

Ett exempel finns i [Hantera programhemligheter](service-fabric-application-secret-management.md).

## <a name="secure-the-hosting-environment"></a>Säkra värdmiljön
Genom att använda Azure Service Fabric kan du skydda program som körs i klustret under olika användarkonton. Service Fabric hjälper också till att skydda de resurser som används av program vid tidpunkten för distributionen under användarkontona - till exempel filer, kataloger och certifikat. Detta gör program som körs, även i en delad värdmiljö, säkrare från varandra.

Programmanifestet deklarerar de säkerhetsobjekt (användare och grupper) som krävs för att köra tjänsten/tjänsterna och säkra resurser.  Dessa säkerhetsobjekt refereras i principer, till exempel principer för körning, slutpunktsbindning, paketdelning eller principer för säkerhetsåtkomst.  Principer tillämpas sedan på tjänstresurser i avsnittet **ServiceManifestImport** i programmanifest.

När du deklarerar huvudnamn kan du också definiera och skapa användargrupper så att en eller flera användare kan läggas till i varje grupp som ska hanteras tillsammans. Detta är användbart när det finns flera användare för olika tjänsttransaktionspunkter och de måste ha vissa vanliga privilegier som är tillgängliga på gruppnivå.

Som standard körs Service Fabric-program under kontot som Fabric.exe-processen körs under. Service Fabric ger också möjlighet att köra program under ett lokalt användarkonto eller lokalt systemkonto, vilket anges i programmanifestet. Mer information finns i [Kör en tjänst som ett lokalt användarkonto eller ett lokalt systemkonto](service-fabric-application-runas-security.md).  Du kan också [köra ett tjänststartskript som ett lokalt användar- eller systemkonto](service-fabric-run-script-at-service-startup.md).

När du kör Service Fabric i ett fristående Windows-kluster kan du köra en tjänst under [Active Directory-domänkonton](service-fabric-run-service-as-ad-user-or-group.md) eller [grupphanterade tjänstkonton](service-fabric-run-service-as-gmsa.md).

## <a name="secure-containers"></a>Säkra container
Service Fabric tillhandahåller en mekanism för tjänster i en behållare för åtkomst till ett certifikat som är installerat på noderna i ett Windows- eller Linux-kluster (version 5.7 eller senare). Detta PFX-certifikat kan användas för att autentisera programmet eller tjänsten eller säker kommunikation med andra tjänster. Mer information finns i [Importera ett certifikat till en behållare](service-fabric-securing-containers.md).

Dessutom stöder Service Fabric även gMSA (grupphanterade tjänstkonton) för Windows-behållare. Mer information finns i [Konfigurera gMSA för Windows-behållare](service-fabric-setup-gmsa-for-windows-containers.md).

## <a name="secure-service-communication"></a>Säker servicekommunikation
I Service Fabric körs en tjänst någonstans i ett Service Fabric-kluster, vanligtvis distribuerad över flera virtuella datorer. Service Fabric erbjuder flera alternativ för att säkra din servicekommunikation.

Du kan aktivera HTTPS-slutpunkter i dina [ASP.NET Core- eller Java-webbtjänster.](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service)

Du kan upprätta en säker anslutning mellan omvänd proxy och tjänster, vilket möjliggör en på säker kanal. Anslutning till säkra tjänster stöds endast när omvänd proxy är konfigurerad för att lyssna på HTTPS. Information om hur du konfigurerar den omvända proxyn finns [i Omvänd proxy i Azure Service Fabric](service-fabric-reverseproxy.md).  [Anslut till en säker tjänst](service-fabric-reverseproxy-configure-secure-communication.md) beskriver hur du upprättar en säker anslutning mellan omvänd proxy och tjänster.

Reliable Services-programramverket innehåller några fördefinierade kommunikationsstackar och verktyg som du kan använda för att förbättra säkerheten. Läs om hur du förbättrar säkerheten när du använder tjänståterbefordring (i [C#](service-fabric-reliable-services-secure-communication.md) eller [Java)](service-fabric-reliable-services-secure-communication-java.md)eller använder [WCF](service-fabric-reliable-services-secure-communication-wcf.md).

## <a name="encrypt-application-data-at-rest"></a>Kryptera programdata i vila
Varje [nodtyp](service-fabric-cluster-nodetypes.md) i ett Service Fabric-kluster som körs i Azure backas upp av en [skalningsuppsättning för virtuella datorer](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Genom att använda en Azure Resource Manager-mall, kan du ansluta datadiskar till skalningsuppsättningen som utgör Service Fabric-klustret.  Om dina tjänster sparar data på en bifogad datadisk kan du [kryptera dessa datadiskar](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md) för att skydda dina programdata.

<!--TO DO: Enable BitLocker on Windows standalone clusters?
TO DO: Encrypt disks on Linux clusters?-->


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg
* [Köra ett installationsskript vid start av tjänsten](service-fabric-run-script-at-service-startup.md)
* [Ange resurser i ett tjänstmanifest](service-fabric-service-manifest-resources.md)
* [Distribuera ett program](service-fabric-deploy-remove-applications.md)
* [Lär dig mer om klustersäkerhet](service-fabric-cluster-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/general/overview.md
[config-package]: service-fabric-application-and-service-manifests.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-and-service-security/overview.png