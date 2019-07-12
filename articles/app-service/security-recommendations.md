---
title: Säkerhetsrekommendationer för Azure App Service
description: Säkerhetsrekommendationer för Azure App Service. Implementera rekommendationerna förbättrar hjälp du uppfylla dina security skyldigheter enligt beskrivningen i vår delat ansvar modellen och kommer den övergripande säkerheten för dina lösningar för web app.
services: app-service
author: barclayn
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: barclayn
ms.custom: security-recommendations
ms.openlocfilehash: 53cd2b1dde1158a1c46f798e57911dad110dc87e
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718263"
---
# <a name="security-recommendations-for-app-service"></a>Säkerhetsrekommendationer för App Service

Den här artikeln innehåller säkerhetsrekommendationer för Azure App Service. Implementera rekommendationerna förbättrar hjälp du uppfylla dina security skyldigheter enligt beskrivningen i vår delat ansvar modellen och kommer den övergripande säkerheten för dina lösningar för Web App. För mer information om vilka Microsoft för att uppfylla service provider ansvarsområden, läsa [Azure infrastruktursäkerhet](../security/azure-security-infrastructure.md).

## <a name="general"></a>Allmänt

| Rekommendation | Kommentar |
|-|-|----|
| Håll dig uppdaterad | Använd de senaste versionerna av plattformar som stöds, programmeringsspråk, protokoll och ramverk. |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Rekommendation | Kommentar |
|-|----|
| Inaktivera anonym åtkomst | Om du inte behöver stöd för anonyma begäranden, inaktivera anonym åtkomst. Mer information om alternativ för Azure App Service-autentisering finns i [autentisering och auktorisering i Azure App Service](overview-authentication-authorization.md).|
| Kräv autentisering | När det är möjligt använda modulen för App Service-autentisering i stället för att skriva kod för att hantera autentisering och auktorisering. Se [autentisering och auktorisering i Azure App Service](overview-authentication-authorization.md). |
| Skydda backend-resurser med autentiserad åtkomst | Du kan använda användarens identitet, eller så kan du använda en programidentitet för att autentisera till en backend-resurs. När du väljer att använda ett program kan du använda identitet en [hanterad identitet](overview-managed-identity.md).
| Kräv autentisering av klientcertifikat | Autentisering av klientcertifikat förbättrar säkerheten genom att bara tillåta anslutningar från klienter som kan autentisera med hjälp av certifikat som du anger. |

## <a name="data-protection"></a>Dataskydd

| Rekommendation | Kommentar |
|-|-|
| Omdirigera HTTP till HTTPs | Som standard kan klienter ansluta till web apps med hjälp av både HTTP eller HTTPS. Vi rekommenderar att omdirigera HTTP till HTTPs eftersom HTTPS använder SSL/TLS-protokollet för att tillhandahålla en säker anslutning som är både krypterade och autentiseras. |
| Kryptera kommunikation till Azure-resurser | När din app ansluter till Azure-resurser, till exempel [SQL Database](https://azure.microsoft.com/services/sql-database/) eller [Azure Storage](/azure/storage/), anslutningen som ligger i Azure. Eftersom anslutningen går igenom det delade nätverket i Azure, bör du alltid kryptera all kommunikation. |
| Kräv den senaste versionen för TLS möjligt | Eftersom 2018 nya Azure App Service-appar använder TLS 1.2. Nyare versioner av TLS omfattar säkerhetsförbättringar äldre protokollversioner. |
| Använda FTPS | App Service stöder både FTP och FTPS för att distribuera dina filer. Använd FTPS istället för FTP när det är möjligt. När en eller båda av dessa protokoll inte används, bör du [inaktivera dem](deploy-ftp.md#enforce-ftps). |
| Skydda programdata | Lagra programhemligheter som Databasautentiseringsuppgifter, API-token eller privata nycklar i filerna kodning eller inte. Allmänt accepterade metod är att komma åt dem som [miljövariabler](https://wikipedia.org/wiki/Environment_variable) med hjälp av Standardmönstret i valfritt språk. I Azure App Service kan du definiera miljövariabler [appinställningar](web-sites-configure.md) och [anslutningssträngar](web-sites-configure.md). Appinställningar och anslutningssträngar lagras krypterade i Azure. Appinställningarna dekrypteras endast innan som införs i din app processminne när appen startar. Krypteringsnycklarna roteras regelbundet. Du kan också integrera Azure App Service-appen med [Azure Key Vault](/azure/key-vault/) för hantering av avancerade hemligheter. Genom att [åtkomst till Nyckelvalv med en hanterad identitet](../key-vault/tutorial-web-application-keyvault.md), App Service-appen på ett säkert sätt kan komma åt hemligheter som du behöver. |

## <a name="networking"></a>Nätverk

| Rekommendation | Kommentar |
|-|-|
| Använda statiska IP-adressbegränsningar | Azure App Service i Windows kan du definiera en lista över IP-adresser som ska kunna komma åt din app. Listan över tillåtna kan inkludera enskilda IP-adresser eller ett intervall med IP-adresser som definieras av en nätmask. Mer information finns i [Azure App Service statiska IP-begränsningar](app-service-ip-restrictions.md).  |
| Använd den isolerade prisnivån | Förutom den isolerade prisnivån kör alla nivåer apparna på den delade nätverksinfrastrukturen i Azure App Service. Den isolerade nivån ger dig fullständig nätverksisolering genom att köra appar i en dedikerad [apptjänstmiljö](environment/intro.md). App Service environment som körs i en egen instans av [Azure Virtual Network](/azure/virtual-network/).|
| Använd säkra anslutningar när åtkomst till lokala resurser | Du kan använda [hybridanslutningar](app-service-hybrid-connections.md), [Virtual Network-integration](web-sites-integrate-with-vnet.md), eller [App Service-miljö](environment/intro.md) att ansluta till lokala resurser. |
| Begränsa exponeringen för inkommande nätverkstrafik | Nätverkssäkerhetsgrupper kan du begränsa nätverksåtkomst och kontrollera antalet exponerade slutpunkter. Mer information finns i [hur du kontrollerar inkommande trafik till en App Service Environment](environment/app-service-app-service-environment-control-inbound-traffic.md). |

## <a name="monitoring"></a>Övervakning

| Rekommendation | Kommentar |
|-|-|
|Använd Azure Security Center standard-nivån | [Azure Security Center](../security-center/security-center-app-services.md) internt är integrerat med Azure App Service. Det kan köra utvärderingar och ge säkerhetsrekommendationer. |

## <a name="next-steps"></a>Nästa steg

Kontrollera med leverantören program för att se om det finns ytterligare säkerhetskrav. Mer information om hur du utvecklar säkra program finns i [Secure Development-dokumentationen](../security/abstract-develop-secure-apps.md).
