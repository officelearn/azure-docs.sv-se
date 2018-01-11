---
title: "Översikt över hybridanslutningar | Microsoft Docs"
description: "Läs mer om hybridanslutningar, säkerhet, TCP-portar och konfigurationer som stöds. MABS, WABS."
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: erikre
editor: 
ms.assetid: 216e4927-6863-46e7-aa7c-77fec575c8a6
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/18/2016
ms.author: ccompy
ms.openlocfilehash: 819af52bb10c9ffcb7e1133437f6d0afbe6105ae
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="hybrid-connections-overview"></a>Översikt över hybridanslutningar

> [!IMPORTANT]
> BizTalk-Hybridanslutningar har dragits tillbaka och ersatts med App Service Hybrid-anslutningar. Mer information, inklusive hur du hanterar din befintliga BizTalk Hybridanslutningar finns i [Hybridanslutningar för Azure App Service](../app-service/app-service-hybrid-connections.md).

Introduktion till hybridanslutningar, en lista med konfigurationer som stöds och en lista med nödvändiga TCP-portar.

## <a name="what-is-a-hybrid-connection"></a>Vad är en hybridanslutning?
Hybridanslutningar är en funktion i Azure BizTalk Services. Hybridanslutningar är ett enkelt och praktiskt sätt att ansluta Web Apps-funktionen i Azure Apptjänst (tidigare Websites) och Mobile Apps-funktionen i Azure Apptjänst (tidigare Mobile Services) till lokala resurser bakom brandväggen.

![Hybridanslutningar][HCImage]

Fördelarna med hybridanslutningar är:

* Web Apps och Mobile Apps får åtkomst till befintliga lokala data och tjänster på ett säkert sätt.
* Flera Web Apps eller Mobile Apps kan dela en hybridanslutning för att få åtkomst till en lokal resurs.
* Minimalt antal TCP-portar krävs för att ansluta till nätverket.
* Program som använder hybridanslutningar har endast åtkomst till specifika lokala resurser som publiceras via hybridanslutningen.
* Kan ansluta till alla lokala resurser som använder en statisk TCP-port, som t.ex. SQL Server, MySQL, HTTP-webb-API:er och de flesta anpassade webbtjänster.
  
  > [!NOTE]
  > TCP-baserade tjänster som använder dynamiska portar (till exempel Inaktivt läge för FTP eller Utökat inaktivt läge) stöds inte för närvarande. LDAP stöds inte heller. LDAP använder en statisk TCP-port, men kan också använda UDP. Därför stöds den inte.
  > 
  > 
* Kan användas med alla ramverk som stöds av Web Apps (.NET, PHP, Java, Python, Node.js) och Mobile Apps (Node.js, .NET).
* Web Apps och Mobile Apps har åtkomst till lokala resurser på samma sätt som om webb- eller mobilappen fanns i det lokala nätverket. Exempelvis kan samma anslutningssträng som används lokalt även användas i Azure.

Hybridanslutningar ger också företagsadministratörer bättre inblick i företagets resurser som kan nås av hybridprogram, inklusive:

* Med grupprincipinställningar kan administratörer tillåta hybridanslutningar i nätverket och också tilldela resurser som kan nås av hybridprogram.
* Händelse- och granskningsloggar i företagsnätverket ger bättre inblick i de resurser som hybridanslutningarna har åtkomst till.

## <a name="example-scenarios"></a>Exempelscenarier
Hybridanslutningar stöder följande kombinationer av ramverk och program:

* .NET-ramverksåtkomst till SQL Server
* .NET-ramverksåtkomst till HTTP/HTTPS-tjänster med WebClient
* PHP-åtkomst till SQL Server, MySQL
* Java-åtkomst till SQL Server, MySQL och Oracle
* Java-åtkomst till HTTP/HTTPS-tjänster

När du använder hybridanslutningar för att få åtkomst till en lokal SQL Server bör du tänka på följande:

* Namngivna SQL Express-instanser måste konfigureras till att använda statiska portar. Som standard använder namngivna SQL Express-instanser dynamiska portar.
* Standardinstanser i SQL Express använder en statisk port, men TCP måste vara aktiverat. TCP är inte aktiverat som standard.
* När du använder klustring eller tillgänglighetsgrupper stöds inte `MultiSubnetFailover=true`-läget för närvarande.
* `ApplicationIntent=ReadOnly` stöds inte för närvarande.
* SQL-autentisering kan krävas eftersom auktoriseringsmetoden från slutpunkt till slutpunkt stöds av Azure-programmet och den lokala SQL-servern.

## <a name="security-and-ports"></a>Säkerhet och portar
Hybridanslutningar använder auktorisering med signatur för delad åtkomst (SAS) till att skydda anslutningarna från Azure-program och den lokala hybridanslutningshanteraren till hybridanslutningen. Nycklar för separat anslutning skapas för programmet och den lokala hybridanslutningshanteraren. Anslutningsnycklarna kan återställas och återkallas fristående.

Med hybridanslutningar sker en sömlös och säker distribution av nycklar till programmen och den lokala hybridanslutningshanteraren.

Se [Skapa och hantera hybridanslutningar](integration-hybrid-connection-create-manage.md).

*Programauktoriseringen är fristående från hybridanslutningen*. Någon av lämpliga auktoriseringsmetoder kan användas. Vilken auktoriseringsmetod som väljs beror på de slutpunkt-till-slutpunkts-auktoriseringsmetoder som stöds i Azure-molnet och i de lokala komponenterna. Till exempel kan ditt Azure-program ha åtkomst till en lokal SQL Server. I det här scenariot kanske SQL-auktorisering är den metod som stöds från slutpunkt till slutpunkt.

#### <a name="tcp-ports"></a>TCP-portar
Hybridanslutningar kräver endast utgående TCP- eller HTTP-anslutning från det privata nätverket. Du behöver inte öppna portar i brandväggen eller ändra nätverkets perimeterkonfiguration till att tillåta inkommande anslutningar i nätverket.

Följande TCP-portar används av hybridanslutningar:

| Port | Varför du behöver den |
| --- | --- |
| 9350–9354 |Dessa portar används till dataöverföring. Service Bus Relay-hanteraren avsöker port 9350 för att se om TCP-anslutningen är tillgänglig. Om den är tillgänglig förutsätts det att även port 9352 är tillgänglig. Datatrafik går via port 9352. <br/><br/>Tillåt utgående anslutningar till dessa portar. |
| 5671 |När port 9352 används för datatrafik, används port 5671 som kontrollkanal. <br/><br/>Tillåt utgående anslutningar till denna port. |
| 80, 443 |Dessa portar används för vissa databegäranden till Azure. Om portarna 9352 och 5671 inte kan användas, kommer *ändå* portarna 80 och 443 vara återställningsportar för dataöverföring och kontrollkanal.<br/><br/>Tillåt utgående anslutningar till dessa portar. <br/><br/>**Observera** Vi rekommenderar inte att använda dessa som återställningsportar i stället för de andra TCP-portarna. HTTP/WebSocket används som protokoll i stället för intern TCP för datakanaler. Det kan leda till lägre prestanda. |

## <a name="next-steps"></a>Nästa steg
[Skapa och hantera hybridanslutningar](integration-hybrid-connection-create-manage.md)

## <a name="see-also"></a>Se även
[REST API för att hantera BizTalk Services i Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)  
[BizTalk Services: Diagram över utgåvor](biztalk-editions-feature-chart.md)  
[Skapa en BizTalk-tjänst](biztalk-provision-services.md)  
[BizTalk Services: Flikarna Instrumentpanel, Övervakare och Skalning](biztalk-dashboard-monitor-scale-tabs.md)  

[HCImage]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionImage.png
