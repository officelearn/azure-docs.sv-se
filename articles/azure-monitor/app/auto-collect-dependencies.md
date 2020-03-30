---
title: Azure Application Insights – Autoinsamling för beroende | Microsoft-dokument
description: Application Insights samlar automatiskt in och visualiserar beroenden
ms.topic: reference
author: nikmd23
ms.author: nimolnar
ms.date: 04/29/2019
ms.reviewer: mbullwin
ms.openlocfilehash: eaafe19f5112b433d50a34aa551aa84d196726a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665824"
---
# <a name="dependency-auto-collection"></a>Automatisk insamling för beroenden

Nedan visas den lista över beroendeanrop som för närvarande stöds som automatiskt identifieras som beroenden utan att kräva några ytterligare ändringar av programmets kod. Dessa beroenden visualiseras i [programinsiktsmappningen](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) och [transaktionsdiagnostikvyerna.](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) Om ditt beroende inte finns i listan nedan kan du fortfarande spåra det manuellt med ett [spårberoendeanrop](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

## <a name="net"></a>.NET

| Appramverk| Versioner |
| ------------------------|----------|
| ASP.NET webbformulär | 4,5+ |
| ASP.NET MVC | 4+ |
| ASP.NET WebAPI | 4,5+ |
| ASP.NET Core | 1.1+ |
| <b>Kommunikationsbibliotek</b> |
| [HttpClient (på ett sätt)](https://www.microsoft.com/net/) | 4,5+, .NET Core 1,1+ |
| [SqlClient (sqlclient)](https://www.nuget.org/packages/System.Data.SqlClient) | .NET Core 1.0+, NuGet 4.3.0 |
| [EventHubs-klient SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 |
| [ServiceBus-klient SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | 3.0.0 |
| <b>Lagringsklienter</b>|  |
| ADO.NET | 4,5+ |

## <a name="java"></a>Java
| Appservrar | Versioner |
|-------------|----------|
| [Tomcat](https://tomcat.apache.org/) | 7, 8 | 
| [JBoss-EAP](https://developers.redhat.com/products/eap/download/) | 6, 7 |
| [Brygga](https://www.eclipse.org/jetty/) | 9 |
| <b>Appramverk</b> |  |
| [Spring](https://spring.io/) | 3.0 |
| [Spring Boot](https://spring.io/projects/spring-boot) | 1.5.9+<sup>*</sup> |
| Java Servlet | 3.1+ |
| <b>Kommunikationsbibliotek</b> |  |
| [Apache http-klient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) | 4.3+<sup>†</sup> |
| <b>Lagringsklienter</b> | |
| [SQL Server]( https://mvnrepository.com/artifact/com.microsoft.sqlserver/mssql-jdbc) | 1+<sup>†</sup> |
| [PostgreSQL (betasupport)](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/CHANGELOG.md#version-240-beta) | |
| [Oracle]( https://www.oracle.com/technetwork/database/application-development/jdbc/downloads/index.html) | 1+<sup>†</sup> |
| [Mysql]( https://mvnrepository.com/artifact/mysql/mysql-connector-java) | 1+<sup>†</sup> |
| <b>Logga bibliotek</b> | |
| [Logga tillbaka](https://logback.qos.ch/) | 1+ |
| [Log4j (på andra sätt)](https://logging.apache.org/log4j/) | 1.2+ |
| <b>Måttbibliotek</b> |  |
| Jmx | 1.0+ |

> [!NOTE]
> * Utom reaktiv programmering stöd.
> <br>†Kräver installation av [JVM Agent](https://docs.microsoft.com/azure/application-insights/app-insights-java-agent#install-the-application-insights-agent-for-java).

## <a name="nodejs"></a>Node.js

| Kommunikationsbibliotek | Versioner |
| ------------------------|----------|
| [HTTP](https://nodejs.org/api/http.html), [HTTPS](https://nodejs.org/api/https.html) | 0.10+ |
| <b>Lagringsklienter</b> | |
| [Redis](https://www.npmjs.com/package/redis) | 2.x |
| [MongoDb](https://www.npmjs.com/package/mongodb); [MongoDb kärna](https://www.npmjs.com/package/mongodb-core) | 2.x - 3.x |
| [MySQL](https://www.npmjs.com/package/mysql) | 2.0.0 - 2.16.x |
| [PostgreSql](https://www.npmjs.com/package/pg); | 6.x - 7.x |
| [pg-pool](https://www.npmjs.com/package/pg-pool) | 1.x - 2.x |
| <b>Logga bibliotek</b> | |
| [Konsolen](https://nodejs.org/api/console.html) | 0.10+ |
| [Bunyan](https://www.npmjs.com/package/bunyan) | 1.x |
| [Winston](https://www.npmjs.com/package/winston) | 2.x - 3.x |

## <a name="javascript"></a>JavaScript

| Kommunikationsbibliotek | Versioner |
| ------------------------|----------|
| [XMLhttpRequest](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) | Alla |

## <a name="next-steps"></a>Nästa steg

- Ställ in anpassad beroendespårning för [.NET](../../azure-monitor/app/asp-net-dependencies.md).
- Konfigurera anpassad beroendespårning för [Java](../../azure-monitor/app/java-agent.md).
- Ställ in anpassad beroendespårning för [OpenCensus Python](../../azure-monitor/app/opencensus-python-dependency.md).
- [Skriv anpassad beroendetelemetri](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Se [datamodell](../../azure-monitor/app/data-model.md) för programinsiktstyper och datamodell.
- Kolla in [plattformar](../../azure-monitor/app/platforms.md) som stöds av Application Insights.
