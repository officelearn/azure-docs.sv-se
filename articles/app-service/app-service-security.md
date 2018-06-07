---
title: Säkerhet i Azure App Service och Azure Functions | Microsoft Docs
description: Lär dig hur Apptjänst hjälper dig att skydda din app och hur du ytterligare låsa appen mot hot.
keywords: Azure apptjänst, webbprogram, mobilappar, api-app, funktionsapp, säkerhet, säker, skyddad, godkännande, kompatibla, certifikat, certifikat, https, ftps tls, förtroende, kryptering, kryptera, krypterad IP-begränsning, autentisering, auktorisering, authn, autho, msi, hanterade tjänstidentiteten, hemligheter, hemlighet, korrigering, korrigering, korrigeringsfiler, version, isolering, isolering av nätverk, ddos, mitm
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: cephalin
ms.openlocfilehash: 2ca1c1518589e60a03570e1c2063381f749ed9aa
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655368"
---
# <a name="security-in-azure-app-service-and-azure-functions"></a>Säkerhet i Azure App Service och Azure Functions

Den här artikeln beskrivs hur du [Azure App Service](app-service-web-overview.md) skyddar ditt webbprogram, serverdelen för mobilappen, API-app och [Azure Functions](/azure/azure-functions/). Här visas också hur du kan skydda din app med de inbyggda funktionerna för Apptjänst ytterligare.

Platform-komponenter av Apptjänst, bland annat virtuella Azure-datorer, lagring, nätverksanslutningar, web ramverk, hantering och integrering funktioner är aktivt skyddade och blivit hårdare. Apptjänst passerar kraftig kompatibilitetskontroller på kontinuerligt för att se till att:

- Appen resurser är [skyddade](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) från andra kunders Azure-resurser.
- [VM-instanser och runtime programvara uppdateras regelbundet](app-service-patch-os-runtime.md) för adress nyligen identifierade säkerhetsrisker. 
- Överföring av hemligheter (till exempel anslutningssträngar) mellan appen och andra Azure-resurser (t.ex [SQL-databas](/services/sql-database/)) finns kvar i Azure och inte mellan några nätverksgränser. Hemligheter krypteras alltid när lagras.
- All kommunikation över App Service-anslutning funktioner som [hybridanslutning](app-service-hybrid-connections.md), krypteras. 
- Anslutningar med verktyg för fjärrhantering som Azure PowerShell, Azure CLI, Azure SDK: er, REST-API: er, krypteras alla.
- 24-timmarsformat threat management skyddar infrastruktur och plattform mot skadlig kod, distribuerade denial of service (DDoS), man-in-the-middle (MITM) och andra hot.

Mer information om infrastruktur och plattform säkerhet i Azure finns [Azure Säkerhetscenter](https://azure.microsoft.com/overview/trusted-cloud/).

Följande avsnitt visar hur du ytterligare skydda din Apptjänst-app från hot.

## <a name="https-and-certificates"></a>HTTPS- och certifikat

Med Apptjänst kan du skydda dina appar med [HTTPS](https://wikipedia.org/wiki/HTTPS). När din app skapas, dess standarddomännamnet (\<programnamn >. azurewebsites.net) redan är tillgänglig via HTTPS. Om du [konfigurera en anpassad domän för din app](app-service-web-tutorial-custom-domain.md), bör du även [skydda den med ett anpassat certifikat](app-service-web-tutorial-custom-ssl.md) så att klientwebbläsare kan göra säkra HTTPS-anslutningar till domänen. Det finns två sätt att göra det:

- **Certifikat för App Service** -skapa ett certifikat direkt i Azure. Certifikatet är skyddad i [Azure Key Vault](/azure/key-vault/), och kan importeras till din Apptjänst-app. Mer information finns i [köpa och konfigurera ett SSL-certifikat för Azure App Service](web-sites-purchase-ssl-web-site.md).
- **Certifikat från tredje part** – ladda upp ett anpassat SSL-certifikat som du har köpt från en betrodd certifikatutfärdare och kopplar den till din Apptjänst-app. Apptjänst stöder både domän certifikat och jokerteckencertifikat. Det stöder också självsignerade certifikat för testning. Mer information finns i [binda ett befintligt anpassat SSL-certifikat till Azure Web Apps](app-service-web-tutorial-custom-ssl.md).

## <a name="insecure-protocols-http-tls-10-ftp"></a>Osäkra protokoll (HTTP, TLS 1.0 FTP)

Om du vill skydda appen mot alla okrypterad (HTTP)-anslutningar tillhandahåller App Service en enda klickning konfiguration för att använda HTTPS. Oskyddat begäranden aktiverad direkt innan de når även din programkod. Mer information finns i [genomdriva HTTPS](app-service-web-tutorial-custom-ssl.md#enforce-https).

[TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0 är inte längre anses vara skyddade av branschstandarder, som [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). App Service kan du inaktivera inaktuella protokoll av [tvingande TLS 1.1/1.2](app-service-web-tutorial-custom-ssl.md#enforce-tls-1112).

Apptjänst stöder både FTP och FTPS för distribution av dina filer. Dock ska FTPS användas i stället för FTP, om det är möjligt. När en eller båda av dessa protokoll inte används, bör du [inaktivera dem](app-service-deploy-ftp.md#enforce-ftps).

## <a name="static-ip-restrictions"></a>Statiska IP-adressbegränsningar

Apptjänst-app accepterar begäranden från alla IP-adresser från internet som standard, men du kan begränsa den åtkomsten till en liten del av IP-adresser. App Service i Windows kan du definiera en lista över IP-adresser som har tillgång till din app. Listan över tillåtna kan inkludera enskilda IP-adresser eller ett intervall med IP-adresser som definieras av en nätmask. Mer information finns i [Azure App Service statiska IP-begränsningar](app-service-ip-restrictions.md).

För användning i Windows, du kan också begränsa IP-adresser dynamiskt genom att konfigurera den _web.config_. Mer information finns i [dynamisk IP-säkerhet <dynamicIpSecurity> ](https://docs.microsoft.com/iis/configuration/system.webServer/security/dynamicIpSecurity/).

## <a name="client-authentication-and-authorization"></a>Klientautentisering och auktorisering

Azure App Service tillhandahåller nyckelfärdig autentisering och auktorisering av användare eller klienten appar. När aktiverat Logga den in användare och klientappar med lite eller ingen programkod. Du kan implementera din egen lösning för autentisering och auktorisering, eller att Apptjänst ska hantera du i stället. Autentisering och auktorisering modulen hanterar webbförfrågningar innan du skickar dem till din programkod och den nekar obehöriga begäranden innan de når din kod.

Användning-autentisering och auktorisering stöd för flera autentiseringsproviders, inklusive Azure Active Directory, Microsoft-konton, Facebook, Google och Twitter. Mer information finns i [autentisering och auktorisering i Azure App Service](app-service-authentication-overview.md).

## <a name="service-to-service-authentication"></a>Tjänst-till-tjänst-autentisering

Vid autentisering mot en backend tjänst tillhandahåller App Service två olika metoder beroende på dina behov:

- **Tjänsten identitet** -logga in till fjärresursen identiteten för själva appen. Med Apptjänst kan du enkelt skapa en [hanterade tjänstidentiteten](app-service-managed-service-identity.md), som du kan använda för att autentisera med andra tjänster som [Azure SQL Database](/azure/sql-database/) eller [Azure Key Vault](/azure/key-vault/). En slutpunkt till slutpunkt med den här metoden finns [säker Azure SQL Database-anslutningen från App Service med hanterade tjänstidentiteten](app-service-web-tutorial-connect-msi.md).
- **On-behalf-of (OBO)** -Se delegerad åtkomst till fjärresurser för användarens räkning. Med Azure Active Directory som autentiseringsprovider App Service-appen kan utföra delegerad logga in på en fjärrtjänsten som [Azure Active Directory Graph API](../active-directory/develop/active-directory-graph-api.md) eller en fjärransluten API-app i App Service. En slutpunkt till slutpunkt med den här metoden finns [autentisera och auktorisera användare slutpunkt till slutpunkt i Azure App Service](app-service-web-tutorial-auth-aad.md).

## <a name="connectivity-to-remote-resources"></a>Anslutningen till fjärresurser

Det finns tre typer av fjärranslutna resurser som din app kan behöva åtkomst till: 

- [Azure-resurser](#azure-resources)
- [Resurser i Azure-nätverk](#resources-inside-an-azure-virtual-network)
- [Lokala resurser](#on-premises-resources)

I dessa fall App Service tillhandahåller ett sätt att skapa säkra anslutningar, men du bör ändå Se säkerhetsmetoder. Exempelvis kan alltid använda krypterade anslutningar även om backend-resursen tillåter okrypterade anslutningar. Kontrollera dessutom att din serverdel i Azure-tjänsten tillåter minsta uppsättning IP-adresser. Du kan hitta de utgående IP-adresserna för din app på [inkommande och utgående IP-adresser i Azure App Service](app-service-ip-addresses.md).

### <a name="azure-resources"></a>Azure-resurser

När appen ansluter till Azure-resurser, exempelvis [SQL-databas](/services/sql-database/) och [Azure Storage](/azure/storage/), anslutningen håller sig inom Azure och inte mellan några nätverksgränser. Men anslutningen går igenom delade nätverk i Azure, så ska du kontrollera att anslutningen är krypterad. 

Om din app finns i en [apptjänstmiljö](environment/intro.md), bör du [ansluta till stöd för Azure-tjänster med hjälp av virtuellt nätverk Tjänsteslutpunkter](../virtual-network/virtual-network-service-endpoints-overview.md).

### <a name="resources-inside-an-azure-virtual-network"></a>Resurser i Azure-nätverk

Appen kan komma åt resurser i en [Azure Virtual Network](/azure/virtual-network/) via [virtuell nätverksintegration](web-sites-integrate-with-vnet.md). Integrationen upprättas med ett virtuellt nätverk med en punkt-till-plats-VPN. Appen kan sedan komma åt resurser i det virtuella nätverket som använder sina privata IP-adresser. Punkt-till-plats-anslutning passerar dock fortfarande delade nätverk i Azure. 

Om du vill isolera resurs anslutningen helt från de delade nätverk i Azure, skapa din app i en [apptjänstmiljö](environment/intro.md). Anslutningen mellan appen och resurser i det virtuella nätverket är helt isolerade eftersom en Apptjänst-miljö alltid har distribuerats till ett dedikerat virtuellt nätverk. Andra aspekter av nätverkssäkerhet i en Apptjänst-miljö finns i [Nätverksisolering](#network-isolation).

### <a name="on-premises-resources"></a>Lokala resurser

Du kan på ett säkert sätt komma åt lokala resurser, t.ex databaser, på tre sätt: 

- [Hybridanslutningar](app-service-hybrid-connections.md) -upprättar en PPP-anslutning till en fjärresurs via en TCP-tunnel. TCP-tunneln upprättas med hjälp av TLS 1.2 med delad åtkomst (SAS)-signaturnycklar.
- [Virtuell nätverksintegration](web-sites-integrate-with-vnet.md) med plats-till-plats-VPN - som beskrivs i [resurser i ett Azure Virtual Network](#resources-inside-an-azure-virtual-network), men det virtuella nätverket kan anslutas till ett lokalt nätverk via en [ plats-till-plats VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). I det här nätverkets topologi, kan din app ansluta till lokala resurser som andra resurser i det virtuella nätverket.
- [Apptjänst-miljö](environment/intro.md) med plats-till-plats-VPN - som beskrivs i [resurser i ett Azure Virtual Network](#resources-inside-an-azure-virtual-network), men det virtuella nätverket kan anslutas till ett lokalt nätverk via en [plats-till-plats VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). I det här nätverkets topologi, kan din app ansluta till lokala resurser som andra resurser i det virtuella nätverket.

## <a name="application-secrets"></a>Programmet hemligheter

Lagra inte programmet hemligheter, till exempel autentiseringsuppgifter på databasen, API-token och privata nycklar i kod eller konfigurationsfil filerna. Ofta metod är att komma åt dem som [miljövariabler](https://wikipedia.org/wiki/Environment_variable) med det vanliga mönstret på ditt språk väljer. I App Service sättet att definiera miljövariabler är via [appinställningar](web-sites-configure.md#app-settings) (och, särskilt för .NET-program, [anslutningssträngar](web-sites-configure.md#connection-strings)). App-inställningar och anslutningssträngar lagras krypterade i Azure och de är dekrypteras endast innan som läggs in i din app processminne när appen startar. Krypteringsnycklarna roteras regelbundet.

Alternativt kan du integrera din Apptjänst-app med [Azure Key Vault](/azure/key-vault/) för hantering av avancerade hemligheter. Av [åtkomst till Nyckelvalvet med hanterade tjänstidentiteten](../key-vault/tutorial-web-application-keyvault.md), Apptjänst-app på ett säkert sätt kan komma åt hemligheter som du behöver.

## <a name="network-isolation"></a>Isolering av nätverk

Undantag för den **isolerad** prisnivån, alla nivåer köra dina appar på den delade nätverksinfrastrukturen i App Service. Till exempel delas den offentliga IP-adresser och frontend belastningsutjämnare med andra klienter. Den **isolerad** nivån får du fullständiga nätverksisolering genom att köra dina appar i ett dedikerat [apptjänstmiljö](environment/intro.md). En Apptjänst-miljö som körs i egna instans av [Azure Virtual Network](/azure/virtual-network/). Det kan du: 

- Begränsa åtkomst till nätverket med [nätverkssäkerhetsgrupper](../virtual-network/virtual-networks-nsg.md). 
- Hantera dina appar via en dedikerad offentlig slutpunkt med dedikerade frontwebbservrarna.
- Hantera interna program med hjälp av en intern belastningsutjämnare (ILB), vilket ger åtkomst från i ditt Azure-nätverk. ILB har en IP-adress från ditt privata undernät, vilket ger totalt isolering av dina appar från internet.
- [Använd en ILB bakom en brandvägg för webbaserade program (Brandvägg)](environment/integrate-with-application-gateway.md). Brandvägg ger skydd på företagsnivå till din offentliga program, till exempel DDoS-skydd, URI-filtrering och SQL injection förebyggande.

Mer information finns i [introduktion till Azure App Service-miljöer](environment/intro.md).