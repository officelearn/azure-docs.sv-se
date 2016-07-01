<properties 
    pageTitle="Vad är Logic Apps?" 
    description="Läs mer om Apptjänst Logic Apps" 
    authors="kevinlam1" 
    manager="dwrede" 
    editor="" 
    services="app-service\logic" 
    documentationCenter=""/>

<tags
    ms.service="app-service-logic"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article" 
    ms.date="04/07/2016"
    ms.author="klam"/>

#Vad är Logic Apps?

| Snabbreferens |
| --------------- |
| [Definitionsspråk för Logic Apps](https://msdn.microsoft.com/library/azure/mt643789.aspx) |
| [Dokumentation om logikappskopplingar](../connectors/apis-list.md) |
| [Forum för Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) |

Azure Apptjänst är en helt hanterad PaaS (plattform som en tjänst) för utvecklare som gör det enklare att skapa webb-, mobil- och integrationsappar. Logic Apps är en del av den här sviten och de gör det möjligt för tekniska användare och utvecklare att automatisera affärsprocesskörningar och arbetsflöden med hjälp av en lättanvänd visuell designer.

Det bästa är att Logic Apps kan kombineras med inbyggda [hanterade API:er][managedapis] så att du enkelt kan komma till rätta med komplicerade integrationsscenarier: 

![Designer för flödesappar](./media/app-service-logic-what-are-logic-apps/LogicAppCapture2.png)

Som tidigare nämnts kan du med hjälp av Logic Apps automatisera affärsprocesser. Här följer några exempel:  
 
* Du kan automatiskt replikera nya poster i din SQL-databas och sedan skicka e-post till receptionen.   
* Automatiskt hitta negativa tweets och skicka dem till en Slack-kanal.

Alla scenarier av den här typen kan konfigureras från den visuella designern utan att du behöver skriva en enda rad kod. Kom igång med att [skapa din logikapp nu][skapa].

## Vad är Logic Apps till för?

Logic Apps gör det möjligt för utvecklare att utforma arbetsflöden som startar från en utlösare och sedan kör en serie steg. Varje steg anropar en API och tar samtidigt på ett säkert sätt hand om autentisering och bästa praxis, till exempel kontrollpunkter och varaktig körning.

Om du vill automatisera en affärsprocess (t.ex. hitta negativa tweets och skicka dem till din interna Slack-kanal eller replikera nya kundposter från SQL till CRM-systemet samtidigt som de kommer in) blir det med Logic Apps enkelt att integrera olika datakällor, från molnet till lokala källor. Kolla in våra [hanterade API:er][managedapis] för mer inspiration och [kom igång][skapa] nu och se vad du kan göra. 

Med våra [BizTalk-hanterade API:er][biztalk] kan du skala till mogna integrationsscenarier med kraften i en [regelmotor][regler], i [hantering av handelspartners][tpm] och mycket mer.

- **Lättanvända designverktyg** – Logic Apps kan skapas från grunden till färdig produkt i webbläsaren. Starta med en utlösare – från ett enkelt schema till när en tweet om ditt företag dyker upp. Sedan kan du dirigera valfritt antal åtgärder med hjälp av det utförliga galleriet med kopplingar.

- **Skriv SaaS enkelt** – Till och med skrivuppgifter som är lätta att beskriva är svåra att implementera i kod. Logic Apps gör det lätt att ansluta olika system. Vill du skapa en uppgift i din CRM-programvara som baseras på aktiviteten i dina Facebook- eller Twitter-konton? Vill du ansluta din marknadsföringslösning i molnet till ditt lokala faktureringssystem? Logic Apps är det snabbaste och mest tillförlitliga sättet att leverera lösningar på sådana här problem.

- **Kom igång snabbt med mallar** – För att hjälpa dig att komma igång har vi skapat ett [galleri med mallar][mallar] med vars hjälp du snabbt kan skapa ett antal vanliga lösningar. Med allt från avancerade BizTalk-lösningar till enkel SaaS-anslutningsbarhet, och till och med några lösningar bara för skojs skull, är galleriet det snabbaste sättet att förstå hur kraftfulla Logic Apps är.

- **Inbyggd utökningsbarhet** – Hittar du inte den API du behöver? Logic Apps är utformade för att fungera med API Apps. Du kan enkelt skapa din egen API-app och använda den som en anpassad API. Skapa en ny app som bara du använder, eller dela och tjäna pengar på marknadsplatsen.

- **Verklig integrationskraft** – Starta enkelt och utöka i takt med dina behov. Logic Apps kan enkelt utnyttja kraften i BizTalk, Microsofts branschledande integrationslösning, och gör det möjligt för integrationstekniker att skapa de lösningar de behöver. Lär dig mer om [BizTalk-funktionerna som finns i Logic Appsna][biztalk].

## Logikappkoncept

Följande är några av de viktiga punkter som ingår i logikappupplevelsen. 

- **Arbetsflöde** – Med Logic Apps kan du på ett grafiskt sätt beskriva dina affärsprocesser som en serie steg eller ett arbetsflöde.
- **Hanterade API:er** – Dina Logic Apps behöver åtkomst till data och tjänster. Hanterade API:er är speciellt utformade för att hjälpa dig när du ansluter till och arbetar med dina data. Se en lista över API:er som för tillfället finns tillgängliga i [hanterade API:er][managedapis].
- **Utlösare** – Vissa hanterade API:er kan också fungera som utlösare. En utlösare startar en ny instans av ett arbetsflöde baserat på en händelse som till exempel ankomsten av ett mejl eller en ändring i ditt Azure Storage-konto.
-  **Åtgärder** – Alla steg efter utlösaren i ett arbetsflöde kallas för åtgärder. Alla åtgärder mappar vanligtvis till en åtgärd i dina hanterade eller anpassade API Apps.
- **BizTalk** – För mer avancerade integrationsscenarier innehåller Logic Apps funktioner från Biztalk. BizTalk är Microsofts branschledande integrationsplattform. Med BizTalk-API Appsna kan du enkelt inkludera verifiering, transformation, regler och mycket mer i dina logikappsarbetsflöden. Läs mer i [Vad är BizTalk-API Apps][biztalk].

## Komma igång  

 - Kom igång med Logic Apps genom att följa kursen [skapa en logikapp][skapa].  
 - [Visa vanliga exempel och scenarier](app-service-logic-examples-and-scenarios.md)
 - [Du kan automatisera affärsprocesser med hjälp av Logic Apps](http://channel9.msdn.com/Events/Build/2016/T694) 
 - [Lär dig hur du integrerar dina system med Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462)
- Mer information om Azure Apptjänst-plattformen finns på [Azure Apptjänst][appservice].

[biztalk]: app-service-logic-what-are-biztalk-api-apps.md
[appservice]: ../app-service/app-service-value-prop-what-is.md
[skapa]: app-service-logic-create-a-logic-app.md
[managedapis]: ../connectors/apis-list.md
[tpm]: app-service-logic-create-a-trading-partner-agreement.md
[regler]: app-service-logic-use-biztalk-rules.md
[mallar]: app-service-logic-use-logic-app-templates.md



<!--HONumber=Jun16_HO2-->


