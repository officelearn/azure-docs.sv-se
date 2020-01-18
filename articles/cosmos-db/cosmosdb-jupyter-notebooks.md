---
title: Introduktion till det inbyggda stödet för Jupyter Notebooks i Azure Cosmos DB (för hands version)
description: Lär dig hur du kan använda det inbyggda stödet för Jupyter Notebooks i Azure Cosmos DB för att interaktivt köra frågor.
ms.service: cosmos-db
ms.topic: overview
ms.date: 09/22/2019
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: 5c6d7ae458c30d462c599042e254f4243daf474c
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2020
ms.locfileid: "76260879"
---
# <a name="built-in-jupyter-notebooks-support-in-azure-cosmos-db"></a>Inbyggt stöd för Jupyter Notebooks i Azure Cosmos DB

Jupyter Notebook är ett webb program med öppen källkod som gör att du kan skapa och dela dokument som innehåller Live-kod, ekvationer, visualiseringar och text. Azure Cosmos DB stöder inbyggda Jupyter-anteckningsböcker för alla API: er som Cassandra, MongoDB, SQL, Gremlin och Table. Med det inbyggda Notebook-stödet för alla Azure Cosmos DB-API: er och data modeller kan du interaktivt köra frågor. Jupyter-anteckningsbokarna körs i Azure Cosmos-kontona och de gör det möjligt för utvecklare att utföra data utforskning, data rensning, data transformationer, numeriska simuleringar, statistisk modellering, data visualisering och maskin inlärning.

![Jupyter Notebooks-visualiseringar i Azure Cosmos DB](./media/cosmosdb-jupyter-notebooks/cosmos-notebooks-overview.png)

Antecknings böckerna för Jupyter har stöd för Magic Functions som utökar funktionerna i kärnan genom att stödja ytterligare kommandon. Cosmos Magic är ett kommando som utökar funktionerna i python-kärnan i Jupyter Notebook så att du kan köra Azure Cosmos SQL API-frågor förutom Apache Spark. Du kan enkelt kombinera python-och SQL API-frågor för att fråga och visualisera data med hjälp av omfattande visualiserings bibliotek som är integrerade med Render-kommandon.
Azure Portal integrerar Jupyter Notebook-upplevelse i Azure Cosmos-konton på det sätt som visas i följande bild:

![Stöd för Jupyter Notebooks i Azure Cosmos DB](./media/cosmosdb-jupyter-notebooks/jupyter-notebooks-portal.png)

## <a name="benefits-of-jupyter-notebooks"></a>Fördelar med Jupyter-anteckningsböcker

Jupyter-anteckningsböcker utvecklades ursprungligen för data vetenskaps program skrivna i python, R. De kan dock användas på olika sätt för olika typer av projekt, till exempel:

* ***data visualiseringar:** med Jupyter-anteckningsböcker kan du visualisera data i form av en delad antecknings bok som återger viss data uppsättning som en bild. Med Jupyter Notebook kan du redigera visualiseringar, dela dem och tillåta interaktiva ändringar i den delade koden och data uppsättningen.

* **Kod delning:** Tjänster som GitHub ger olika sätt att dela kod, men de är mycket icke-interaktiva. Med en Jupyter Notebook kan du Visa kod, köra den och visa resultaten direkt i Azure Portal.

* **Live-interaktioner med kod:** Jupyter Notebook Code är dynamisk. den kan redige ras och köras stegvis i real tid. Antecknings böcker kan också bädda in användar kontroller (t. ex. skjutreglage eller text inmatnings fält) som kan användas som inmatnings källor för kod, demonstrationer eller proof of Concepts (POC).

* **Dokumentation av kod exempel och resultat för data utforskning:** Om du har en kod i koden och du vill förklara rad för rad hur det fungerar i Azure Cosmos DB, med real tids utdata på vägen, kan du bädda in det i en Jupyter Notebook. Koden fortsätter att fungera helt och hållet. Du kan lägga till interaktivitet tillsammans med dokumentationen på samma tid.

* **Cosmos Magic-kommandon:** I Jupyter Notebooks kan du använda anpassade Magic-kommandon för att Azure Cosmos DB för att förenkla interaktiva data behandling. Till exempel%% SQL Magic som tillåter att en Cosmos-behållare använder SQL API direkt i en bärbar dator.

* **Alla i en plats miljö:** Jupyter-anteckningsböcker kombinerar kod, RTF, bilder, videor, animeringar, matematiska ekvationer, ritytor, kartor, interaktiva figurer, widgetar och grafiska användar gränssnitt till ett enda dokument.

## <a name="components-of-a-jupyter-notebook"></a>Komponenter i en Jupyter Notebook

Jupyter-anteckningsböcker kan innehålla flera typer av komponenter, som var och en är ordnade i separata block:

* **Text och HTML:** Oformaterad text eller text som är kommenterad i markdown-syntaxen för att generera HTML, kan infogas i dokumentet när som helst. CSS-format kan också inkluderas infogade eller läggas till i mallen som används för att skapa antecknings boken.

* **Kod och utdata:** Jupyter Notebooks stöder python-kod. Resultatet av den körda koden visas omedelbart efter kodblock och kodblock kan köras flera gånger i vilken ordning som helst.

* **Visualiseringar:** Grafik och diagram kan genereras från koden med hjälp av moduler som matplotlib, ritas upp eller bokeh. På samma sätt som utdata visas dessa visualiseringar bredvid den kod som genererar dem.

* **Multimedia:** Eftersom Jupyter Notebook är byggd på webb tekniken kan den Visa alla typer av multimedia som stöds på en webb sida. Du kan inkludera dem i en bärbar dator som HTML-element, eller så kan du generera dem program mässigt genom att använda `IPython.display`-modulen.

* **Data:** Data från Azure Cosmos-behållare och resultat från frågorna kan importeras till en Jupyter-anteckningsbok program mässigt. Om du till exempel inkluderar kod i antecknings boken för att fråga data med hjälp av någon av de Cosmos DB API: erna eller internt inbyggda Apache Spark.

## <a name="next-steps"></a>Nästa steg

Om du vill komma igång med inbyggda Jupyter-anteckningsböcker i Azure Cosmos DB kan du läsa följande artiklar:

* [Aktivera antecknings böcker i ett Azure Cosmos-konto](enable-notebooks.md)
* [Använda Notebook-funktioner och-kommandon](use-notebook-features-and-commands.md)



