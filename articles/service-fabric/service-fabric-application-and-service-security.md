---
title: Lär dig mer om säkerhet för Azure Service Fabric-program | Microsoft Docs
description: En översikt över hur du kör på ett säkert sätt mikrotjänstprogram på Service Fabric. Lär dig hur du kör skriptet för tjänster och Start under olika konton, autentisera och auktorisera användare, hantera programhemligheter, säkra kommunikationer, Använd en API-gateway och säker data i vila.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/16/2018
ms.author: ryanwi
ms.openlocfilehash: 91e7fdd215d246156f601d3b5e6e05b7f8f71f59
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56116464"
---
# <a name="service-fabric-application-and-service-security"></a>Service Fabric-program- och Tjänstsäkerhet
En arkitektur för mikrotjänster kan ge [många fördelar](service-fabric-overview-microservices.md). Hantering av säkerhet för mikrotjänster, men är en utmaning och skiljer sig från traditionella oflexibla tillämpningar säkerhetshantering. 

Programmet körs vanligtvis på en eller flera servrar i ett nätverk med en gammal och det är lättare att identifiera de portar och API: er och IP-adress. Det finns ofta ett perimeternätverk eller gräns och en databas som ska skydda. Om systemet har komprometterats på grund av en säkerhetsbrist eller en attack är det troligt att allt i systemet är tillgängliga för angriparen. Med mikrotjänster är systemet mer komplex.  Tjänster decentraliserad och distribueras över flera värdar och migrera mellan värdar.  Med rätt säkerhet begränsa du de behörigheter som en angripare kan få och mängden data som är tillgängliga i en enda attack av brott mot en tjänst.  Kommunikation är inte internt, men det sker via ett nätverk och det finns många portar och samverkan mellan tjänster. Att känna till vad dessa tjänstens interaktioner är och när de inträffar är avgörande för ditt programsäkerhet.

Den här artikeln är inte en guide till mikrotjänster security, det finns många resurser tillgängliga online, men beskrivs hur olika aspekter av säkerhet kan utföras i Service Fabric.

## <a name="authentication-and-authorization"></a>Autentisering och auktorisering
Det krävs ofta för resurser och API: er visas av en tjänst ska begränsas till vissa betrodda användare eller klienter. Autentisering är processen för att på ett tillförlitligt sätt fastställa användarens identitet.  Auktorisering är den process som gör API: er eller tjänster tillgängliga för några autentiserad användare, men inte för andra.

### <a name="authentication"></a>Authentication
Det första steget att göra API-nivå förtroendebeslut är autentisering. Autentisering är processen för att på ett tillförlitligt sätt fastställa användarens identitet.  I scenarier för mikrotjänster hanteras autentisering vanligtvis centralt. Om du använder en API-Gateway, kan du [avlasta autentisering](/azure/architecture/patterns/gateway-offloading) till gatewayen. Om du använder den här metoden kan du se till att enskilda tjänster inte kan nås direkt (utan API-Gateway), såvida inte ytterligare säkerhet är på plats för att autentisera meddelanden om de kommer från gatewayen och eller inte.

Om tjänster kan nås direkt är en autentiseringstjänst som Azure Active Directory eller en dedikerad autentisering mikrotjänst som fungerar som en säkerhets-och säkerhetstokentjänst (STS) som kan användas för att autentisera användare. Lita på beslut som delas mellan tjänster med säkerhetstoken eller cookies. 

För ASP.NET Core, den primära mekanismen för [autentisering av användare](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/) är ASP.NET Core identitet medlemskapssystem. ASP.NET Core identitet lagrar information om användare (inklusive inloggningsinformation, roller och anspråk) i ett datalager som konfigurerats av utvecklaren. ASP.NET Core identitet har stöd för tvåfaktorautentisering.  Externa autentiseringsproviders stöds också, så att användare kan logga in med befintliga Autentiseringsprocesser från leverantörer som Microsoft, Google, Facebook eller Twitter. 

### <a name="authorization"></a>Auktorisering
När autentisering, tjänster måste du auktorisera användarnas åtkomst eller fastställer vad en användare kan göra. Den här processen kan en tjänst för att göra API: er tillgängliga för vissa autentiserade användare, men inte till alla. Auktorisering är rätvinkliga och oberoende av autentisering, vilket är en process för att fastställa vem en användare är. Autentisering kan skapa en eller flera identiteter för den aktuella användaren.

[ASP.NET Core-auktorisering](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/authorization-net-microservices-web-applications) kan baserat på användarnas roller eller baserat på anpassad princip, vilket kan innebära att granska anspråk eller andra heuristik.

## <a name="restrict-and-secure-access-using-an-api-gateway"></a>Begränsa och säker åtkomst med hjälp av en API-gateway
Molnprogram behöver ofta en klientdelsgateway som enda åtkomstpunkt för ingång för användare, enheter och andra program. En [API-gatewayen](/azure/architecture/microservices/gateway) placerad mellan klienter och tjänster och är startpunkten för alla tjänster som programmet tillhandahåller. Den fungerar som en omvänd proxy, dirigering av begäranden från klienter till tjänster. Det kan också utföra olika övergripande uppgifter, till exempel autentisering och auktorisering, SSL-avslutning och hastighetsbegränsningar. Om du inte distribuera en gateway, måste klienter skickar begäranden direkt till frontend-tjänster.

I Service Fabric kan en gateway vara valfri tillståndslös tjänst som en [ASP.NET Core-program](service-fabric-reliable-services-communication-aspnetcore.md), eller en annan tjänst för ingångstrafik, till exempel [Traefik](https://docs.traefik.io/), [Händelsehubbar](https://docs.microsoft.com/azure/event-hubs/), [IoT-hubb](https://docs.microsoft.com/azure/iot-hub/), eller [Azure API Management](https://docs.microsoft.com/azure/api-management).

API Management integreras direkt med Service Fabric, så att du kan publicera API: er med en omfattande uppsättning routningsregler och backend-Service Fabric-tjänster.  Du kan skydda åtkomsten till backend-tjänster, förhindra DOS-attacker med hjälp av begränsning, eller verifiera API-nycklar, JWT-token, certifikat och andra autentiseringsuppgifter. Mer information, [Service Fabric med Azure API Management-översikt](service-fabric-api-management-overview.md).

## <a name="manage-application-secrets"></a>Hantera programhemligheter
Hemligheter kan vara känslig information, till exempel storage-anslutningssträngar, lösenord och andra värden som inte ska hanteras i oformaterad text. Den här artikeln använder Azure Key Vault för att hantera nycklar och hemligheter. Dock *med* hemligheter i ett program är molnbaserad plattformsagnostiska att tillåta program att distribueras till ett kluster som finns på olika platser.

Det rekommenderade sättet att hantera konfigurationsinställningar för tjänsten sker via [tjänsten konfigurationspaket][config-package]. Konfigurationspaket för namnges och uppdateras via hanterade löpande uppgraderingar med hälsovalideringsutdrag och automatiska återställningen. Detta är prioriterade för global konfiguration eftersom det minskar risken för ett avbrott i tjänsten för global. Krypterade hemligheter är inget undantag. Service Fabric finns funktioner för att kryptera och dekryptera värden i en paketet Settings.xml konfigurationsfil med hjälp av för certifikatkryptering.

Följande diagram illustrerar det grundläggande flödet för hemlighetshantering i ett Service Fabric-program:

![Översikt över hemlighetshantering][overview]

Det finns fyra steg i det här flödet:

1. Hämta en chiffreringscertifikatet för data.
2. Installera certifikatet i klustret.
3. Kryptera hemliga värden när du distribuerar ett program med certifikatet och placera dem i en tjänst Settings.xml konfigurationsfilen.
4. Läsa krypterade värden utanför Settings.xml genom att dekryptera med samma chiffreringscertifikatet. 

[Azure Key Vault] [ key-vault-get-started] används här som en säker lagringsplats för certifikat och som ett sätt att få certifikat installerade på Service Fabric-kluster i Azure. Om du inte distribuerar till Azure, behöver du inte använda Key Vault för att hantera hemligheter i Service Fabric-program.

Ett exempel finns i [hantera programhemligheter](service-fabric-application-secret-management.md).

## <a name="secure-the-hosting-environment"></a>Skydda den faktiska driftsmiljön
Med hjälp av Azure Service Fabric kan skydda du program som körs i klustret under olika användarkonton. Service Fabric kan du också skydda resurser som används av program vid tidpunkten för distribution under användarkonton – till exempel, filer, kataloger och certifikat. Detta gör program som körs, även i en delad miljö, säkrare från varandra.

Applikationsmanifestet deklarerar säkerhetsobjekt (användare och grupper) krävs kör tjänsterna och säkra resurser.  Dessa säkerhet huvudnamn refereras till i principer, till exempel kör-som slutpunkten som binder, paketera dela eller säkerhetsprinciper för åtkomst.  Principer som sedan används för service-resurser i den **ServiceManifestImport** avsnittet i programmanifestet.

När du deklarerar huvudkonton du också definiera och skapa användargrupper så att en eller flera användare kan läggas till varje grupp som ska hanteras tillsammans. Detta är användbart när det finns flera användare för olika startpunkter och de behöver för att ha vissa vanliga behörigheter som är tillgängliga på gruppnivå.

Service Fabric-program körs under kontot som Fabric.exe processen körs under som standard. Service Fabric ger också möjlighet att köra program under ett lokalt användarkonto eller kontot Lokalt system som har angetts i manifestet. Mer information finns i [köra tjänster som ett lokalt användarkonto eller kontot Lokalt system](service-fabric-application-runas-security.md).  Du kan också [kör ett startskript för tjänsten som ett lokalt konto för användaren eller systemet](service-fabric-run-script-at-service-startup.md).

När du använder Service Fabric på ett fristående kluster för Windows, kan du köra en tjänst under [Active Directory-domänkonton](service-fabric-run-service-as-ad-user-or-group.md) eller [gruppen hanterade tjänstkonton](service-fabric-run-service-as-gmsa.md).

## <a name="secure-containers"></a>Säkra container
Service Fabric tillhandahåller en mekanism för tjänster i en behållare för att få åtkomst till ett certifikat som installeras på noderna i ett Windows- eller Linux-kluster (version 5.7 eller högre). Den här PFX-certifikat kan användas för att autentisera programmet eller tjänsten eller säker kommunikation med andra tjänster. Mer information finns i [importera ett certifikat till en behållare](service-fabric-securing-containers.md).

Service Fabric stöder dessutom också gMSA (grupphanterade tjänstkonton) för Windows-behållare. Mer information finns i [konfigurera gMSA för Windows-behållare](service-fabric-setup-gmsa-for-windows-containers.md).

## <a name="secure-service-communication"></a>Säker kommunikation
I Service Fabric körs en tjänst någonstans i ett Service Fabric-kluster, vanligtvis fördelade på flera virtuella datorer. Service Fabric tillhandahåller flera alternativ för att skydda din tjänst-kommunikation.

Du kan aktivera HTTPS-slutpunkter i din [ASP.NET Core- eller Java](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) webbtjänster.

Du kan upprätta säker anslutning mellan omvänd proxy och tjänster, vilket gör att en säker kanal från slutpunkt till slutpunkt. Ansluter till säker tjänster stöds endast när omvänd proxy är konfigurerad för att lyssna på HTTPS. Information om hur du konfigurerar den omvända proxyn finns [omvänd proxy i Azure Service Fabric](service-fabric-reverseproxy.md).  [Ansluta till en säker tjänst](service-fabric-reverseproxy-configure-secure-communication.md) beskriver hur du upprättar säker anslutning mellan omvänd proxy och tjänster.

Reliable Services application framework innehåller några fördefinierade kommunikation stackar och verktyg som du kan använda för att förbättra säkerheten. Lär dig att förbättra säkerheten när du använder en fjärrtjänst (i [ C# ](service-fabric-reliable-services-secure-communication.md) eller [Java](service-fabric-reliable-services-secure-communication-java.md)) eller med hjälp av [WCF](service-fabric-reliable-services-secure-communication-wcf.md).

## <a name="encrypt-application-data-at-rest"></a>Kryptera programdata i vila
Varje [nodtyp](service-fabric-cluster-nodetypes.md) i ett Service Fabric-kluster som körs i Azure backas upp av en [virtual machine scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Genom att använda en Azure Resource Manager-mall, kan du ansluta datadiskar till skalningsuppsättningen som utgör Service Fabric-klustret.  Om dina tjänster spara data till en ansluten datadisk, kan du [kryptera dessa datadiskar](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md) att skydda dina programdata.

<!--TO DO: Enable BitLocker on Windows standalone clusters?
TO DO: Encrypt disks on Linux clusters?-->


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg
* [Köra ett installationsskript när tjänsten startar](service-fabric-run-script-at-service-startup.md)
* [Ange resurser i ett tjänstmanifest](service-fabric-service-manifest-resources.md)
* [Distribuera ett program](service-fabric-deploy-remove-applications.md)
* [Lär dig mer om Klustersäkerhet](service-fabric-cluster-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-overview.md
[config-package]: service-fabric-application-and-service-manifests.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-and-service-security/overview.png