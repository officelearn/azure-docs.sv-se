---
title: Komma igång med förberedelse av Data för Azure Machine Learning | Microsoft Docs
description: Det här är att komma igång-guide för dataavsnittet prep AML-arbetsstationen
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ROBOTS: NOINDEX
ms.openlocfilehash: a3f50e8fbaa396d9ca5a0d14fe9f54c30bc24f41
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46996289"
---
# <a name="getting-started-with-data-preparation"></a>Komma igång med förberedelse av data

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 



Välkommen till den komma igång guiden för databearbetning. 

Förberedelse av data innehåller en uppsättning verktyg för att utforska effektivt, förstå och lösa problem i data. Det kan du använda data i många formulär och omvandlar data till rensade data som passar bättre för underordnade användning.

Förberedelse av data har installerats som en del av Azure Machine Learning Workbench-upplevelse.  Använda förberedelse av Data lokalt eller distribuera till ett målkluster och i molnet som en körning eller körning-miljö.

Design tid körningen använder Python för utökningsbarhet och beror på olika Python-biblioteken, till exempel Pandas. Eftersom det finns ingen anledning att installera Python med andra komponenter i Azure ML Workbench, installeras den åt dig. Men om du vill installera ytterligare bibliotek är dessa bibliotek måste vara installerat i katalogen Azure ML Workbench Python inte din vanliga Python-katalog. Mer information om hur du installerar paket finns [här](data-prep-python-extensibility-overview.md).

Ett projekt krävs innan du kan använda förberedelse och när projektet har skapats kan du förbereda data. 

Gå till avsnittet Data i projektet genom att välja ikonen Data ![data källan ikonen](media/data-prep-getting-started/data-source-icon.png) till vänster på skärmen.  Klicka på ”+” igen till **lägga till en datakälla**. Guiden datakälla ska starta och lägger till en **datakälla** (.dsource)-filen i projektet när guiden har slutförts. Som standard är data visas i rutnätet. Ovanför rutnätet är det också möjligt att välja måttvyn. I måttvyn visas sammanfattande statistik.  När du har granskat sammanfattande statistik, klickar du på **Förbered** överst på skärmen. [Mer information om guiden datakälla](data-source-wizard.md) 

## <a name="building-blocks-of-data-preparation"></a>Byggstenarna för förberedelse av data ##
### <a name="the-package"></a>Paketet ###

Ett paket är den primära behållaren för ditt arbete. Ett paket är den artefakt som sparats och lästs in från disken. När du arbetar i klienten är paketet ständigt sparad automatiskt i bakgrunden. 

Resultatet av ett paket kan utforskas i Python eller via en Jupyter-anteckningsbok.

Ett paket kan köras i flera körningar inklusive lokal Python, Spark (även i Docker) och HDInsight.

Ett paket innehåller en eller flera dataflöden som är stegen och transformeringar som tillämpas på data.

Ett paket kan använda ett annat paket som en datakälla (kallas en referens dataflöde).

### <a name="the-dataflow"></a>Dataflödet ###
Ett dataflöde har en källa och valfritt transformeringar som är ordnade genom en serie steg och valfritt mål. När du klickar på ett steg kör igen alla källor och transformeringar före till och med det valda steget.  Transformerade data via det steget visas i rutnätet. Stegen kan har lagts till, flyttas och togs bort inom en dataflöde i steg-listan.

Listan över steg på höger sida av klienten kan öppnas och stängas för att ge mer skärmutrymmet.

Flera dataflöden kan finnas i Användargränssnittet i taget, varje dataflöde representeras som en flik i Användargränssnittet.

### <a name="the-source"></a>Källan
En källa är där data kommer från, och formatet som den tillhör. Ett paket för Data Prep datakällor alltid dess data från en annan Data Flow(Data Source). Det är den här referensen dataflöde som innehåller information. Varje källa har en annan användare erfarenhet av att den kan konfigureras. Källan ger en ”rektangulär” / tabular visning av data. Om datakällan har ursprungligen ”ojämna höger”, är strukturen normaliserade för att vara ”rektangulär”. [Bilaga 2 innehåller den aktuella listan med källor som stöds](data-prep-appendix2-supported-data-sources.md).

### <a name="the-transform"></a>Transformering ###
Transformeringar använda data i ett visst format, utföra åtgärder på data (till exempel ändra datatypen) och producerar data i det nya formatet. Varje transformering har ett eget användargränssnitt och behavior(s). Länkning av flera transformeringar tillsammans via steg i dataströmmen är kärnan i för förberedelse av Data. [Tillägg 3 innehåller den aktuella listan över transformeringar som stöds](data-prep-appendix3-supported-transforms.md).

### <a name="the-inspector"></a>Inspector ###

Inspectors är visualiseringar av data och är tillgängliga för att förbättra förståelse av dina data.  Förstå data och data utfärdar quality hjälper dig att bestämma vilka åtgärder (transformeringar) som ska vidtas. Vissa kontroller stöd för åtgärder som genererar transformeringar. Till exempel kan antal värden Inspector du välja ett värde och sedan använda ett filter att inkludera detta värde eller också utesluta detta värde. Kontroller kan också ge kontext för transformeringar. Till exempel ändras att välja en eller flera kolumner möjliga transformeringar som kan tillämpas.

En kolumn kan ha flera kontroller när som helst i tid (till exempel Kolumnstatistik och ett Histogram). Det kan också finnas instanser av en Inspector i flera kolumner. Alla numeriska kolumner kan till exempel ha histogram på samma gång.

Inspectors visas i den profilering bra längst ned på skärmen.  Maximera kontroller för att se dem större inom huvudinnehållet. Tänk på datarutnätet som standard-inspector. Alla Inspector kan expanderas till huvudinnehållet. Kontroller inom huvudinnehållet minimeras till den profilering bra. Anpassa en Inspector genom att klicka på pennikonen i Inspector. Ordna om kontroller inom det bra med dra och släpp.

Vissa kontroller stöd för ett ”Halo”-läge. Det här läget visar värdet eller tillstånd innan den sista transformeringen tillämpades. Det gamla värdet visas i grått med det aktuella värdet i förgrunden och visar effekten av en transformering. [Tillägg 4 innehåller den aktuella listan över kontroller som stöds](data-prep-appendix4-supported-inspectors.md).

### <a name="the-destination"></a>Målet
 Ett mål är där du skrivning/exportera data till när du har förberett den i en dataflöde. En viss dataflöde kan ha flera mål. Varje mål har en annan användare erfarenhet av att den kan konfigureras. Målet använder data i ett format som ”rektangulär” / tabular och skriver den på en plats i ett visst format. [Tillägg 5 innehåller den aktuella listan över stöds mål](data-prep-appendix5-supported-destinations.md).

### <a name="using-data-preparation"></a>Med förberedelse av data ###
Förberedelse av data förutsätter en grundläggande fem steg metod/metod för förberedelse av data.

#### <a name="step-1-ingestion"></a>Steg 1: inmatning ####
Importera data för förberedelse av Data med hjälp av den **Lägg till datakälla** alternativet i projektvyn.  Alla inledande inmatning av data hanteras via guiden datakälla.

#### <a name="step-2-understandprofile-the-data"></a>Steg 2: Förstå/profil-data ####

Titta först på Datakvalitetsstapeln högst upp i varje kolumn. Grön representerar de rader som innehåller värden. Grå representerar rader med en saknad värde, null, osv. Rött anger felvärdena. Hovra över fältet för att hämta en knappbeskrivning med det exakta antalet rader i var och en av tre bucketarna. Datakvalitetsstapeln används en logaritmisk skala så du måste alltid kontrollera faktiska siffrorna för att få en ungefärlig bild mängden data som saknas.

![Kolumner](media/data-prep-getting-started/columns.png)

Använd sedan en kombination av andra Inspectors plus rutnätet för att bättre förstå dataegenskaper.  Börja utforma hypoteser om den förberedelse av data som krävs för vidare analys. De flesta inspectors fungerar på en enda kolumn eller ett litet antal kolumner.  

Det är troligt att flera kontroller över flera kolumner som krävs för att förstå data. Du kan bläddra igenom olika kontroller i den profilering bra. I brunnen, kan du också flytta Inspectors till toppen av listan för att kunna se dem i området direkt kan visas.

![kontroller](media/data-prep-getting-started/inspectors.PNG)

Olika Inspectors tillhandahålls för kontinuerlig eller kategorisk variabler/kolumner. Menyn Inspector aktiverar och inaktiverar alternativ beroende på vilken typ av variabler/kolumner som du har.

När du arbetar med många datauppsättningar som har många kolumner, rekommenderas en pragmatisk metod för att arbeta med delmängder. Den här metoden innehåller fokuserar på ett litet antal kolumner (till exempel 5-10), förbereder dem och sedan gå igenom övriga kolumner. Rutnätet inspector stöder vertikal partitionering av kolumner och det om du har fler än 300 kolumner måste du ”page” igenom dem för.
 

#### <a name="step-3-transform-the-data"></a>Steg 3: Omvandla data ####
Transformeringar ändra data och att körningen av data för den aktuella fungerande hypotesen. Transformeringar visas som stegen i steg listan till höger. Det är möjligt att ”tid resor” i listan med steg genom att klicka på en godtycklig tidpunkt i listan över steg.

En grön ikon till vänster om ett visst steg anger att den har körts och data återspeglar körningen av transformeringen. Det visar det aktuella tillståndet för data i kontrollerna i en lodrät stapel till vänster om steget.

![steg](media/data-prep-getting-started/steps.PNG)

Försök att göra små frekventa ändringar av data och om du vill verifiera (steg 4) efter varje ändring hypotesen utvecklas.

#### <a name="step-4-verify-the-impact-of-the-transformation"></a>Steg 4: Kontrollera effekten av transformeringen. 
Bestäm om hypotesen var korrekt. Om det är rätt, utveckla nästa hypotesen och upprepa steg 2 – 3 för den nya. Om det är felaktigt, sedan Ångra senaste omvandlingen och utveckla en ny hypotes och upprepa steg 2 – 3.

Det vanligaste sättet att avgöra om vi hade rätt effekten är att använda kontrollerna. Använd befintlig. Använd Inspectors med Halo effekt aktiverad eller starta flera kontroller för att visa data på poäng i tid.

![Halo inspector](media/data-prep-getting-started/halo1.PNG) ![Halo inspector](media/data-prep-getting-started/halo2.PNG)

Om du vill ångra en omvandling går du steg listan till höger i Användargränssnittet. (Steg listpanel behöva vara visats igen. För att öppna den, klickar du på tecknet som pekar vänster). I panelen, väljer du den transformering som kördes som du vill ångra. Välj i listrutan på höger sida av UI-block. Välj antingen **redigera** göra ändringar eller **ta bort** att ta bort transformeringen från listan med steg och dataflödet.

#### <a name="step-5-output"></a>Steg 5: utdata 
När du är klar med din förberedelse av data kan du skriva dataflödet till utdata. Ett dataflöde kan ha många utdata. Du kan välja vilket utdata du vill att datauppsättningen som ska registreras transformeringar-menyn. Du kan också välja den utdata mål. 

## <a name="list-of-appendices"></a>Lista över tillägg 
[Bilaga 2 - datakällor som stöds](data-prep-appendix2-supported-data-sources.md)  
[Tillägg 3 - stöds omvandlar](data-prep-appendix3-supported-transforms.md)  
[Tillägg 4 - kontroller som stöds](data-prep-appendix4-supported-inspectors.md)  
[Tillägg 5 – stöd för mål](data-prep-appendix5-supported-destinations.md)  
[Bilaga 6 – exempel filteruttryck i Python](data-prep-appendix6-sample-filter-expressions-python.md)  
[Bilaga 7 – exempel transformera dataflöde uttryck i Python](data-prep-appendix7-sample-transform-data-flow-python.md)  
[Bilaga 8 – exempel datakällor i Python](data-prep-appendix8-sample-source-connections-python.md)  
[Bilaga 9 – exempel målanslutningar i Python](data-prep-appendix9-sample-destination-connections-python.md)  
[Bilaga 10 – exempel kolumnen transformeringar i Python](data-prep-appendix10-sample-custom-column-transforms-python.md)  

## <a name="see-also"></a>Se även

[Avancerad dataförberedelse av självstudier](tutorial-bikeshare-dataprep.md)