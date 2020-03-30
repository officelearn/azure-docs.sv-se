---
title: Introduktion till det inbyggda jupyter-stöd för bärbara datorer i Azure Cosmos DB (Förhandsversion)
description: Lär dig hur du kan använda det inbyggda jupyter-stöd för anteckningsböcker i Azure Cosmos DB för att interaktivt köra frågor.
ms.service: cosmos-db
ms.topic: overview
ms.date: 09/22/2019
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: 5b320485001d6cbc457d39ef193ed8c57f7161df
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76760291"
---
# <a name="built-in-jupyter-notebooks-support-in-azure-cosmos-db-preview"></a>Stöd för inbyggda Jupyter-anteckningsböcker i Azure Cosmos DB (förhandsversion)

Jupyter notebook är en öppen källkod webbprogram som låter dig skapa och dela dokument som innehåller levande kod, ekvationer, visualiseringar och berättande text. Azure Cosmos DB stöder inbyggda Jupyter-anteckningsböcker för alla API:er som Cassandra, MongoDB, SQL, Gremlin och Table. Med det inbyggda stöd för bärbara datorer för alla Azure Cosmos DB-API:er och datamodeller kan du interaktivt köra frågor. Jupyter-anteckningsböckerna körs inom Azure Cosmos-kontona och de gör det möjligt för utvecklare att utföra datautforskning, datarensning, dataomvandlingar, numeriska simuleringar, statistisk modellering, datavisualisering och maskininlärning.

![Jupyter-anteckningsböcker visualiseringar i Azure Cosmos DB](./media/cosmosdb-jupyter-notebooks/cosmos-notebooks-overview.png)

Jupyter-anteckningsböckerna stöder magiska funktioner som utökar kärnans funktioner genom att stödja ytterligare kommandon. Cosmos magic är ett kommando som utökar funktionerna i Python-kärnan i Jupyter-anteckningsboken så att du kan köra Azure Cosmos SQL API-frågor utöver Apache Spark. Du kan enkelt kombinera Python- och SQL API-frågor för att fråga och visualisera data med hjälp av omfattande visualiseringsbibliotek integrerade med renderingskommandon.
Azure-portalen integrerar inbyggt jupyter-anteckningsboksupplevelse i Azure Cosmos-konton enligt följande avbildning:

![Jupyter-anteckningsböcker stöder i Azure Cosmos DB](./media/cosmosdb-jupyter-notebooks/jupyter-notebooks-portal.png)

## <a name="benefits-of-jupyter-notebooks"></a>Fördelar med Jupyter bärbara datorer

Jupyter bärbara datorer utvecklades ursprungligen för datavetenskap applikationer skrivna i Python, R. De kan dock användas på olika sätt för olika typer av projekt, till exempel:

* ***Datavisualiseringar:** Med Jupyter-anteckningsböcker kan du visualisera data i form av en delad anteckningsbok som återger vissa datauppsättningar som en bild. Med Den bärbara jupyter-anteckningsboken kan du skapa visualiseringar, dela dem och tillåta interaktiva ändringar i den delade koden och datauppsättningen.

* **Koddelning:** Tjänster som GitHub ger sätt att dela kod, men de är till stor del icke-interaktiva. Med en Jupyter-anteckningsbok kan du visa kod, köra den och visa resultaten direkt i Azure-portalen.

* **Live-interaktioner med kod:** Jupyter notebook code is dynamic; Den kan redigeras och köras stegvis i realtid. Anteckningsböcker kan också bädda in användarkontroller (t.ex. reglage eller textinmatningsfält) som kan användas som indatakällor för kod, demonstrationer eller Proof of Concepts (POCs).

* **Dokumentation av kodprover och utfall av datautforskning:** Om du har en kod och vill förklara rad för rad hur det fungerar i Azure Cosmos DB, med realtidsutdata hela vägen, kan du bädda in den i en Jupyter Notebook. Koden kommer att förbli fullt fungerande. Du kan lägga till interaktivitet tillsammans med dokumentationen samtidigt.

* **Cosmos magiska kommandon:** I Jupyter-anteckningsböcker kan du använda anpassade magiska kommandon för Azure Cosmos DB för att göra interaktiv databehandling enklare. Till exempel den %%sql magi som gör att man kan fråga en Cosmos-behållare med SQL API direkt i en anteckningsbok.

* **Allt på ett ställe miljö:** Jupyter anteckningsböcker kombinera kod, RTF, bilder, videor, animationer, matematiska ekvationer, tomter, kartor, interaktiva figurer, widgets och grafiska användargränssnitt i ett enda dokument.

## <a name="components-of-a-jupyter-notebook"></a>Komponenter i en Jupyter bärbar dator

Jupyter bärbara datorer kan innehålla flera typer av komponenter, var och en organiserad i diskreta block:

* **Text och HTML:** Oformaterad text, eller text som kommenterats i markdown-syntaxen för att generera HTML, kan infogas i dokumentet när som helst. CSS-styling kan också inkluderas infogad eller läggas till i mallen som används för att generera anteckningsboken.

* **Kod och utdata:** Jupyter-anteckningsböcker stöder Python-kod. Resultatet av den körda koden visas omedelbart efter kodblocken och kodblocken kan köras flera gånger i valfri ordning.

* **Visualiseringar:** Grafik och diagram kan genereras från koden med hjälp av moduler som Matplotlib, Plotly eller Bokeh. I likhet med utdata visas dessa visualiseringar infogade bredvid koden som genererar dem.

* **Multimedia:** Eftersom Jupyter notebook är byggd på webbteknik, kan det visa alla typer av multimedia som stöds i en webbsida. Du kan inkludera dem i en anteckningsbok som HTML-element, eller `IPython.display` så kan du generera dem programmässigt med hjälp av modulen.

* **Uppgifter:** Data från Azure Cosmos-behållare och resultat av frågorna kan importeras till en Jupyter-anteckningsbok programmässigt. Genom att till exempel inkludera kod i anteckningsboken för att fråga data med någon av Cosmos DB API:er eller inbyggt inbyggt Apache Spark.

## <a name="next-steps"></a>Nästa steg

Så här kommer du igång med inbyggda Jupyter-anteckningsböcker i Azure Cosmos DB och läser följande artiklar:

* [Aktivera anteckningsböcker i ett Azure Cosmos-konto](enable-notebooks.md)
* [Använda funktioner och kommandon för bärbara datorer](use-notebook-features-and-commands.md)



