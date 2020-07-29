---
title: Lokal datagateway för Azure Analysis Services | Microsoft Docs
description: En lokal gateway krävs om din Analysis Services-server i Azure ska ansluta till lokala data källor.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 648646b6f973762245c344cd2629a874a219b170
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "76310160"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>Ansluta till lokala data källor med lokal datagateway

Den lokala datagatewayen ger säker data överföring mellan lokala data källor och dina Azure Analysis Services-servrar i molnet. Förutom att arbeta med flera Azure Analysis Services-servrar i samma region, fungerar den senaste versionen av gatewayen också med Azure Logic Apps, Power BI, Power Apps och automatiserad energi. Även om den gateway som du installerar är samma för alla dessa tjänster, Azure Analysis Services och Logic Apps har ytterligare steg.

Informationen som anges här är speciell för hur Azure Analysis Services fungerar med den lokala datagatewayen. Mer information om gatewayen i allmänhet och hur det fungerar med andra tjänster finns i [Vad är en lokal datagateway?](/data-integration/gateway/service-gateway-onprem).

För Azure Analysis Services hämtar installationen med gatewayen första gången en process i fyra delar:

- **Hämta och kör installationen** – det här steget installerar en gateway-tjänst på en dator i din organisation. Du loggar också in på Azure med ett konto i [klient organisationens](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) Azure AD. Konton för Azure B2B (gäst) stöds inte.

- **Registrera din gateway** – i det här steget anger du ett namn och en återställnings nyckel för din gateway och väljer en region, registrerar din gateway med moln tjänsten Gateway. Din gateway-resurs kan registreras i vilken region som helst, men det rekommenderas att den finns i samma region som dina Analysis Services-servrar. 

- **Skapa en gateway-resurs i Azure** – i det här steget skapar du en gateway-resurs i Azure.

- **Anslut dina servrar till din gateway-resurs** – när du har en gateway-resurs kan du börja ansluta servrar till den. Du kan ansluta flera servrar och andra resurser förutsatt att de finns i samma region.



## <a name="how-it-works"></a><a name="how-it-works"> </a>Så här fungerar det
Den gateway som du installerar på en dator i din organisation körs som en Windows-tjänst, **lokal datagateway**. Den här lokala tjänsten registreras för gatewaymolntjänsten via Azure Service Bus. Sedan skapar du en lokal datagateway-resurs för din Azure-prenumeration. Dina Azure Analysis Services-servrar ansluts sedan till din Azure gateway-resurs. När modeller på servern måste ansluta till dina lokala data källor för frågor eller bearbetning, passerar en fråga och ett data flöde Gateway-resursen, Azure Service Bus, lokal datagateway-tjänst och data källor. 

![Så här fungerar det](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Frågor och dataflöde:

1. En fråga skapas av molntjänsten med de krypterade autentiseringsuppgifterna för den lokala datakällan. Därefter skickas den till en kö för gatewaybehandling.
2. Gateway-molntjänsten analyserar frågan och skickar en begäran till [Azure Service Bus](https://azure.microsoft.com/documentation/services/service-bus/).
3. Den lokala datagatewayen avsöker Azure Service Bus för väntande frågor.
4. Gatewayen hämtar frågan, dekrypterar autentiseringsuppgifterna och ansluter till datakällorna med autentiseringsuppgifterna.
5. Gatewayen skickar frågan till datakällan för körning.
6. Resultaten skickas från datakällan tillbaka till gatewayen och sedan till molntjänsten och din server.

## <a name="installing"></a>Installerar

När du installerar för en Azure Analysis Services-miljö är det viktigt att du följer stegen som beskrivs i [Installera och konfigurera en lokal datagateway för Azure Analysis Services](analysis-services-gateway-install.md). Den här artikeln är unik för Azure Analysis Services. Den innehåller ytterligare steg som krävs för att konfigurera en lokal datagateway-resurs i Azure och ansluta din Azure Analysis Services-server till resursen.

## <a name="ports-and-communication-settings"></a>Portar och kommunikations inställningar

Gatewayen skapar en utgående anslutning till Azure Service Bus. Den kommunicerar via utgående portar: TCP 443 (standard), 5671, 5672, 9350 till 9354.  Gatewayen behöver inte några ingående portar.

Du kan behöva inkludera IP-adresser för ditt data område i brand väggen. Du kan ladda ned [IP-listan för Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=56519). Listan uppdateras en gång i veckan. De IP-adresser som finns i IP-listan för Azure Datacenter använder CIDR-notering. Mer information finns i [Interplatsroutning mellan domäner](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Följande är fullständigt kvalificerade domän namn som används av gatewayen.

| Domännamn | Utgående portar | Beskrivning |
| --- | --- | --- |
| *.powerbi.com |80 |HTTP används för att hämta installationsprogrammet. |
| *.powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *. login.windows.net, login.live.com, aadcdn.msauth.net |443 |HTTPS |
| *.servicebus.windows.net |5671–5672 |Advanced Message Queuing Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350–9354 |Lyssnare på Service Bus Relay via TCP (kräver 443 för att hämta token för åtkomstkontroll) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |Används för att testa Internetanslutningen om denna gateway inte kan nås av Power BI-tjänsten. |
| *.microsoftonline-p.com |443 |Används för autentisering beroende på konfiguration. |
| dc.services.visualstudio.com  |443 |Används av AppInsights för att samla in telemetri. |

### <a name="forcing-https-communication-with-azure-service-bus"></a><a name="force-https"></a>Tvinga HTTPS-kommunikation med Azure Service Bus

Du kan tvinga gatewayen att kommunicera med Azure Service Bus genom att använda HTTPS i stället för direkt TCP. Det kan dock försämra prestanda avsevärt. Du kan ändra *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* -filen genom att ändra värdet från `AutoDetect` till `Https` . Den här filen finns vanligt vis i *C:\Program Files\On-premises data Gateway*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="next-steps"></a>Nästa steg 

Följande artiklar ingår i den lokala datagatewayens allmänna innehåll som gäller för alla tjänster som gatewayen stöder:

* [Vanliga frågor och svar om lokal datagateway](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)   
* [Använda appen för lokal datagateway](https://docs.microsoft.com/data-integration/gateway/service-gateway-app)   
* [Administration på klientorganisationsnivå](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin)
* [Konfigurera proxyinställningar](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)   
* [Justera kommunikationsinställningar](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)   
* [Konfigurera loggfiler](https://docs.microsoft.com/data-integration/gateway/service-gateway-log-files)   
* [Felsöka](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [Övervaka och optimera gatewayprestanda](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)
