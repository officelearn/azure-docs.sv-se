---
title: Säkerhets översikt – Azure App Service | Microsoft Docs
description: Lär dig mer om hur App Service skyddar din app och hur du kan låsa din app ytterligare.
keywords: Azure App Service, webbapp, mobilapp, API-app, funktions program, säkerhet, säker, säker, efterlevnad, kompatibel, certifikat, certifikat, https, FTPS, TLS, Trust, kryptering, kryptera, krypterad, IP-begränsning, autentisering, auktorisering, authn, autentisering, MSI, hanterad tjänst identitet, hanterad identitet, hemligheter, hemlighet, korrigering, korrigering, korrigeringar, version, isolering, nätverks isolering, DDoS, MITM
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/24/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: b6f122abff1ac75bb1cb836f3389c96dfcdf60e0
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074107"
---
# <a name="security-in-azure-app-service"></a>Säkerhet i Azure App Service

Den här artikeln visar hur [Azure App Service](overview.md) skyddar din webbapp, Server del för mobilapp, API-app och Function- [appen](/azure/azure-functions/). Det visar också hur du kan skydda din app ytterligare med de inbyggda App Service funktionerna.

Plattforms komponenterna i App Service, inklusive virtuella Azure-datorer, lagring, nätverks anslutningar, webb ramverk, hanterings-och integrations funktioner, är aktivt säkra och skärps. App Service genomgår en kontinuerlig kontroll av efterlevnaden för att se till att:

- Dina app-resurser [skyddas](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) från de andra kundernas Azure-resurser.
- [VM-instanser och runtime-programvaran uppdateras regelbundet](overview-patch-os-runtime.md) för att lösa nyupptäckta sårbarheter. 
- Kommunikation av hemligheter (t. ex. anslutnings strängar) mellan din app och andra Azure-resurser (till exempel [SQL Database](https://azure.microsoft.com/services/sql-database/)) ligger kvar i Azure och korsar inte några nätverks gränser. Hemligheter krypteras alltid när de lagras.
- All kommunikation via App Service anslutnings funktioner, till exempel [hybrid anslutning](app-service-hybrid-connections.md), krypteras. 
- Anslutningar med verktyg för fjärrhantering som Azure PowerShell, Azure CLI, Azure SDK: er, REST API: er är krypterade.
- 24-timmars hot hantering skyddar infrastrukturen och plattformen mot skadlig kod, distribuerad denial-of-service (DDoS), man-in-the-Middle (MITM) och andra hot.

Mer information om infrastruktur-och plattforms säkerhet i Azure finns [Azure Säkerhetscenter](https://azure.microsoft.com/overview/trusted-cloud/).

I följande avsnitt visas hur du skyddar din App Service-app ytterligare från hot.

## <a name="https-and-certificates"></a>HTTPS och certifikat

Med App Service kan du skydda dina appar med [https](https://wikipedia.org/wiki/HTTPS). När din app skapas, är dess standard domän namn (\<APP_NAME >. azurewebsites. net) redan tillgängligt via https. Om du [konfigurerar en anpassad domän för din app](app-service-web-tutorial-custom-domain.md)bör du även [skydda den med ett anpassat certifikat](app-service-web-tutorial-custom-ssl.md) så att klient webbläsare kan göra säkra HTTPS-anslutningar till din anpassade domän. Det finns två sätt att göra det:

- **App service certifikat** – skapa ett certifikat direkt i Azure. Certifikatet skyddas i [Azure Key Vault](/azure/key-vault/)och kan importeras till din app service-app. Mer information finns i [köpa och konfigurera ett SSL-certifikat för din Azure App Service](web-sites-purchase-ssl-web-site.md).
- **Certifikat från tredje part** – Ladda upp ett anpassat SSL-certifikat som du har köpt från en betrodd certifikat utfärdare och bind det till din app service-app. App Service stöder både certifikat med enkel domän och jokertecken. Den har också stöd för självsignerade certifikat i test syfte. Mer information finns i [BIND ett befintligt anpassat SSL-certifikat till Azure App Service](app-service-web-tutorial-custom-ssl.md).

## <a name="insecure-protocols-http-tls-10-ftp"></a>Osäkra protokoll (HTTP, TLS 1,0, FTP)

För att skydda din app mot alla okrypterade anslutningar (HTTP), App Service tillhandahålla konfiguration med ett klick för att använda HTTPS. Oskyddade förfrågningar är inaktiverade innan de når program koden. Mer information finns i [FRAMTVINGA https](app-service-web-tutorial-custom-ssl.md#enforce-https).

[TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1,0 anses inte längre vara säkert av bransch standarder, t. ex. [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Med App Service kan du inaktivera inaktuella protokoll genom att [FRAMTVINGA TLS 1.1/1.2](app-service-web-tutorial-custom-ssl.md#enforce-tls-versions).

App Service stöder både FTP-och FTPS för att distribuera dina filer. FTPS bör dock användas i stället för FTP, om det är möjligt. När ett eller båda av dessa protokoll inte används bör du [inaktivera dem](deploy-ftp.md#enforce-ftps).

## <a name="static-ip-restrictions"></a>Statiska IP-begränsningar

Som standard godkänner din App Service-app begär Anden från alla IP-adresser från Internet, men du kan begränsa åtkomsten till en liten delmängd av IP-adresser. App Service i Windows kan du definiera en lista med IP-adresser som får åtkomst till din app. Listan över tillåtna kan innehålla enskilda IP-adresser eller ett intervall med IP-adresser som definieras av en under nät mask. Mer information finns i [Azure App Service statiska IP-begränsningar](app-service-ip-restrictions.md).

För App Service i Windows kan du också begränsa IP-adresser dynamiskt genom att konfigurera _Web. config_. Mer information finns i [dynamisk IP-säkerhet \<dynamicIpSecurity >](https://docs.microsoft.com/iis/configuration/system.webServer/security/dynamicIpSecurity/).

## <a name="client-authentication-and-authorization"></a>Klientautentisering och-auktorisering

Azure App Service tillhandahåller autentisering med turn-och auktorisering av användare eller klient program. När den är aktive rad kan den logga in användare och klient program med lite eller ingen program kod. Du kan implementera en egen lösning för autentisering och auktorisering, eller låta App Service hantera den åt dig i stället. Modulen för autentisering och auktorisering hanterar webb förfrågningar innan de skickas till program koden och nekas obehöriga begär Anden innan de når din kod.

App Service autentisering och auktorisering stöder flera autentiseringsproviders, inklusive Azure Active Directory, Microsoft-konton, Facebook, Google och Twitter. Mer information finns i [Autentisering och auktorisering i Azure App Service](overview-authentication-authorization.md).

## <a name="service-to-service-authentication"></a>Tjänst-till-tjänst-autentisering

Vid autentisering mot en backend-tjänst tillhandahåller App Service två olika metoder beroende på dina behov:

- **Tjänst identitet** – logga in på fjär resursen med appens identitet. Med App Service kan du enkelt skapa en [hanterad identitet](overview-managed-identity.md), som du kan använda för att autentisera med andra tjänster, till exempel [Azure SQL Database](/azure/sql-database/) eller [Azure Key Vault](/azure/key-vault/). En fullständig själv studie kurs om den här metoden finns i [säker Azure SQL Database anslutning från App Service med hjälp av en hanterad identitet](app-service-web-tutorial-connect-msi.md).
- **På uppdrag av (OBO)** – gör delegerad åtkomst till fjär resurser åt användaren. Med Azure Active Directory som autentiseringsprovider kan App Service-appen utföra delegerad inloggning till en fjärrtjänst, till exempel [Azure Active Directory Graph API](../active-directory/develop/active-directory-graph-api.md) eller en fjärr-API-App i App Service. En fullständig själv studie kurs om den här metoden finns i [autentisera och auktorisera användare från slut punkt till slut punkt i Azure App Service](app-service-web-tutorial-auth-aad.md).

## <a name="connectivity-to-remote-resources"></a>Anslutning till fjär resurser

Det finns tre typer av fjär resurser som din app kan behöva åtkomst till: 

- [Azure-resurser](#azure-resources)
- [Resurser i en Azure-Virtual Network](#resources-inside-an-azure-virtual-network)
- [Lokala resurser](#on-premises-resources)

I vart och ett av dessa fall är App Service ett sätt för dig att skapa säkra anslutningar, men du bör fortfarande se rekommenderade säkerhets metoder. Använd till exempel alltid krypterade anslutningar även om Server dels resursen tillåter okrypterade anslutningar. Kontrol lera också att Azure-tjänsten för Server delen tillåter den lägsta uppsättningen IP-adresser. Du kan hitta de utgående IP-adresserna för din app på [inkommande och utgående IP-adresser i Azure App Service](overview-inbound-outbound-ips.md).

### <a name="azure-resources"></a>Azure-resurser

När appen ansluter till Azure-resurser, till exempel [SQL Database](https://azure.microsoft.com/services/sql-database/) och [Azure Storage](/azure/storage/), stannar anslutningen i Azure och korsar inte några nätverks gränser. Anslutningen går dock igenom det delade nätverket i Azure, så kontrol lera alltid att anslutningen är krypterad. 

Om din app finns i en [app Services miljö](environment/intro.md)bör du [ansluta till Azure-tjänster som stöds med hjälp av Virtual Network tjänst slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md).

### <a name="resources-inside-an-azure-virtual-network"></a>Resurser i en Azure-Virtual Network

Din app kan komma åt resurser i en [Azure-Virtual Network](/azure/virtual-network/) via Virtual Network- [integrering](web-sites-integrate-with-vnet.md). Integrationen upprättas med en Virtual Network med hjälp av en punkt-till-plats-VPN. Appen kan sedan komma åt resurserna i Virtual Network med hjälp av sina privata IP-adresser. Punkt-till-plats-anslutningen går dock fortfarande igenom de delade nätverken i Azure. 

Skapa din app i en [App Service-miljö](environment/intro.md)för att isolera resurs anslutningen helt från de delade nätverken i Azure. Eftersom en App Services miljö alltid distribueras till en dedikerad Virtual Network, är anslutningen mellan appen och resurserna i Virtual Network helt isolerad. Andra aspekter av nätverks säkerheten i en App Service-miljö finns i [nätverks isolering](#network-isolation).

### <a name="on-premises-resources"></a>Lokala resurser

Du kan få säker åtkomst till lokala resurser, till exempel databaser, på tre sätt: 

- [Hybrid anslutningar](app-service-hybrid-connections.md) – upprättar en punkt-till-punkt-anslutning till fjär resursen via en TCP-tunnel. TCP-tunneln upprättas med hjälp av TLS 1,2 med nycklar för signatur för delad åtkomst (SAS).
- [Virtual Network integrering](web-sites-integrate-with-vnet.md) med plats-till-plats-VPN – enligt beskrivningen i [resurser i en Azure-Virtual Network](#resources-inside-an-azure-virtual-network), men Virtual Network kan anslutas till ditt lokala nätverk via en [plats-till-plats-VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). I den här nätverk sto pol Ogin kan din app ansluta till lokala resurser som andra resurser i Virtual Network.
- [App Service miljö](environment/intro.md) med plats-till-plats-VPN – enligt beskrivningen i [resurser i en Azure-Virtual Network](#resources-inside-an-azure-virtual-network), men Virtual Network kan anslutas till ditt lokala nätverk via en [plats-till-plats-VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). I den här nätverk sto pol Ogin kan din app ansluta till lokala resurser som andra resurser i Virtual Network.

## <a name="application-secrets"></a>Program hemligheter

Lagra inte program hemligheter, t. ex. autentiseringsuppgifter för databaser, API-token och privata nycklar i koden eller konfigurationsfilerna. Den vanligaste metoden är att få åtkomst till dem [](https://wikipedia.org/wiki/Environment_variable) som miljövariabler som använder standard mönstret på det språk som du väljer. I App Service kan du definiera miljövariabler genom att ange [appinställningar](configure-common.md#configure-app-settings) (och särskilt för .NET-program, [anslutnings strängar](configure-common.md#configure-connection-strings)). App-inställningar och anslutnings strängar lagras krypterade i Azure och de dekrypteras bara innan de matas in i appens process minne när appen startas. Krypterings nycklarna roteras regelbundet.

Du kan också integrera din App Service-app med [Azure Key Vault](/azure/key-vault/) för hantering av avancerade hemligheter. Genom att [komma åt Key Vault med en hanterad identitet](../key-vault/tutorial-web-application-keyvault.md)kan din app service-app på ett säkert sätt komma åt de hemligheter du behöver.

## <a name="network-isolation"></a>Nätverks isolering

Förutom den **isolerade** pris nivån kör alla nivåer dina appar i den delade nätverks infrastrukturen i App Service. Till exempel delas offentliga IP-adresser och klient dels belastnings utjämning med andra klienter. Den **isolerade** nivån ger dig fullständig nätverks isolering genom att köra apparna i en dedikerad [app Services miljö](environment/intro.md). En App Service-miljö körs i din egen instans av [Azure Virtual Network](/azure/virtual-network/). Det gör att du kan: 

- Hantera dina appar via en dedikerad offentlig slut punkt, med dedikerade klient delar.
- Hantera internt program med hjälp av en intern belastningsutjämnare (ILB), som endast tillåter åtkomst från i Azure-Virtual Network. ILB har en IP-adress från ditt privata undernät, vilket ger en fullständig isolering av dina appar från Internet.
- [Använd en ILB bakom en brand vägg för webbaserade program (WAF)](environment/integrate-with-application-gateway.md). WAF erbjuder skydd på företags nivå för dina offentliga program, till exempel DDoS-skydd, URI-filtrering och SQL-injektering.

Mer information finns i [Introduktion till Azure App Service miljöer](environment/intro.md). 
