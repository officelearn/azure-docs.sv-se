---
title: IP-adresser som används av Application Insights och Log Analytics | Microsoft-dokument
description: Undantag för serverbrandväggen som krävs av Application Insights
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 12/19/2019
ms.openlocfilehash: 454138f8e0d92935126f446455810a444b0a053a
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984154"
---
# <a name="ip-addresses-used-by-application-insights-and-log-analytics"></a>IP-adresser som används av Application Insights och Log Analytics
Azure [Application Insights-tjänsten](../../azure-monitor/app/app-insights-overview.md) använder ett antal IP-adresser. Du kan behöva känna till dessa adresser om appen som du övervakar finns bakom en brandvägg.

> [!NOTE]
> Även om dessa adresser är statiska, är det möjligt att vi kommer att behöva ändra dem från tid till annan. All Application Insights-trafik representerar utgående trafik med undantag för tillgänglighetsövervakning och webhooks som kräver inkommande brandväggsregler.
> 
> 

> [!TIP]
> Prenumerera på denna sida som https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/azure-monitor/app/ip-addresses.md.atom ett RSS-flöde genom att lägga till din favorit RSS / ATOM-läsare för att få meddelande om de senaste ändringarna.
> 
> 

## <a name="outgoing-ports"></a>Utgående portar
Du måste öppna vissa utgående portar i serverns brandvägg så att Programstatistiken SDK och/eller Statusövervakaren kan skicka data till portalen:

| Syfte | URL | IP-adress | Portar |
| --- | --- | --- | --- |
| Telemetri |dc.applicationinsights.azure.com<br/>dc.applicationinsights.microsoft.com<br/>dc.services.visualstudio.com |40.114.241.141<br/>104.45.136.42<br/>40.84.189.107<br/>168.63.242.221<br/>52.167.221.184<br/>52.169.64.244<br/>40.85.218.175<br/>104.211.92.54<br/>52.175.198.74<br/>51.140.6.23<br/>40.71.12.231<br/>13.69.65.22<br/>13.78.108.165<br/>13.70.72.233<br/>20.44.8.7<br/>13.86.218.248<br/>40.79.138.41<br/>52.231.18.241<br/>13.75.38.7<br/>102.133.155.50<br/>52.162.110.67<br/>191.233.204.248<br/>13.69.66.140<br/>13.77.52.29<br/>51.107.59.180<br/>40.71.12.235<br/>20.44.8.10<br/>40.71.13.169<br/>13.66.141.156<br/>40.71.13.170 | 443 |
| Live Metrics Stream (östra USA) |use.rt.prod.applicationinsights.trafficmanager.net |23.96.28.38<br/>13.92.40.198<br/>40.112.49.101<br/>40.117.80.207 |443 |
| Live Metrics Stream (Södra centrala USA) |ussc.rt.prod.applicationinsights.trafficmanager.net |157.55.177.6<br/>104.44.140.84<br/>104.215.81.124<br/>23.100.122.113 |443 |
| Live Metrics Stream (Norra Europa) |eun.rt.prod.applicationinsights.trafficmanager.net |40.115.103.168<br/>40.115.104.31<br/>40.87.140.215<br/>40.87.138.220 |443 |
| Live Metrics Stream (Västeuropa) |euw.rt.prod.applicationinsights.trafficmanager.net |13.80.134.255<br/>40.68.61.229<br/>23.101.69.223<br/>52.232.106.242 |443 |
| Live Metrics Stream (Östasien) |ase.rt.prod.applicationinsights.trafficmanager.net |23.100.90.7<br/>23.101.13.65<br/>23.101.0.142<br/>23.101.9.4 |443 |
| Live Metrics Stream (Sydostasien) |asse.rt.prod.applicationinsights.trafficmanager.net |207.46.224.101<br/>207.46.236.191<br/>137.116.151.139<br/>13.76.87.86 |443 |

## <a name="status-monitor"></a>Statusövervakare
Konfiguration av statusövervakare - behövs endast när du gör ändringar.

| Syfte | URL | IP-adress | Portar |
| --- | --- | --- | --- |
| Konfiguration |`management.core.windows.net` | |`443` |
| Konfiguration |`management.azure.com` | |`443` |
| Konfiguration |`login.windows.net` | |`443` |
| Konfiguration |`login.microsoftonline.com` | |`443` |
| Konfiguration |`secure.aadcdn.microsoftonline-p.com` | |`443` |
| Konfiguration |`auth.gfx.ms` | |`443` |
| Konfiguration |`login.live.com` | |`443` |
| Installation | `globalcdn.nuget.org`, `packages.nuget.org` ,`api.nuget.org/v3/index.json` `nuget.org`, `api.nuget.org`, `dc.services.vsallin.net` | |`443` |

## <a name="availability-tests"></a>Tillgänglighetstester
Det här är listan över adresser som [webbtester för tillgänglighet](../../azure-monitor/app/monitor-web-app-availability.md) körs från. Om du vill köra webbtester på din app, men webbservern är begränsad till att betjäna specifika klienter, måste du tillåta inkommande trafik från våra tillgänglighetstestservrar.

### <a name="service-tag"></a>Tjänsttagg

Om du använder Azure Network Security Groups lägger du bara till en **inkommande portregel** för att tillåta tillgänglighetstester för trafik från Application Insights genom att välja **Service Tag** som **Source** och **ApplicationInsightsAvailability** som **source-tjänsttagg**.

>[!div class="mx-imgBorder"]
>![Under inställningar väljer du Regler för inkommande säkerhet och väljer sedan lägg till högst upp på fliken](./media/ip-addresses/add-inbound-security-rule.png)

>[!div class="mx-imgBorder"]
>![Fliken Lägg till inkommande säkerhetsregel](./media/ip-addresses/add-inbound-security-rule2.png)

Öppna portarna 80 (http) och 443 (https) för inkommande trafik från dessa adresser (IP-adresser grupperas efter plats):

### <a name="addresses-grouped-by-location"></a>Adresser grupperade efter plats

> [!NOTE]
> Dessa adresser visas med CIDR-notation (Classless Inter-Domain Routing). Det innebär att `51.144.56.112/28` en post som motsvarar 16 IPs som börjar på `51.144.56.112` och slutar på `51.144.56.127`.

```
Australia East
20.40.124.176/28
20.40.124.240/28
20.40.125.80/28

Brazil South
191.233.26.176/28
191.233.26.128/28
191.233.26.64/28

France Central (Formerly France South)
20.40.129.96/28
20.40.129.112/28
20.40.129.128/28
20.40.129.144/28

France Central
20.40.129.32/28
20.40.129.48/28
20.40.129.64/28
20.40.129.80/28

East Asia
52.229.216.48/28
52.229.216.64/28
52.229.216.80/28

North Europe
52.158.28.64/28
52.158.28.80/28
52.158.28.96/28
52.158.28.112/28

Japan East
52.140.232.160/28
52.140.232.176/28
52.140.232.192/28

West Europe
51.144.56.96/28
51.144.56.112/28
51.144.56.128/28
51.144.56.144/28
51.144.56.160/28
51.144.56.176/28

UK South
51.105.9.128/28
51.105.9.144/28
51.105.9.160/28

UK West
20.40.104.96/28
20.40.104.112/28
20.40.104.128/28
20.40.104.144/28

Southeast Asia
52.139.250.96/28
52.139.250.112/28
52.139.250.128/28
52.139.250.144/28

West US
40.91.82.48/28
40.91.82.64/28
40.91.82.80/28
40.91.82.96/28
40.91.82.112/28
40.91.82.128/28

Central US
13.86.97.224/28
13.86.97.240/28
13.86.98.48/28
13.86.98.0/28
13.86.98.16/28
13.86.98.64/28

North Central US
23.100.224.16/28
23.100.224.32/28
23.100.224.48/28
23.100.224.64/28
23.100.224.80/28
23.100.224.96/28
23.100.224.112/28
23.100.225.0/28

South Central US
20.45.5.160/28
20.45.5.176/28
20.45.5.192/28
20.45.5.208/28
20.45.5.224/28
20.45.5.240/28

East US
20.42.35.32/28
20.42.35.64/28
20.42.35.80/28
20.42.35.96/28
20.42.35.112/28
20.42.35.128/28

```  

## <a name="application-insights-api"></a>API för programinsikter
| Syfte | URI | IP-adress | Portar |
| --- | --- | --- | --- |
| API |api.applicationinsights.io<br/>api1.applicationinsights.io<br/>api2.applicationinsights.io<br/>api3.applicationinsights.io<br/>api4.applicationinsights.io<br/>api5.applicationinsights.io |23.96.58.253<br/>13.78.151.158<br/>40.74.59.40<br/>40.70.42.246<br/>40.117.198.0<br/>137.116.226.91<br/>52.163.88.44<br/>52.189.210.240<br/>13.77.201.34<br/>13.78.149.206<br/>52.232.28.146<br/>52.175.241.170<br/>20.36.36.66<br/>52.147.29.101<br/>40.115.155.252<br/>20.188.34.152<br/>52.141.32.103 |80 443 |
| API-dokument |dev.applicationinsights.io<br/>dev.applicationinsights.microsoft.com<br/>dev.aisvc.visualstudio.com<br/>www.applicationinsights.io<br/>www.applicationinsights.microsoft.com<br/>www.aisvc.visualstudio.com |23.96.58.253<br/>13.78.151.158<br/>40.74.59.40<br/>40.70.42.246<br/>40.117.198.0<br/>137.116.226.91<br/>52.163.88.44<br/>52.189.210.240<br/>13.77.201.34<br/>13.78.149.206<br/>52.232.28.146<br/>52.175.241.170<br/>20.36.36.66<br/>52.147.29.101<br/>40.115.155.252<br/>20.188.34.152<br/>52.141.32.103 |80 443 |
| Tillägg för Azure Pipeline-anteckningar |aigs1.aisvc.visualstudio.com |dynamisk|443 |

## <a name="log-analytics-api"></a>Api för logganalys

| Syfte | URI | IP-adress | Portar |
| --- | --- | --- | --- |
| API |api.loganalytics.io<br/>*.api.loganalytics.io |23.96.58.253<br/>13.78.151.158<br/>40.74.59.40<br/>40.70.42.246<br/>40.117.198.0<br/>137.116.226.91<br/>52.163.88.44<br/>52.189.210.240<br/>13.77.201.34<br/>13.78.149.206<br/>52.232.28.146<br/>52.175.241.170<br/>20.36.36.66<br/>52.147.29.101<br/>40.115.155.252<br/>20.188.34.152<br/>52.141.32.103 |80 443 |
| API-dokument |dev.loganalytics.io<br/>docs.loganalytics.io<br/>www.loganalytics.io |23.96.58.253<br/>13.78.151.158<br/>40.74.59.40<br/>40.70.42.246<br/>40.117.198.0<br/>137.116.226.91<br/>52.163.88.44<br/>52.189.210.240<br/>13.77.201.34<br/>13.78.149.206<br/>52.232.28.146<br/>52.175.241.170<br/>20.36.36.66<br/>52.147.29.101<br/>40.115.155.252<br/>20.188.34.152<br/>52.141.32.103 |80 443 |

## <a name="application-insights-analytics"></a>Programinsiktsanalys

| Syfte | URI | IP-adress | Portar |
| --- | --- | --- | --- |
| Analysportal | analytics.applicationinsights.io | dynamisk | 80 443 |
| CDN | applicationanalytics.azureedge.net | dynamisk | 80 443 |
| Media CDN | applicationanalyticsmedia.azureedge.net | dynamisk | 80 443 |

Domänen *.applicationinsights.io ägs av Application Insights-teamet.

## <a name="log-analytics-portal"></a>Logga Analytics Portal

| Syfte | URI | IP-adress | Portar |
| --- | --- | --- | --- |
| Portalen | portal.loganalytics.io | dynamisk | 80 443 |
| CDN | applicationanalytics.azureedge.net | dynamisk | 80 443 |

*.loganalytics.io domän ägs av Log Analytics-teamet.

## <a name="application-insights-azure-portal-extension"></a>Programinsikter Azure portal tillägg

| Syfte | URI | IP-adress | Portar |
| --- | --- | --- | --- |
| Tillägg för Programinsikter | stamp2.app.insightsportal.visualstudio.com | dynamisk | 80 443 |
| Cdn-tillägg för programinsikter | insightsportal-prod2-cdn.aisvc.visualstudio.com<br/>insightsportal-prod2-asiae-cdn.aisvc.visualstudio.com<br/>insightsportal-cdn-aimon.applicationinsights.io | dynamisk | 80 443 |

## <a name="application-insights-sdks"></a>SDK:er för programinsikter

| Syfte | URI | IP-adress | Portar |
| --- | --- | --- | --- |
| Programinsikter JS SDK CDN | az416426.vo.msecnd.net | dynamisk | 80 443 |
| Programinsikter Java SDK | aijavasdk.blob.core.windows.net | dynamisk | 80 443 |

## <a name="alert-webhooks"></a>Avisera webhooks

| Syfte | IP-adress | Portar
| --- | --- | --- |
| Aviseringar | 23.96.11.4 | 443 |

## <a name="profiler"></a>Profilerare

| Syfte | URI | IP-adress | Portar |
| --- | --- | --- | --- |
| Agent | agent.azureserviceprofiler.net<br/>*.agent.azureserviceprofiler.net | 20.190.60.38<br/>20.190.60.32<br/>52.173.196.230<br/>52.173.196.209<br/>23.102.44.211<br/>23.102.45.216<br/>13.69.51.218<br/>13.69.51.175<br/>138.91.32.98<br/>138.91.37.93<br/>40.121.61.208<br/>40.121.57.2<br/>51.140.60.235<br/>51.140.180.52<br/>52.138.31.112<br/>52.138.31.127<br/>104.211.90.234<br/>104.211.91.254<br/>13.70.124.27<br/>13.75.195.15<br/>52.185.132.101<br/>52.185.132.170<br/>20.188.36.28<br/>40.89.153.171<br/>52.141.22.239<br/>52.141.22.149<br/>102.133.162.233<br/>102.133.161.73<br/>191.232.214.6<br/>191.232.213.239 | 443
| Portalen | gateway.azureserviceprofiler.net | dynamisk | 443
| Storage | *.core.windows.net | dynamisk | 443

## <a name="snapshot-debugger"></a>Felsökning av ögonblicksbild

> [!NOTE]
> Profiler och Snapshot Debugger delar samma uppsättning IP-adresser.

| Syfte | URI | IP-adress | Portar |
| --- | --- | --- | --- |
| Agent | ppe.azureserviceprofiler.net<br/>*.ppe.azureserviceprofiler.net | 20.190.60.38<br/>20.190.60.32<br/>52.173.196.230<br/>52.173.196.209<br/>23.102.44.211<br/>23.102.45.216<br/>13.69.51.218<br/>13.69.51.175<br/>138.91.32.98<br/>138.91.37.93<br/>40.121.61.208<br/>40.121.57.2<br/>51.140.60.235<br/>51.140.180.52<br/>52.138.31.112<br/>52.138.31.127<br/>104.211.90.234<br/>104.211.91.254<br/>13.70.124.27<br/>13.75.195.15<br/>52.185.132.101<br/>52.185.132.170<br/>20.188.36.28<br/>40.89.153.171<br/>52.141.22.239<br/>52.141.22.149<br/>102.133.162.233<br/>102.133.161.73<br/>191.232.214.6<br/>191.232.213.239 | 443
| Portalen | ppe.gateway.azureserviceprofiler.net | dynamisk | 443
| Storage | *.core.windows.net | dynamisk | 443
