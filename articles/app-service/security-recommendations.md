---
title: Säkerhetsrekommendationer
description: Implementera säkerhetsrekommendationerna för att uppfylla dina säkerhetsskyldigheter enligt vår modell för delat ansvar. Förbättra säkerheten för din app.
author: barclayn
manager: barbkess
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: barclayn
ms.custom: security-recommendations
ms.openlocfilehash: e3f901fd78436b42729607d38f0ddb533f31cd30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74684117"
---
# <a name="security-recommendations-for-app-service"></a>Säkerhetsrekommendationer för App Service

Den här artikeln innehåller säkerhetsrekommendationer för Azure App Service. Genom att implementera dessa rekommendationer kan du uppfylla dina säkerhetsskyldigheter enligt beskrivningen i vår modell för delat ansvar och förbättra den övergripande säkerheten för dina webbapplösningar. Mer information om vad Microsoft gör för att uppfylla ansvaret för tjänsteleverantören finns i [Azure Infrastructure security](../security/fundamentals/infrastructure.md).

## <a name="general"></a>Allmänt

| Rekommendation | Kommentarer |
|-|-|----|
| Håll dig uppdaterad | Använd de senaste versionerna av plattformar, programmeringsspråk, protokoll och ramverk som stöds. |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Rekommendation | Kommentarer |
|-|----|
| Inaktivera anonym åtkomst | Om du inte behöver stödja anonyma förfrågningar inaktiverar du anonym åtkomst. Mer information om autentiseringsalternativ för Azure App Service finns [i Autentisering och auktorisering i Azure App Service](overview-authentication-authorization.md).|
| Kräv autentisering | Använd autentiseringsmodulen för App Service i stället för att skriva kod för att hantera autentisering och auktorisering när det är möjligt. Se [Autentisering och auktorisering i Azure App Service](overview-authentication-authorization.md). |
| Skydda backend-resurser med autentiserat åtkomst | Du kan antingen använda användarens identitet eller använda en programidentitet för att autentisera till en backend-resurs. När du väljer att använda en programidentitet använder du en [hanterad identitet](overview-managed-identity.md).
| Kräv autentisering av klientcertifikat | Klientcertifikatautentisering förbättrar säkerheten genom att endast tillåta anslutningar från klienter som kan autentisera med certifikat som du anger. |

## <a name="data-protection"></a>Dataskydd

| Rekommendation | Kommentarer |
|-|-|
| Omdirigera HTTP till HTTPs | Som standard kan klienter ansluta till webbappar med både HTTP eller HTTPS. Vi rekommenderar att http omdirigeras till HTTPs eftersom HTTPS använder SSL/TLS-protokollet för att tillhandahålla en säker anslutning, som är både krypterad och autentiserad. |
| Kryptera kommunikation till Azure-resurser | När din app ansluter till Azure-resurser, till exempel [SQL Database](https://azure.microsoft.com/services/sql-database/) eller [Azure Storage,](/azure/storage/)finns anslutningen kvar i Azure. Eftersom anslutningen går via det delade nätverket i Azure bör du alltid kryptera all kommunikation. |
| Kräv den senaste TLS-versionen möjlig | Sedan 2018 använder nya Azure App Service-appar TLS 1.2. Nyare versioner av TLS innehåller säkerhetsförbättringar jämfört med äldre protokollversioner. |
| Använd FTPS | App Service stöder både FTP och FTPS för distribution av dina filer. Använd FTPS i stället för FTP när det är möjligt. När ett eller båda av dessa protokoll inte används bör du [inaktivera dem](deploy-ftp.md#enforce-ftps). |
| Skydda programdata | Lagra inte programhemligheter, till exempel databasautentiseringsuppgifter, API-token eller privata nycklar i koden eller konfigurationsfilerna. Den allmänt accepterade metoden är att komma åt dem som [miljövariabler](https://wikipedia.org/wiki/Environment_variable) med hjälp av standardmönstret på ditt språk. I Azure App Service kan du definiera miljövariabler via [appinställningar](web-sites-configure.md) och [anslutningssträngar](web-sites-configure.md). Appinställningar och anslutningssträngar lagras krypterade i Azure. Appinställningarna dekrypteras bara innan de injiceras i appens processminne när appen startar. Krypteringsnycklarna roteras regelbundet. Alternativt kan du integrera din Azure App Service-app med [Azure Key Vault](/azure/key-vault/) för avancerad hemligheterhantering. Genom [att komma åt Key Vault med en hanterad identitet](../key-vault/tutorial-web-application-keyvault.md)kan appen App Service komma åt de hemligheter du behöver på ett säkert sätt. |

## <a name="networking"></a>Nätverk

| Rekommendation | Kommentarer |
|-|-|
| Använd statiska IP-begränsningar | Med Azure App Service i Windows kan du definiera en lista över IP-adresser som har åtkomst till din app. Den tillåtna listan kan innehålla enskilda IP-adresser eller ett intervall med IP-adresser som definieras av en nätmask. Mer information finns i [Statiska IP-begränsningar för Azure App Service](app-service-ip-restrictions.md).  |
| Använd den isolerade prisnivån | Med undantag för den isolerade prisnivån kör alla nivåer dina appar på den delade nätverksinfrastrukturen i Azure App Service. Den isolerade nivån ger dig fullständig nätverksisolering genom att köra dina appar i en dedikerad [App Service-miljö](environment/intro.md). En App Service-miljö körs i din egen instans av [Azure Virtual Network](/azure/virtual-network/).|
| Använda säkra anslutningar vid åtkomst till lokala resurser | Du kan använda [Hybrid-anslutningar,](app-service-hybrid-connections.md) [integrering av virtuella nätverk](web-sites-integrate-with-vnet.md)eller App [Service-miljöer](environment/intro.md) för att ansluta till lokala resurser. |
| Begränsa exponeringen för inkommande nätverkstrafik | Med nätverkssäkerhetsgrupper kan du begränsa nätverksåtkomsten och kontrollera antalet exponerade slutpunkter. Mer information finns [i Så här kontrollerar du inkommande trafik till en apptjänstmiljö](environment/app-service-app-service-environment-control-inbound-traffic.md). |

## <a name="monitoring"></a>Övervakning

| Rekommendation | Kommentarer |
|-|-|
|Använda standardnivå för Azure Security Center | [Azure Security Center](../security-center/security-center-app-services.md) är inbyggt integrerat med Azure App Service. Den kan köra utvärderingar och ge säkerhetsrekommendationer. |

## <a name="next-steps"></a>Nästa steg

Kontrollera med programleverantören om det finns ytterligare säkerhetskrav. Mer information om hur du utvecklar säkra program finns i [Säker utvecklingsdokumentation](../security/fundamentals/abstract-develop-secure-apps.md).
