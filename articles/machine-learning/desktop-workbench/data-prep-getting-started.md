---
title: Komma igång med Data förberedelser för Azure Machine Learning | Microsoft Docs
description: Detta är Kom igång-guide för dataavsnittet prep AML arbetsstationen
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: 6bfa02bc098cdc8a9f37d238761dd44202e57ae6
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831225"
---
# <a name="getting-started-with-data-preparation"></a>Komma igång med förberedelse av data

Välkommen till den Data förberedelse komma igång-guiden. 

Förberedelse av data innehåller en uppsättning av verktyg för att utforska effektivt, förstå och lösa problem i data. Det kan du använda data i många formulär och transformera data till rengörs data som passar bättre för underordnade användning.

Förberedelse av data är installerad som en del av Azure Machine Learning-arbetsstationen.  Använd förberedelse av Data lokalt eller distribuera till ett målkluster och moln som en runtime eller köra miljö.

Design tid runtime använder Python utökningsbarhet och beror på olika Python-bibliotek, till exempel Pandas. Eftersom det inte finns behöver du inte installera Python med andra komponenter i Azure ML-arbetsstationen, installeras den åt dig. Men om du behöver installera ytterligare bibliotek dessa bibliotek måste vara installerat i Azure ML-arbetsstationen Python-katalog inte din vanliga Python-katalog. Mer information om hur du installerar paket finns [här](data-prep-python-extensibility-overview.md).

Ett projekt krävs innan du kan använda Data Prep när projektet har skapats kan du förbereda data. 

Gå till avsnittet Data i projektet genom att välja ikonen Data ![data ikonen](media/data-prep-getting-started/data-source-icon.png) till vänster på skärmen.  Klicka på ”+” igen till **lägga till en datakälla**. Guiden datakälla ska starta och lägger till en **datakällan** (.dsource)-filen i projektet när guiden har slutförts. Som standard är data visas i rutnätet. Ovanför rutnätet är det också möjligt att välja mått vyn. Sammanfattande statistik som visas i vyn mått.  När du har granskat sammanfattande statistik, klickar du på **Förbered** överst på skärmen. [Mer information om guiden datakälla](data-source-wizard.md) 

## <a name="building-blocks-of-data-preparation"></a>Byggblock för förberedelse av data ##
### <a name="the-package"></a>Paketet ###

Ett paket är primär behållare för ditt arbete. Ett paket är artefakten som sparas till och läses in från disken. När du arbetar i klienten är paketet ständigt sparad automatiskt i bakgrunden. 

Resultatet av ett paket kan undersökas i Python eller via en Jupyter-anteckningsbok.

Ett paket kan köras i flera körningar inklusive lokala Python, Spark (inklusive i Docker) och HDInsight.

Ett paket som innehåller en eller flera Dataflows som stegen och transformeringar tillämpas på informationen.

Ett paket kan använda ett annat paket som en datakälla (kallas en referens dataflöde).

### <a name="the-dataflow"></a>I dataströmmen ###
En dataflöde har en källa och valfria omvandlar som är ordnade genom en serie steg och valfritt mål. Klicka på ett steg kör igen alla källor och transformeringar före och inklusive det valda steget.  Transformerade data genom att steg visas i rutnätet. Steg kan läggas till, flyttas och tas bort i en dataflöde igenom listan steg.

Listan steg på höger sida av klienten kan öppnas och stängas för att ge mer plats.

Flera Dataflows kan finnas i Användargränssnittet i taget, varje dataflöde representeras som en flik i Användargränssnittet.

### <a name="the-source"></a>Källan
En källa är där data hämtas från och formatet finns i. Ett paket med Data Prep datakällor alltid data från en annan Data Flow(Data Source). Det är den här referensen dataflöde som innehåller informationen. Varje källa har en annan användare för att det ska konfigureras. Källan ger en ”rektangulär” / tabular visning av data. Om datakällan har ursprungligen ”ojämna höger”, är strukturen normaliserat ska ”rektangulär”. [Bilaga 2 innehåller en aktuell lista över källor som stöds](data-prep-appendix2-supported-data-sources.md).

### <a name="the-transform"></a>Transformeringen ###
Transformeringar använder data i ett visst format, utföra en åtgärd i data (till exempel ändra datatyp) och skapar sedan data i det nya formatet. Varje transformering har ett eget användargränssnitt och behavior(s). Länkning av flera transformeringar tillsammans via steg i dataströmmen är kärnan i funktioner för förberedelse av Data. [Tillägg 3 innehåller en aktuell lista över stöds transformeringar](data-prep-appendix3-supported-transforms.md).

### <a name="the-inspector"></a>Inspector ###

Kontrollanter är visualiseringar av data och är tillgängliga för att förbättra förståelse av data.  Förstå data och data utfärdar quality hjälper dig att bestämma vilka åtgärder (transformeringar) måste åtgärdas. Vissa kontrollanter stöd för åtgärder som genererar transformeringar. Exempelvis kan antal värden Inspector du välja ett värde och sedan använda ett filter ska inkluderas värdet eller för att utesluta värdet. Kontrollanter kan också ge kontext för transformeringar. Till exempel ändras välja en eller flera kolumner möjliga transformeringar som kan tillämpas.

En kolumn kan ha flera kontrollanter när som helst i tid (till exempel Kolumnstatistik och ett Histogram). Det kan också finnas instanser av kontrollant i flera kolumner. Alla numeriska kolumner kan till exempel ha histogram på samma gång.

Kontrollanter visas i den profilering väl längst ned på skärmen.  Maximera kontrollanter för att se dem större inom området huvudsakliga innehåll. Tänk på rutnätet som standard inspector. Alla Inspector kan expanderas till området huvudsakliga innehåll. Kontrollanter i det huvudsakliga innehållsområdet minimeras till den profilering bra. Anpassa kontrollant genom att klicka på pennikonen i Inspector. Ordna om kontrollanter i och med dra och släpp.

Vissa kontrollanter stöder en ”Halo”-läge. Det här läget visar värdet eller tillstånd innan den sista transformeringen tillämpades. Gammalt värde visas i grått med det aktuella värdet i förgrunden och visar effekten av en transformering. [Tillägg 4 innehåller en aktuell lista över stöds kontrollanter](data-prep-appendix4-supported-inspectors.md).

### <a name="the-destination"></a>Målet
 Ett mål är där du skrivning och exportera data till när du har förberett i en dataflöde. En viss dataflöde kan ha flera mål. Varje målet har en annan användarupplevelse så att den kan konfigureras. Målet använder data i formatet ”rektangulär” / tabular och skriver den till en plats i ett visst format. [Tillägg 5 innehåller en aktuell lista över stöds mål](data-prep-appendix5-supported-destinations.md).

### <a name="using-data-preparation"></a>Med hjälp av förberedelse av data ###
Förberedelse av data förutsätter en grundläggande fem steg metoder/metod för förberedelse av data.

#### <a name="step-1-ingestion"></a>Steg 1: införandet ####
Importera data för förberedelse av Data med hjälp av den **Lägg till datakälla** alternativet i projektvyn.  Alla inledande införandet av data hanteras via guiden datakälla.

#### <a name="step-2-understandprofile-the-data"></a>Steg 2: Förstå/profil data ####

Titta först på kvalitet datastapel längst upp i varje kolumn. Grönt representerar de rader som innehåller värden. Grå representerar rader med en saknad null-värde, osv. Rött anger felaktiga värden. Hovra över fältet att hämta en knappbeskrivning med det exakta antalet rader i var och en av tre buckets. Kvalitet datastapel använder en logaritmisk skala så alltid kontrollera faktiska talen som ska få en grov bild av mängden data som saknas.

![Kolumner](media/data-prep-getting-started/columns.png)

Använd sedan en kombination av andra kontrollanter plus rutnätet för att bättre förstå dataegenskaper.  Starta utforma hypoteser om förberedelse för data som krävs för ytterligare analys. De flesta kontrollanter fungerar på en kolumn eller ett litet antal kolumner.  

Det är troligt att flera kontrollanter över flera kolumner behövs för att förstå informationen. Du kan bläddra igenom olika kontrollanter i den profilering bra. Inom korrekt, kan du också flytta kontrollanter i huvudet i listan för att kunna se dem i omedelbart visningsområdet.

![kontrollanter](media/data-prep-getting-started/inspectors.PNG)

Olika kontrollanter tillhandahålls för kontinuerlig vs kategoriska variabler/kolumner. Menyn Inspector aktiverar och inaktiverar alternativ beroende på vilken typ av variabler eller kolumner som du har.

När du arbetar med bred datauppsättningar som har många kolumner, rekommenderas en pragmatic metod för att arbeta med delmängder. Den här metoden innehåller fokusera på ett litet antal kolumner (till exempel 5-10), förbereder dem och sedan gå igenom övriga kolumner. Rutnät inspector stöder vertikal partitionering av kolumner, så om du har mer än 300 kolumner måste du ”sidan” överstruket.
 

#### <a name="step-3-transform-the-data"></a>Steg 3: Transformera data ####
Transformeringar ändra data och fjärrkörning av data till stöd för den aktuella fungerande hypotesen. Transformeringar visas som stegen i listan över steg på höger sida. Det är möjligt att ”tid resa” igenom listan steg genom att klicka på valfri valfri punkt i listan över steg.

En grön ikon till vänster om ett visst steg visar att det har körts och data är körningen av transformeringen. Ett lodrätt streck till vänster om steget anger det aktuella tillståndet för data i kontrollanterna.

![steg](media/data-prep-getting-started/steps.PNG)

Försök att göra små frekventa ändringar av data och om du vill verifiera (steg 4) efter varje ändring hypotesen utvecklas.

#### <a name="step-4-verify-the-impact-of-the-transformation"></a>Steg 4: Verifiera effekten av transformeringen. 
Bestäm om hypotesen är korrekt. Om det är korrekt, utveckla nästa hypotesen och upprepa steg 2 – 3 för den nya. Om felaktigt, sedan Ångra senaste omvandling och utveckla nya hypotesen och upprepa steg 2 – 3.

Det vanligaste sättet att avgöra om transformationen har rätt effekten är att använda kontrollanterna. Använd befintlig. Använd kontrollanter med Halo effekt aktiverad eller starta flera kontrollanter om du vill visa data på poäng i tid.

![Halo inspector](media/data-prep-getting-started/halo1.PNG) ![Halo inspector](media/data-prep-getting-started/halo2.PNG)

Om du vill ångra en omvandling gå steg listan till höger i Användargränssnittet. (Panelen steg listan kan behöva popup ut igen. Klicka för att öppna den tecknet pekar vänstra). Välj den transformering som kördes som du vill ångra panelen. Välj i listrutan på höger sida av UI-block. Välj antingen **redigera** att göra ändringar eller **ta bort** bort transformeringen från listan över steg och i dataströmmen.

#### <a name="step-5-output"></a>Steg 5: utdata 
När du är klar med din förberedelse av data kan du skriva i dataströmmen till utdata. En dataflöde kan ha många utdata. Du kan välja som utdata du vill datamängden som ska registreras på menyn transformeringar. Du kan också välja den utgående mål. 

## <a name="list-of-appendices"></a>Lista över tillägg 
[Bilaga 2 - datakällor som stöds](data-prep-appendix2-supported-data-sources.md)  
[Transformerar tillägg 3 - stöd](data-prep-appendix3-supported-transforms.md)  
[Bilaga 4 - kontrollanter som stöds](data-prep-appendix4-supported-inspectors.md)  
[Bilaga 5 - stöd för mål](data-prep-appendix5-supported-destinations.md)  
[Bilaga 6 - exempel filteruttryck i Python](data-prep-appendix6-sample-filter-expressions-python.md)  
[Bilaga 7 - exempel transformera dataflöde uttryck i Python](data-prep-appendix7-sample-transform-data-flow-python.md)  
[Bilaga 8 - exempel datakällor i Python](data-prep-appendix8-sample-source-connections-python.md)  
[Bilaga 9 - exempel mål-anslutningar i Python](data-prep-appendix9-sample-destination-connections-python.md)  
[Bilaga 10 - exempel kolumnen omvandlar i Python](data-prep-appendix10-sample-custom-column-transforms-python.md)  

## <a name="see-also"></a>Se även

[Avancerade förberedelse av kursen](tutorial-bikeshare-dataprep.md)