<properties
   pageTitle="Vad är Azure Data Catalog? | Microsoft Azure"
   description="Den här artikeln innehåller en översikt över Microsoft Azure Data Catalog, inklusive funktioner och vilka problem den är tänkt att lösa. Data Catalog innehåller funktioner som gör att alla användare – från analytiker till datavetare och utvecklare – kan registrera, identifiera, förstå och använda datakällor."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/21/2016"
   ms.author="maroche"/>


# Vad är Azure Data Catalog?

Azure Data Catalog är en helt hanterad molntjänst som hjälper användarna att identifiera de datakällor de behöver och förstå datakällorna de identifierar, samtidigt som organisationerna får ut mer av sina befintliga investeringar. Data Catalog innehåller funktioner som gör att alla användare – från analytiker till datavetare och utvecklare – kan identifiera, förstå och använda datakällor. Data Catalog innehåller en gemensam modell för metadata och kommentarer så att alla användare kan bidra med sina kunskaper för att skapa en databaserad gemenskap och kultur.

## Det är svårt för datakonsumenterna att hitta rätt

Traditionellt har man identifierat företagets datakällor genom en organisk process baserad på gruppens kunskaper. Detta medför stora utmaningar för företag som vill få ut största möjliga värde av sina informationstillgångar.

-   Användarna känner inte till att datakällorna finns om de inte stöter på dem som en del i en annan process. Det finns ingen central plats där datakällorna registreras.
-   Såvida inte en användare känner till var en datakälla finns kan han eller hon inte ansluta till den med ett klientprogram. Datakonsumtionen kräver att användarna känner till anslutningssträngen eller sökvägen.
-   Såvida inte en användare känner till var dokumentationen för en datakälla finns kan han eller hon inte förstå hur den är avsedd att användas. Datakällor och dokumentation finns på olika platser och används via olika kanaler.
-   Om en användare har frågor om en informationstillgång måste han eller hon leta rätt på experten eller teamet som ansvarar för den och kontakta dem öga mot öga. Det finns ingen uttrycklig koppling mellan data och personer med expertkunskaper om hur de används.
-  Om en användare inte känner till hur man begär åtkomst till datakällan, kan användaren inte komma åt de data som behövs trots att han eller hon har identifierat datakällan och dess dokumentation.

## Det är svårt för dataproducenterna att hitta rätt

Datakonsumenterna står inför vissa svårigheter, medan användarna som ansvarar för att producera och upprätthålla informationstillgångar står inför andra svårigheter.

-   Det är ofta en onödig ansträngning att kommentera datakällor med beskrivande metadata, eftersom klientprogram vanligen ignorerar beskrivningar som lagrats i datakällan.
-   Det är ofta en onödig ansträngning att skapa dokumentation för datakällor. Att se till att dokumentationen är synkroniserad med datakällan är ett kontinuerligt ansvar, och användarna saknar förtroende för dokumentationen eftersom den ofta uppfattas som föråldrad.
- Det är en ständig utmaning att begränsa tillgången till datakällan och samtidigt se till att datakonsumenterna vet hur de begär tillgång.

Det är komplicerat och tidskrävande att skapa och upprätthålla dokumentationen för en datakälla. Det är ofta ännu svårare att göra dokumentationen lättillgänglig för alla som använder datakällan.

I kombination utgör dessa svårigheter ett stort hinder för företag som vill uppmuntra och främja användning och förståelse av företagsdata.

## Azure Data Catalog kan hjälpa dig

Data Catalog är utformad för att åtgärda dessa problem och hjälpa företagen att få ut så mycket som möjligt av sina befintliga informationstillgångar. Data Catalog underlättar genom att göra det enkelt för användare som behöver data att identifiera och förstå datakällorna.

Data Catalog är en molnbaserad tjänst där du kan registrera datakällor. Dina data blir kvar där du har dem, men en kopia av deras metadata läggs till i Data Catalog tillsammans med en referens till platsen där datakällan finns. Dessa metadata indexeras också för att det ska bli enkelt att identifiera alla datakällor och för att användare som identifierar dem ska förstå dem.

När en datakälla har registrerats kan dess metadata utökas, antingen av användaren som registrerade källan eller av andra användare i företaget. Alla användare kan kommentera en datakälla genom att ange beskrivningar, taggar eller andra metadata, till exempel dokumentation och processer för att begära åtkomst till datakällan. Dessa beskrivande metadata kompletterar strukturella metadata (till exempel kolumnnamn och datatyper) som registrerats från datakällan.

Huvudsyftet med registrering av källorna är att identifiera och förstå datakällor och deras användningsområden. När företagsanvändare behöver data i sitt arbete (till exempel affärsanalyser, apputveckling, datavetenskap eller andra uppgifter där rätt data krävs) kan de söka i Data Catalog och snabbt identifiera data som motsvarar deras behov, förstå datakällan för att utvärdera dess lämplighet och använda data genom att öppna datakällan i önskat verktyg. Data Catalog gör det också möjligt för användarna att bidra till katalogen genom att tagga, dokumentera och kommentera datakällor som redan har registrerats och genom att registrera nya datakällor som sedan kan identifieras, förstås och användas av alla kataloganvändare.

![Funktioner i Data Catalog](./media/data-catalog-what-is-data-catalog/data-catalog-capabilities.png)

## Kom igång med Data Catalog

Kom igång med Data Catalog idag genom att besöka [www.azuredatacatalog.com](https://www.azuredatacatalog.com).

En kom igång-guide finns [här](data-catalog-get-started.md).

## Mer information om Data Catalog

Här finns mer information om funktionerna i Data Catalog:

* [Så här registrerar du datakällor](data-catalog-how-to-register.md)
* [Så här identifierar du datakällor](data-catalog-how-to-discover.md)
* [Så här kommenterar du datakällor](data-catalog-how-to-annotate.md)
* [Så här dokumenterar du datakällor](data-catalog-how-to-documentation.md)
* [Så här ansluter du till datakällor](data-catalog-how-to-connect.md)
* [Så här arbetar du med stordata](data-catalog-how-to-big-data.md)
* [Så här hanterar du datatillgångar](data-catalog-how-to-manage.md)
* [Så här konfigurerar du företagsordlistan](data-catalog-how-to-business-glossary.md)
* [Vanliga frågor och svar](data-catalog-frequently-asked-questions.md)



<!--HONumber=Sep16_HO3-->


