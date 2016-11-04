---
title: Vad är Logic Apps?
description: Läs mer om Apptjänst Logic Apps
author: kevinlam1
manager: dwrede
editor: ''
services: logic-apps
documentationcenter: ''

ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 10/12/2016
ms.author: klam

---
# <a name="what-are-logic-apps?"></a>Vad är Logic Apps?
Logic Apps är ett sätt att förenkla och implementera skalbara integreringar och arbetsflöden i molnet. Tjänsten tillhandahåller ett visuellt designverktyg för modellering och automatisering av processer som en serie steg kallade ett arbetsflöde.  Det finns [många anslutningsappar](../connectors/apis-list.md) för molnet och lokala infrastrukturer för snabb integrering mellan tjänster och protokoll.  En logikapp börjar med en utlösare (t.ex. ”när ett konto läggs till i Dynamics CRM”) och kan när den utlösts starta många kombinationer av åtgärder, konverteringar och villkorslogik.

Här är några exempel på fördelarna med att använda Logic Apps:  

* Spara tid genom att skapa komplexa processer med användarvänliga designverktyg.
* Implementera mönster och arbetsflöden sömlöst som annars skulle vara svåra att implementera i kod.
* Kom snabbt igång med hjälp av mallar.
* Anpassa din logikapp med egna anpassade API:er, kod och åtgärder.
* Anslut och synkronisera separata system i den lokala infrastrukturen och i molnet.
* Utveckla med BizTalk Server, API Management, Azure Functions och Azure Service Bus med förstklassigt integrationsstöd.

Logic Apps är en helt hanterad iPaaS (integration Platform as a Service) som gör att utvecklare slipper bekymra sig om värdhantering, skalbarhet, tillgänglighet och hantering.  Logic Apps skalas upp automatiskt baserat på efterfrågan.

![Designer för flödesappar](./media/app-service-logic-what-are-logic-apps/LogicAppCapture2.png)

Som vi redan nämnt kan du automatisera affärsprocesser med Logic Apps. Här följer några exempel:  

* Flytta filer som laddats upp till en FTP-server till Azure Storage.
* Behandla och dirigera order i lokala system och molnsystem.
* Övervaka alla tweets om ett visst ämne, analysera sentiment och skapa aviseringar och aktiviteter för objekt som kräver uppföljning.

Alla scenarier av den här typen kan konfigureras från den visuella designern utan att du behöver skriva en enda rad kod. Kom igång med att [skapa din logikapp nu][skapa].  När den har skrivits kan en logikapp [snabbt distribueras och konfigureras om](app-service-logic-create-deploy-template.md) i flera miljöer och områden.

## <a name="why-logic-apps?"></a>Vad är Logic Apps till för?
Logic Apps levererar hastighet och skalbarhet för företagsintegreringar.  Det användarvänliga designverktyget, de många tillgängliga utlösarna och åtgärderna och de kraftfulla hanteringsverktygen gör det enklare än någonsin att centralisera dina API:er.  Företag som rör sig mot digitalisering kan använda Logic Apps för att koppla ihop äldre och moderna system.

Med vårt [Enterprise-integrationskonto][biztalk] kan du dessutom skala till mogna integrationsscenarier med kraften i [XML-meddelanden][xml], [partnerhantering][tpm] och mer.

* **Användarvänligt designverktyg** – Logic Apps kan konfigureras från slutpunkt till slutpunkt i webbläsaren eller med Visual Studio-verktyg. Börja med en utlösare – från ett enkelt schema till uppkomsten av ett GitHub-problem. Sedan kan du dirigera valfritt antal åtgärder med hjälp av det utförliga galleriet med kopplingar.
* **API-anslutning** – Till och med skrivuppgifter som är lätta att beskriva är svåra att implementera i kod. Logic Apps gör det enkelt att ansluta olika system. Vill du ansluta din marknadsföringslösning i molnet till ditt lokala faktureringssystem? Vill du centralisera meddelandehanteringen mellan API:er och system med en Enterprise Service Bus? Logic Apps är det snabbaste och mest tillförlitliga sättet att leverera lösningar på sådana här problem.
* **Kom igång snabbt med mallar** – För att hjälpa dig att komma igång har vi skapat ett [galleri med mallar][mallar] med vars hjälp du snabbt kan skapa ett antal vanliga lösningar. Med allt från avancerade B2B-lösningar till enkla SaaS-anslutningar, och några lösningar bara för skojs skull, är galleriet det snabbaste sättet att komma igång med och utnyttja kraften i Logic Apps.
* **Inbyggd skalbarhet** – Ser du inte den anslutningsapp som du behöver? Logic Apps är utformat att fungera med dina API:er och din kod. Du kan enkelt skapa en egen API-app och använda som en anpassad anslutningsapp eller göra ett anrop till en [Azure-funktion](https://functions.azure.com) för att köra kodfragment på begäran. 
* **Verklig integrationskraft** – Starta enkelt och utöka i takt med dina behov. Logic Apps kan enkelt utnyttja kraften i BizTalk, Microsofts branschledande integrationslösning, och gör det möjligt för integrationstekniker att skapa de lösningar de behöver. Lär dig mer om [Enterprise-integrationspaketet](app-service-logic-enterprise-integration-overview.md).

## <a name="logic-app-concepts"></a>Logikappkoncept
Följande är några av de viktiga punkter som ingår i logikappupplevelsen. 

* **Arbetsflöde** – Med Logic Apps kan du på ett grafiskt sätt beskriva dina affärsprocesser som en serie steg eller ett arbetsflöde.
* **Hanterade anslutningsappar** – Dina logikappar behöver åtkomst till data och tjänster. Hanterade anslutningsappar är speciellt utformade för att hjälpa dig när du ansluter till och arbetar med dina data. En lista över tillgängliga anslutningsappar finns i [Hanterade anslutningsappar][managedapis].
* **Utlösare** – Vissa hanterade anslutningsappar kan också fungera som utlösare. En utlösare startar en ny instans av ett arbetsflöde baserat på en händelse som till exempel ankomsten av ett mejl eller en ändring i ditt Azure Storage-konto.
* **Åtgärder** – Alla steg efter utlösaren i ett arbetsflöde kallas för åtgärder. En åtgärd mappar vanligtvis till en åtgärd i dina hanterade anslutningsappar eller anpassade API-appar.
* **Enterprise-integrationspaket** – Logic Apps innehåller funktioner från BizTalk för mer avancerade integrationsscenarier. BizTalk är Microsofts branschledande integrationsplattform. Med anslutningsapparna för Enterprise-integrationspaketet kan du enkelt integrera verifiering, transformering och mer i dina Logic Apps-arbetsflöden.

## <a name="getting-started"></a>Komma igång
* Kom igång med Logic Apps genom att följa kursen [skapa en logikapp][skapa].  
* [Visa vanliga exempel och scenarier](app-service-logic-examples-and-scenarios.md)
* [Du kan automatisera verksamhetsprocesser med Logic Apps](http://channel9.msdn.com/Events/Build/2016/T694) 
* [Läs hur du integrerar dina system med Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462)

[biztalk]: app-service-logic-enterprise-integration-accounts.md
[appservice]: ../app-service/app-service-value-prop-what-is.md
[skapa]: app-service-logic-create-a-logic-app.md
[managedapis]: ../connectors/apis-list.md
[tpm]: app-service-logic-enterprise-integration-accounts.md
[xml]: app-service-logic-enterprise-integration-b2b.md
[mallar]: app-service-logic-use-logic-app-templates.md



<!--HONumber=Oct16_HO3-->


