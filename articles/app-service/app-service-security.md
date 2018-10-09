---
title: Säkerhet i Azure App Service och Azure Functions | Microsoft Docs
description: Läs mer om hur Apptjänst hjälper att säkra din app och hur du kan ytterligare låsa appen från hot.
keywords: Azure apptjänst, webbapp, mobilapp, api-app, funktionsapp, säkerhet, säker och skyddad, efterlevnad, kompatibla, certifikat, certifikat, https, ftps tls, förtroende, kryptering, kryptera, krypterad ip-begränsning, autentisering, auktorisering, authn, autho, msi, hanterad tjänstidentitet, hanterad identitet, hemligheter, hemlighet, korrigering, uppdatering, korrigeringar, version, isolering, isolering av nätverk, ddos, mitm
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
ms.date: 08/24/2018
ms.author: cephalin
ms.openlocfilehash: 3bacc2bf253a6b8c3b869b7a6d4952d982de3ee6
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48857507"
---
# <a name="security-in-azure-app-service-and-azure-functions"></a>Säkerhet i Azure App Service och Azure Functions

Den här artikeln visar hur [Azure App Service](app-service-web-overview.md) hjälper dig att skydda din webbapp, mobilapp-serverdel, API-app och [Azure Functions](/azure/azure-functions/). Den visar även hur du kan öka skyddet din app med de inbyggda funktionerna för App Service.

Plattformskomponenter i App Service, inklusive Azure virtuella datorer, lagring, nätverk, webbramverk, hantering och integration funktioner, är aktivt skyddade och härdade. App Service går igenom kraftig efterlevnadskontroller på kontinuerligt för att se till att:

- Appens resurser är [säker](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) från andra kunders Azure-resurser.
- [VM-instanser och runtime programvara uppdateras regelbundet](app-service-patch-os-runtime.md) för säkerhetsrisker i adressen som nyligen identifierats. 
- Kommunikation av hemligheter (till exempel anslutningssträngar) mellan din app och andra Azure-resurser (till exempel [SQL Database](https://azure.microsoft.com/services/sql-database/)) stannar kvar i Azure och inte mellan alla nätverksgränser. Hemligheter som krypteras alltid när lagras.
- All kommunikation över App Service-anslutning funktioner som [hybridanslutning](app-service-hybrid-connections.md), krypteras. 
- Anslutningar med verktyg för fjärrhantering som Azure PowerShell, Azure CLI, Azure SDK: er, REST API: er, krypteras alla.
- 24-timmarsformat threat management skyddar infrastruktur och plattform mot skadlig kod, distributed denial of service (DDoS), man-in-the-middle (MITM) och andra hot.

Mer information om säkerhet för infrastruktur och plattform i Azure finns i [Azure Trust Center](https://azure.microsoft.com/overview/trusted-cloud/).

I följande avsnitt visas hur ytterligare skydda dina App Service-app från hot.

## <a name="https-and-certificates"></a>HTTPS och certifikat

App Service kan du skydda dina appar med [HTTPS](https://wikipedia.org/wiki/HTTPS). När din app skapas, dess standarddomännamnet (\<app_name >. azurewebsites.net) redan är tillgänglig via HTTPS. Om du [konfigurera ett anpassat domännamn för din app](app-service-web-tutorial-custom-domain.md), bör du också [skydda den med ett anpassat certifikat](app-service-web-tutorial-custom-ssl.md) så att klientwebbläsare kan göra säker HTTPS-anslutningar till din anpassade domän. Det finns två sätt att göra det:

- **App Service certificate** -skapa ett certifikat direkt i Azure. Certifikatet är skyddad i [Azure Key Vault](/azure/key-vault/), och kan importeras till din App Service-app. Mer information finns i [köp och konfigurera ett SSL-certifikat för Azure App Service](web-sites-purchase-ssl-web-site.md).
- **Certifikat från tredje parts** – ladda upp ett anpassat SSL-certifikat som du har köpt från en betrodd certifikatutfärdare och kopplar den till App Service-appen. App Service har stöd för både domän certifikat och jokerteckencertifikat. Det stöder även självsignerade certifikat för testning. Mer information finns i [binda ett befintligt anpassat SSL-certifikat till Azure Web Apps](app-service-web-tutorial-custom-ssl.md).

## <a name="insecure-protocols-http-tls-10-ftp"></a>Säkert protokoll (HTTP, TLS 1.0, FTP)

App Service tillhandahåller ett klick konfigurationen för att använda HTTPS för att skydda din app mot alla okrypterade (HTTP)-anslutningar. Oskyddat begäranden aktiverade direkt innan de når även din programkod. Mer information finns i [framtvinga HTTPS](app-service-web-tutorial-custom-ssl.md#enforce-https).

[TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0 är inte längre anses som säkert enligt branschstandarder, t.ex [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). App Service kan du inaktivera inaktuella protokoll genom [tvingande TLS 1.1/1.2](app-service-web-tutorial-custom-ssl.md#enforce-tls-versions).

App Service stöder både FTP och FTPS för att distribuera dina filer. Dock ska FTPS användas i stället för FTP, om det är möjligt. När en eller båda av dessa protokoll inte används, bör du [inaktivera dem](app-service-deploy-ftp.md#enforce-ftps).

## <a name="static-ip-restrictions"></a>Statisk IP-adressbegränsningar

App Service-appen tar emot förfrågningar från alla IP-adresser från internet som standard, men du kan begränsa den åtkomsten till en liten del av IP-adresser. App Service i Windows kan du definiera en lista över IP-adresser som ska kunna komma åt din app. Listan över tillåtna kan inkludera enskilda IP-adresser eller ett intervall med IP-adresser som definieras av en nätmask. Mer information finns i [Azure App Service statiska IP-begränsningar](app-service-ip-restrictions.md).

För App Service i Windows, du kan också begränsa IP-adresser dynamiskt genom att konfigurera den _web.config_. Mer information finns i [dynamisk IP-säkerhet <dynamicIpSecurity> ](https://docs.microsoft.com/iis/configuration/system.webServer/security/dynamicIpSecurity/).

## <a name="client-authentication-and-authorization"></a>Klientautentisering och auktorisering

Azure App Service tillhandahåller nyckelfärdig autentisering och auktorisering av användare eller klient apps. När aktiverad, kan den logga in användare eller klientprogram med lite eller ingen programkod. Du kan implementera en egen lösning för autentisering och auktorisering eller låta App Service för att hantera för dig i stället. Autentisering och auktorisering modulen hanterar begäranden innan du skickar dem till din programkod och det hindrar obehöriga begäranden innan de når din kod.

App Service-autentisering och auktorisering stöd för flera autentiseringsproviders, inklusive Azure Active Directory, Microsoft-konton, Facebook, Google och Twitter. Mer information finns i [autentisering och auktorisering i Azure App Service](app-service-authentication-overview.md).

## <a name="service-to-service-authentication"></a>Tjänst-till-tjänst-autentisering

Vid autentisering mot en backend tjänst, tillhandahåller App Service två olika sätt beroende på dina behov:

- **Tjänsten identitet** -inloggning på fjärransluten resurs med hjälp av identiteten för själva appen. App Service kan du enkelt skapa en [hanterad identitet](app-service-managed-service-identity.md), som du kan använda för att autentisera med andra tjänster, till exempel [Azure SQL Database](/azure/sql-database/) eller [Azure Key Vault](/azure/key-vault/). En självstudiekurs om slutpunkt till slutpunkt på den här metoden finns i [skydda Azure SQL Database-anslutningar från App Service med en hanterad identitet](app-service-web-tutorial-connect-msi.md).
- **On-behalf-of (OBO)** -gör delegerad åtkomst till fjärresurser för användarens räkning. Med Azure Active Directory som autentiseringsprovider, App Service-appen kan utföra delegerad inloggning till en fjärrtjänst som [Azure Active Directory Graph API](../active-directory/develop/active-directory-graph-api.md) eller en fjärransluten API-app i App Service. En självstudiekurs om slutpunkt till slutpunkt på den här metoden finns i [autentisera och auktorisera användare slutpunkt till slutpunkt i Azure App Service](app-service-web-tutorial-auth-aad.md).

## <a name="connectivity-to-remote-resources"></a>Anslutningen till fjärranslutna resurser

Det finns tre typer av fjärranslutna resurser som din app kan behöva åtkomst till: 

- [Azure-resurser](#azure-resources)
- [Resurser i Azure-nätverk](#resources-inside-an-azure-virtual-network)
- [Lokala resurser](#on-premises-resources)

App Service gör det möjligt för dig att skapa säkra anslutningar i var och en av dessa fall, men du bör fortfarande tillämpa rekommenderade säkerhetsmetoder. Exempelvis kan alltid använda krypterade anslutningar även om backend-resursen tillåter okrypterade anslutningar. Kontrollera dessutom att din serverdel Azure-tjänsten tillåter minsta uppsättning IP-adresser. Du kan hitta de utgående IP-adresserna för din app på [inkommande och utgående IP-adresser i Azure App Service](app-service-ip-addresses.md).

### <a name="azure-resources"></a>Azure-resurser

När din app ansluter till Azure-resurser, till exempel [SQL Database](https://azure.microsoft.com/services/sql-database/) och [Azure Storage](/azure/storage/), anslutningen håller sig inom Azure och inte mellan alla nätverksgränser. Men anslutningen går igenom det delade nätverket i Azure, så du måste alltid se till att anslutningen är krypterad. 

Om din app finns i en [apptjänstmiljö](environment/intro.md), bör du [ansluta till Azure-tjänster med virtuella nätverksslutpunkter som stöds](../virtual-network/virtual-network-service-endpoints-overview.md).

### <a name="resources-inside-an-azure-virtual-network"></a>Resurser i Azure-nätverk

Appen kan komma åt resurser i en [Azure Virtual Network](/azure/virtual-network/) via [Virtual Network-integration](web-sites-integrate-with-vnet.md). Integrationen upprättas med ett virtuellt nätverk med en punkt-till-plats-VPN. Appen kan sedan komma åt resurser i det virtuella nätverket med hjälp av sina privata IP-adresser. Punkt-till-plats-anslutningen inom dock fortfarande delade nätverk i Azure. 

Om du vill isolera resource anslutningen helt från de delade nätverk i Azure, skapa din app i en [apptjänstmiljö](environment/intro.md). Eftersom App Service environment distribueras alltid till ett dedikerat virtuellt nätverk, är anslutningen mellan din app och resurser i det virtuella nätverket helt isolerade. Andra aspekter av nätverkssäkerheten i App Service environment finns [Nätverksisolering](#network-isolation).

### <a name="on-premises-resources"></a>Lokala resurser

Du kan på ett säkert sätt komma åt lokala resurser, till exempel databaser på tre sätt: 

- [Hybridanslutningar](app-service-hybrid-connections.md) -upprättar en point-to-point-anslutning till din fjärresursen via en TCP-tunnel. TCP-tunneln har upprättats med hjälp av TLS 1.2 med nycklar för signatur för delad åtkomst.
- [Integrering med virtuella nätverk](web-sites-integrate-with-vnet.md) med plats-till-plats-VPN - enligt [resurser i Azure Virtual Network](#resources-inside-an-azure-virtual-network), men det virtuella nätverket kan anslutas till det lokala nätverket via en [ plats-till-plats VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). I det här nätverkets topologi, kan din app ansluta till lokala resurser som andra resurser i det virtuella nätverket.
- [App Service-miljö](environment/intro.md) med plats-till-plats-VPN - enligt [resurser i Azure Virtual Network](#resources-inside-an-azure-virtual-network), men det virtuella nätverket kan anslutas till det lokala nätverket via en [plats-till-plats VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). I det här nätverkets topologi, kan din app ansluta till lokala resurser som andra resurser i det virtuella nätverket.

## <a name="application-secrets"></a>Programhemligheter

Lagra programhemligheter som Databasautentiseringsuppgifter, API-token och privata nycklar i filerna kodning eller inte. Allmänt accepterade metod är att komma åt dem som [miljövariabler](https://wikipedia.org/wiki/Environment_variable) med hjälp av Standardmönstret i valfritt språk. I App Service är sättet att definiera miljövariabler via [appinställningar](web-sites-configure.md#app-settings) (och särskilt för .NET-program, [anslutningssträngar](web-sites-configure.md#connection-strings)). Appinställningar och anslutningssträngar lagras krypterade i Azure och de är dekrypteras endast innan som införs i din app processminne när appen startar. Krypteringsnycklarna roteras regelbundet.

Du kan också integrera din App Service-app med [Azure Key Vault](/azure/key-vault/) för hantering av avancerade hemligheter. Genom att [åtkomst till Nyckelvalv med en hanterad identitet](../key-vault/tutorial-web-application-keyvault.md), App Service-appen på ett säkert sätt kan komma åt hemligheter som du behöver.

## <a name="network-isolation"></a>Isolering av nätverk

Undantag för den **isolerad** prisnivå, alla nivåerna kör dina appar i den delade nätverksinfrastrukturen i App Service. Till exempel delas den offentliga IP-adresser och front-end belastningsutjämnare med andra klienter. Den **isolerad** nivån ger dig fullständig nätverksisolering genom att köra appar i en dedikerad [apptjänstmiljö](environment/intro.md). App Service environment som körs i en egen instans av [Azure Virtual Network](/azure/virtual-network/). Du kan: 

- Begränsa nätverksåtkomst med [nätverkssäkerhetsgrupper](../virtual-network/virtual-networks-dmz-nsg.md). 
- Ge dina appar via en dedikerad offentlig slutpunkt med dedikerad klientdelar.
- Hantera internt program med en intern belastningsutjämnare (ILB), vilket ger åtkomst från i ditt virtuella Azure-nätverk. Den interna Belastningsutjämnaren har en IP-adress från ditt privata undernät, vilket ger totalt isolering av dina appar från internet.
- [Använda en ILB bakom en brandvägg för webbaserade program (WAF)](environment/integrate-with-application-gateway.md). WAF erbjuder företagsnivå skydd för dina offentliga program, till exempel DDoS-skydd, URI-filtrering och SQL-inmatning dataförlustskydd.

Mer information finns i [introduktion till Azure App Service-miljöer](environment/intro.md). 
