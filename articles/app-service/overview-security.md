---
title: Säkerhet
description: Läs mer om hur App Service hjälper till att skydda din app och hur du kan låsa appen ytterligare från hot.
keywords: azure app service, webbapp, mobilapp, api app, funktion app, säkerhet, säker, säker, kompatibel, certifikat, certifikat, https, ftps, tls, förtroende, kryptering, krypterad, ip begränsning, autentisering, auktorisering, authn, autho, msi, hanterad tjänst identitet, hanterad identitet, hemligheter, hemlighet, patch, patchar, version, isolering, nätverksisolering, ddos, mitm
ms.topic: article
ms.date: 08/24/2018
ms.custom: seodec18
ms.openlocfilehash: 8a098b1924bf7c2866f6afd7452b8dd3b93f3109
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535663"
---
# <a name="security-in-azure-app-service"></a>Säkerhet i Azure App Service

Den här artikeln visar hur [Azure App Service](overview.md) hjälper till att skydda din webbapp, på mobilappen, API-appen och [funktionsappen](/azure/azure-functions/). Den visar också hur du kan skydda din app ytterligare med de inbyggda App Service-funktionerna.

Plattformskomponenterna i App Service, inklusive virtuella Azure-datorer, lagring, nätverksanslutningar, webbramverk, hanterings- och integrationsfunktioner, är aktivt säkrade och härdade. App Service genomgår kontinuerliga kontroller av efterlevnaden för att se till att:

- Dina appresurser [skyddas](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) från de andra kundernas Azure-resurser.
- [VM-instanser och körningsprogram uppdateras regelbundet](overview-patch-os-runtime.md) för att åtgärda nyupptäckta säkerhetsproblem. 
- Kommunikation av hemligheter (till exempel anslutningssträngar) mellan din app och andra [Azure-resurser](https://azure.microsoft.com/services/sql-database/)(till exempel SQL Database) stannar inom Azure och korsar inte några nätverksgränser. Hemligheter krypteras alltid när de lagras.
- All kommunikation via App Service-anslutningsfunktionerna, till exempel [hybridanslutning,](app-service-hybrid-connections.md)är krypterad. 
- Anslutningar med fjärrhanteringsverktyg som Azure PowerShell, Azure CLI, Azure SDK: er, REST API:er är alla krypterade.
- 24-timmars hothantering skyddar infrastrukturen och plattformen mot skadlig kod, distribuerad denial-of-service (DDoS), man-in-the-middle (MITM) och andra hot.

Mer information om infrastruktur och plattformssäkerhet i Azure finns i [Azure Trust Center](https://azure.microsoft.com/overview/trusted-cloud/).

I följande avsnitt visas hur du skyddar appappen ytterligare från hot.

## <a name="https-and-certificates"></a>HTTPS och certifikat

Med App Service kan du skydda dina appar med [HTTPS](https://wikipedia.org/wiki/HTTPS). När appen skapas är dess\<standarddomännamn ( app_name>.azurewebsites.net) redan tillgängligt med HTTPS. Om du [konfigurerar en anpassad domän för din app](app-service-web-tutorial-custom-domain.md)bör du också skydda den med ett [TLS/SSL-certifikat](configure-ssl-bindings.md) så att klientwebbläsare kan göra säkra HTTPS-anslutningar till din anpassade domän. Det finns flera typer av certifikat som stöds av App Service:

- Hanterat kostnadsfritt hanterat certifikat för apptjänst
- Certifikat för apptjänst
- Tredjepartscertifikat
- Certifikat som importeras från Azure Key Vault

Mer information finns [i Lägga till ett TLS/SSL-certifikat i Azure App Service](configure-ssl-certificate.md).

## <a name="insecure-protocols-http-tls-10-ftp"></a>Osäkra protokoll (HTTP, TLS 1.0, FTP)

För att skydda din app mot alla okrypterade (HTTP) anslutningar, apptjänst ger ett klick konfiguration för att framtvinga HTTPS. Oprioriterade begäranden avvisas innan de ens når din programkod. Mer information finns i [Framtvinga HTTPS](configure-ssl-bindings.md#enforce-https).

[TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0 anses inte längre vara säker enligt branschstandarder, till exempel [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Med App Service kan du inaktivera inaktuella protokoll genom [att verkställa TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions).

App Service stöder både FTP och FTPS för distribution av dina filer. FTPS bör dock användas i stället för FTP, om det alls är möjligt. När ett eller båda av dessa protokoll inte används bör du [inaktivera dem](deploy-ftp.md#enforce-ftps).

## <a name="static-ip-restrictions"></a>Statiska IP-begränsningar

Som standard accepterar apptjänstappen begäranden från alla IP-adresser från internet, men du kan begränsa åtkomsten till en liten delmängd av IP-adresser. Med App Service i Windows kan du definiera en lista över IP-adresser som har åtkomst till din app. Den tillåtna listan kan innehålla enskilda IP-adresser eller ett intervall med IP-adresser som definieras av en nätmask. Mer information finns i [Statiska IP-begränsningar för Azure App Service](app-service-ip-restrictions.md).

För App Service i Windows kan du också begränsa IP-adresser dynamiskt genom att konfigurera _web.config_. Mer information finns i [Dynamisk IP-säkerhet \<dynamicIpSecurity>](https://docs.microsoft.com/iis/configuration/system.webServer/security/dynamicIpSecurity/).

## <a name="client-authentication-and-authorization"></a>Klientautentisering och auktorisering

Azure App Service tillhandahåller nyckelnyckelautentisering och auktorisering av användare eller klientappar. När den är aktiverad kan den logga in användare och klientappar med liten eller ingen programkod. Du kan implementera din egen autentiserings- och auktoriseringslösning eller tillåta att App Service hanterar den åt dig i stället. Autentiserings- och auktoriseringsmodulen hanterar webbbegäranden innan du lämnar dem till programkoden, och den nekar obehöriga begäranden innan de når koden.

Autentisering och auktorisering av apptjänster stöder flera autentiseringsleverantörer, inklusive Azure Active Directory, Microsoft-konton, Facebook, Google och Twitter. Mer information finns i [Autentisering och auktorisering i Azure App Service](overview-authentication-authorization.md).

## <a name="service-to-service-authentication"></a>Tjänst-till-tjänst-autentisering

När App Service autentiseras mot en backend-tjänst finns två olika mekanismer beroende på dina behov:

- **Tjänstidentitet** - Logga in på fjärrresursen med hjälp av själva appens identitet. Med App-tjänsten kan du enkelt skapa en [hanterad identitet](overview-managed-identity.md), som du kan använda för att autentisera med andra tjänster, till exempel [Azure SQL Database](/azure/sql-database/) eller Azure Key [Vault](/azure/key-vault/). En heltäckande självstudiekurs för den här metoden finns i [Secure Azure SQL Database-anslutning från App Service med hjälp av en hanterad identitet](app-service-web-tutorial-connect-msi.md).
- **På uppdrag av (OBO)** - Gör delegerad åtkomst till fjärrresurser för användarens räkning. Med Azure Active Directory som autentiseringsleverantör kan apptjänstappen utföra delegerad inloggning till en fjärrtjänst, till exempel [Microsoft Graph API](../active-directory/develop/microsoft-graph-intro.md) eller en fjärr-API-app i App Service. En heltäckande självstudiekurs av den här metoden finns i [Autentisera och auktorisera användare från på för att avsluta i Azure App Service](app-service-web-tutorial-auth-aad.md).

## <a name="connectivity-to-remote-resources"></a>Anslutning till fjärrresurser

Det finns tre typer av fjärrresurser som appen kan behöva komma åt: 

- [Azure-resurser](#azure-resources)
- [Resurser i ett virtuellt Azure-nätverk](#resources-inside-an-azure-virtual-network)
- [Lokala resurser](#on-premises-resources)

I vart och ett av dessa fall är App Service ett sätt för dig att upprätta säkra anslutningar, men du bör ändå följa bästa säkerhetspraxis. Använd till exempel alltid krypterade anslutningar även om backend-resursen tillåter okrypterade anslutningar. Se dessutom till att din serverdels Azure-tjänst tillåter minsta uppsättning IP-adresser. Du kan hitta utgående IP-adresser för din app på [inkommande och utgående IP-adresser i Azure App Service](overview-inbound-outbound-ips.md).

### <a name="azure-resources"></a>Azure-resurser

När din app ansluter till [Azure-resurser,](https://azure.microsoft.com/services/sql-database/) till exempel SQL Database och [Azure Storage,](/azure/storage/)stannar anslutningen inom Azure och korsar inte några nätverksgränser. Anslutningen går dock via det delade nätverket i Azure, så se alltid till att anslutningen är krypterad. 

Om din app finns i en [App Service-miljö](environment/intro.md)bör du [ansluta till Azure-tjänster som stöds med hjälp av slutpunkter för tjänsten Virtuellt nätverk](../virtual-network/virtual-network-service-endpoints-overview.md).

### <a name="resources-inside-an-azure-virtual-network"></a>Resurser i ett virtuellt Azure-nätverk

Din app kan komma åt resurser i ett [Virtuellt Azure-nätverk](/azure/virtual-network/) via [integrering av virtuella nätverk](web-sites-integrate-with-vnet.md). Integrationen upprättas med ett virtuellt nätverk med hjälp av en punkt-till-plats-VPN. Appen kan sedan komma åt resurserna i det virtuella nätverket med hjälp av sina privata IP-adresser. Point-to-site-anslutningen passerar dock fortfarande de delade nätverken i Azure. 

Om du vill isolera din resursanslutning helt från de delade nätverken i Azure skapar du din app i en [App Service-miljö](environment/intro.md). Eftersom en App Service-miljö alltid distribueras till ett dedikerat virtuellt nätverk är anslutningen mellan din app och resurser i det virtuella nätverket helt isolerad. Andra aspekter av nätverkssäkerhet i en App Service-miljö finns i [Nätverksisolering](#network-isolation).

### <a name="on-premises-resources"></a>Lokala resurser

Du kan komma åt lokala resurser på ett säkert sätt, till exempel databaser, på tre sätt: 

- [Hybridanslutningar](app-service-hybrid-connections.md) - Upprättar en punkt-till-punkt-anslutning till fjärrresursen via en TCP-tunnel. TCP-tunneln upprättas med TLS 1.2 med SAS-nycklar (Shared Access Signature).
- [Integrering av virtuellt nätverk](web-sites-integrate-with-vnet.md) med plats-till-plats-VPN – Som beskrivs i [Resurser i ett virtuellt Azure-nätverk](#resources-inside-an-azure-virtual-network), men det virtuella nätverket kan anslutas till ditt lokala nätverk via en [plats-till-plats-VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). I den här nätverkstopologin kan appen ansluta till lokala resurser som andra resurser i det virtuella nätverket.
- [App Service-miljö](environment/intro.md) med plats-till-plats-VPN – Som beskrivs i [Resurser i ett Virtuellt Azure-nätverk](#resources-inside-an-azure-virtual-network), men det virtuella nätverket kan anslutas till ditt lokala nätverk via en [plats-till-plats-VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). I den här nätverkstopologin kan appen ansluta till lokala resurser som andra resurser i det virtuella nätverket.

## <a name="application-secrets"></a>Ansökan hemligheter

Lagra inte programhemligheter, till exempel databasautentiseringsuppgifter, API-token och privata nycklar i dina kod- eller konfigurationsfiler. Den allmänt accepterade metoden är att komma åt dem som [miljövariabler](https://wikipedia.org/wiki/Environment_variable) med hjälp av standardmönstret på ditt språk. I App Service är sättet att definiera miljövariabler via [appinställningar](configure-common.md#configure-app-settings) (och, särskilt för .NET-program, [anslutningssträngar](configure-common.md#configure-connection-strings)). Appinställningar och anslutningssträngar lagras krypterade i Azure och de dekrypteras bara innan de injiceras i appens processminne när appen startar. Krypteringsnycklarna roteras regelbundet.

Alternativt kan du integrera din App Service-app med [Azure Key Vault](/azure/key-vault/) för avancerad hemligheter hantering. Genom [att komma åt Key Vault med en hanterad identitet](../key-vault/tutorial-web-application-keyvault.md)kan appen App Service komma åt de hemligheter du behöver på ett säkert sätt.

## <a name="network-isolation"></a>Nätverksisolering

Med undantag för den **isolerade** prisnivån kör alla nivåer dina appar på den delade nätverksinfrastrukturen i App Service. Till exempel delas offentliga IP-adresser och frontend-belastningsutjämnare med andra klienter. Den **isolerade** nivån ger dig fullständig nätverksisolering genom att köra dina appar i en dedikerad [App Service-miljö](environment/intro.md). En App Service-miljö körs i din egen instans av [Azure Virtual Network](/azure/virtual-network/). Det låter dig: 

- Visa dina appar via en dedikerad offentlig slutpunkt med dedikerade främredelar.
- Betjänar internt program med hjälp av en intern belastningsutjämnare (ILB), som endast tillåter åtkomst inifrån ditt virtuella Azure-nätverk. ILB har en IP-adress från ditt privata undernät, vilket ger total isolering av dina appar från Internet.
- [Använd en ILB bakom en brandvägg för webbprogram (WAF)](environment/integrate-with-application-gateway.md). WAF erbjuder skydd på företagsnivå för dina offentliga program, till exempel DDoS-skydd, URI-filtrering och FÖREBYGGANDE AV SQL-injektion.

Mer information finns i [Introduktion till Azure App Service-miljöer](environment/intro.md). 
