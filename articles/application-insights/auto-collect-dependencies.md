---
title: Azure Application Insights - beroende Automatisk insamling | Microsoft Docs
description: Application Insights automatiskt samla in och visualisera beroenden
services: application-insights
documentationcenter: .net
author: nikmd23
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: reference
ms.date: 08/13/2018
ms.reviewer: mbullwin
ms.author: nimolnar
ms.openlocfilehash: f20963f030c9040b696f7d6a33b25bcee2dc517f
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2018
ms.locfileid: "40130295"
---
# <a name="dependency-auto-collection"></a>Beroende Automatisk insamling

Nedan visas i listan över beroendeanrop som identifieras automatiskt som beroenden utan några ytterligare ändringar till din programkod för närvarande stöds. Det här består av utgående samtal till databaskommunikationsbibliotek, storage-klienter, loggning och mått bibliotek, samt inkommande anrop till ramverk för programmet och servrar. Dessa beroenden visualiseras i Application Insights [programkartan](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) och [transaktionsdiagnostik](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) vyer. Om din beroende finns inte i listan nedan, du kan fortfarande spåra den manuellt med en [spåra beroendeanropet](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

## <a name="net"></a>.NET

| Appramverk| Versioner |
| ------------------------|----------|
| ASP.NET-webbformulär | 4.5 + |
| ASP.NET MVC | 4 + |
| ASP.NET-WebAPI | 4.5 + |
| ASP.NET Core | 1.1 + |
| <b> Databaskommunikationsbibliotek</b> |
| [HttpClient](https://www.microsoft.com/net/) | 4.5 +, .NET core 1.1 + |
| [SqlClient](https://www.nuget.org/packages/System.Data.SqlClient) | .NET core 1.0 + NuGet 4.3.0 |
| [EventHubs-Client SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 |
| [ServiceBus-klientens SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | 3.0.0 |
| <b>Storage-klienter</b>|  |
| ADO.NET | 4.5 + |
| <b>Bibliotek för loggning</b> |  |
| ILogger | 1.1 + |
| System.Diagnostics.Trace | 4.5 + |
| [nLog](https://www.nuget.org/packages/NLog/) | 4.4.12+ |
| [log4net](https://www.nuget.org/packages/log4net/) | 2.0.8+ på NetStandard 1.3, 2.0.6+ på .NET 4.5 + |

## <a name="java"></a>Java
| App-servrar | Versioner |
|-------------|----------|
| [tomcat](https://tomcat.apache.org/) | 7, 8 | 
| [JBoss EAP](https://developers.redhat.com/products/eap/download/) | 6, 7 |
| [Jetty](http://www.eclipse.org/jetty/) | 9 |
| <b>Appramverk </b> |  |
| [Spring](https://spring.io/) | 3.0 |
| [Spring Boot](https://spring.io/projects/spring-boot) | 1.5.9+<sup>*</sup> |
| Java Servlet | 3.1 + |
| <b>Databaskommunikationsbibliotek</b> |  |
| [Apache Http-klient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) | 4.3 +<sup>†</sup> |
| <b>Storage-klienter</b> | |
| [SQL Server]( https://mvnrepository.com/artifact/com.microsoft.sqlserver/mssql-jdbc) | 1 +<sup>†</sup> |
| [Oracle]( http://www.oracle.com/technetwork/database/application-development/jdbc/downloads/index.html) | 1 +<sup>†</sup> |
| [MySql]( https://mvnrepository.com/artifact/mysql/mysql-connector-java) | 1 +<sup>†</sup> |
| <b>Bibliotek för loggning</b> | |
| [Logback](https://logback.qos.ch/) | 1 + |
| [Log4j](https://logging.apache.org/log4j/) | 1.2 + |
| <b>Mått-bibliotek</b> |  |
| JMX | 1.0 + |

> [!NOTE]
> * Förutom reaktiv programmering support.
> <br>†Requires installation av [JVM agenten](https://docs.microsoft.com/azure/application-insights/app-insights-java-agent#install-the-application-insights-agent-for-java).

## <a name="nodejs"></a>Node.js

| Databaskommunikationsbibliotek | Versioner |
| ------------------------|----------|
| [HTTP](https://nodejs.org/api/http.html), [HTTPS](https://nodejs.org/api/https.html) | 0.10 + |
| <b>Storage-klienter</b> | |
| [Redis](https://www.npmjs.com/package/redis) | 2.x |
| [MongoDb](https://www.npmjs.com/package/mongodb); [MongoDb Core](https://www.npmjs.com/package/mongodb-core) | 2.0.0 - 2.3.0 |
| [MySQL](https://www.npmjs.com/package/mysql) | 2.0.0 - 2.14.x |
| [PostgreSql](https://www.npmjs.com/package/pg); | 6.x |
| [PG-pool](https://www.npmjs.com/package/pg-pool) | 1.x |
| <b>Bibliotek för loggning</b> | |
| [Konsolen](https://nodejs.org/api/console.html) | 0.10 + |
| [Bunyan](https://www.npmjs.com/package/bunyan) | 1.x |
| [Winston](https://www.npmjs.com/package/winston) | 2.x |

## <a name="javascript"></a>JavaScript

| Databaskommunikationsbibliotek | Versioner |
| ------------------------|----------|
| [XMLHttpRequest](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) | Alla |

## <a name="next-steps"></a>Nästa steg

- Konfigurera anpassade beroendespårning för [.NET](app-insights-asp-net-dependencies.md).
- Konfigurera anpassade beroendespårning för [Java](app-insights-java-agent.md).
- [Skriva anpassade beroendetelemetri](app-insights-api-custom-events-metrics.md#trackdependency)
- Se [datamodellen](application-insights-data-model.md) för Application Insights och modellen.
- Kolla in [plattformar](app-insights-platforms.md) stöds av Application Insights.
