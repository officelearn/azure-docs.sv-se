---
title: "Azure Cosmos DB-självstudie: Skapa, ställ frågor och bläddra i Gremlin-konsolen | Microsoft Docs"
description: "En Azure Cosmos DB-snabbstart för att skapa hörn, gränser och frågor med Azure Cosmos DB Graph API."
services: cosmosdb
author: AndrewHoh
manager: jhubbard
editor: monicar
ms.assetid: bf08e031-718a-4a2a-89d6-91e12ff8797d
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: terminal
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: anhoh
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: fb27ba1a70959ba92fbd021e9e42438081000e45
ms.contentlocale: sv-se
ms.lasthandoff: 05/10/2017


---
# <a name="azure-cosmos-db-create-query-and-traverse-a-graph-in-the-gremlin-console"></a>Azure Cosmos DB: Skapa, ställ frågor och bläddra i grafen i Gremlin-konsolen

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Azure Cosmos DB. 

Den här snabbstarten demonstrerar hur du skapar ett Azure Cosmos DB-konto, en databas och en graf (behållare) med hjälp av Azure Portal och därefter använder [Gremlin-konsolen](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) för att arbeta med Graph API-data (förhandsversion). I den här självstudien får du skapa och fråga hörn och gränser, uppdatera en hörnegenskap, fråga kanter, bläddra i grafen och släppa ett hörn.

![Azure Cosmos DB från konsolen Apache Gremlin](./media/create-graph-gremlin-console/gremlin-console.png)

Gremlin-konsolen är Groovy-/Java-baserad och körs på Linux, Mac och Windows. Du kan ladda ned den på [Apache TinkerPop-webbplatsen](https://www.apache.org/dyn/closer.lua/tinkerpop/3.2.4/apache-tinkerpop-gremlin-console-3.2.4-bin.zip).

## <a name="prerequisites"></a>Krav

Du måste ha en Azure-prenumeration för att skapa ett Azure Cosmos DB-konto för den här snabbstarten.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Du måste också installera [Gremlin-konsolen](http://tinkerpop.apache.org/). Använd version 3.2.4 eller senare.

## <a name="create-a-database-account"></a>Skapa ett databaskonto

[!INCLUDE [cosmosdb-create-dbaccount-graph](../../includes/cosmosdb-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Lägga till en graf

[!INCLUDE [cosmosdb-create-graph](../../includes/cosmosdb-create-graph.md)]

## <a id="ConnectAppService"></a>Ansluta till din apptjänst
1. Innan du skapar Gremlin-konsolen ska du skapa eller ändra din *remote-secure.yaml*-konfigurationsfil i katalogen *apache-tinkerpop-gremlin-console-3.2.4/conf*.
2. Fyll i din *värd*, *port*, *användarnamn*, *lösenord*, *connectionPool* och *serialiserarens* konfigurationer:

    Inställning|Föreslaget värde|Beskrivning
    ---|---|---
    Värdar|***.graphs.azure.com|Din URI till Graph-tjänsten som du kan hämta från Azure Portal
    Port|443|Ställ in på 443
    Användarnamn|*Ditt användarnamn*|Resursen i formuläret `/dbs/<db>/colls/<coll>`.
    Lösenord|*Din primära huvudnyckel*|Din primära huvudnyckel för Azure Cosmos DB
    ConnectionPool|{enableSsl: true}|Din anslutningspoolinställning för SSL
    Serialiserare|{ className:org.apache.tinkerpop.gremlin.<br>driver.ser.GraphSONMessageSerializerV1d0,<br> config: { serializeResultToString: true }}|Ange detta värde

3. I din terminal kör du *bin/gremlin.bat* eller *bin/gremlin.sh* för att starta [Gremlin-konsolen](http://tinkerpop.apache.org/docs/3.2.4/tutorials/getting-started/).
4. I din terminal kör du *:remote connect tinkerpop.server conf/remote-secure.yaml* för att ansluta till din apptjänst.

Bra! Konfigurationen är slutförd, så vi kan börja köra några konsolkommandon.

## <a name="create-vertices-and-edges"></a>Skapa hörn och gränser

Vi börjar med att lägga till fyra personhörn för *Thomas*, *Mary Kay*, *Robin* och *Ben*.

Indata (Thomas):

```
:> g.addV('person').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

Resultat:

```
==>[id:1eb91f79-94d7-4fd4-b026-18f707952f21,label:person,type:vertex,properties:[firstName:[[id:ec5fcfbe-040e-48c3-b961-31233c8b1801,value:Thomas]],lastName:[[id:86e5b580-0bca-4bc2-bc53-a46f92c1a182,value:Andersen]],age:[[id:2caeab3c-c66d-4098-b673-40a8101bb72a,value:44]]]]
```
Indata (Mary Kay):

```
:> g.addV('person').property('firstName', 'Mary Kay').property('lastName', 'Andersen').property('age', 39)
```

Resultat:

```
==>[id:899a9d37-6701-48fc-b0a1-90950be7e0f4,label:person,type:vertex,properties:[firstName:[[id:c79c5599-8646-47d1-9a49-3456200518ce,value:Mary Kay]],lastName:[[id:c1362095-9dcc-479d-ab21-86c1b6d4ffc1,value:Andersen]],age:[[id:0b530408-bfae-4e8f-98ad-c160cd6e6a8f,value:39]]]]
```

Indata (Robin):

```
:> g.addV('person').property('firstName', 'Robin').property('lastName', 'Wakefield')
```

Resultat:

```
==>[id:953aefd9-5a54-4033-9b3a-d4dc3049f720,label:person,type:vertex,properties:[firstName:[[id:bbda02e0-8a96-4ca1-943e-621acbb26824,value:Robin]],lastName:[[id:f0291ad3-05a3-40ec-aabb-6538a7c331e3,value:Wakefield]]]]
```

Indata (Ben):

```
:> g.addV('person').property('firstName', 'Ben').property('lastName', 'Miller')
```

Resultat:

```
==>[id:81c891d9-beca-4c87-9009-13a826c9ed9a,label:person,type:vertex,properties:[firstName:[[id:3a3b53d3-888c-46da-bb54-1c42194b1e18,value:Ben]],lastName:[[id:48c6dd50-79c4-4585-ab71-3bf998061958,value:Miller]]]]
```

Nu ska vi lägga till gränser för personernas relationer.

Indata (Thomas -> Mary Kay):

```
:> g.V().hasLabel('person').has('firstName', 'Thomas').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Mary Kay'))
```

Resultat:

```
==>[id:c12bf9fb-96a1-4cb7-a3f8-431e196e702f,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:0d1fa428-780c-49a5-bd3a-a68d96391d5c,outV:1ce821c6-aa3d-4170-a0b7-d14d2a4d18c3]
```

Indata (Thomas -> Robin):

```
:> g.V().hasLabel('person').has('firstName', 'Thomas').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Robin'))
```

Resultat:

```
==>[id:58319bdd-1d3e-4f17-a106-0ddf18719d15,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:3e324073-ccfc-4ae1-8675-d450858ca116,outV:1ce821c6-aa3d-4170-a0b7-d14d2a4d18c3]
```

Indata (Robin -> Ben):

```
:> g.V().hasLabel('person').has('firstName', 'Robin').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Ben'))
```

Resultat:

```
==>[id:889c4d3c-549e-4d35-bc21-a3d1bfa11e00,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:40fd641d-546e-412a-abcc-58fe53891aab,outV:3e324073-ccfc-4ae1-8675-d450858ca116]
```

## <a name="update-a-vertex"></a>Uppdatera ett hörn

Nu ska vi uppdatera *Thomas* hörn med den nya åldern *45*.

Indata:
```
:> g.V().hasLabel('person').has('firstName', 'Thomas').property('age', 45)
```
Resultat:

```
==>[id:ae36f938-210e-445a-92df-519f2b64c8ec,label:person,type:vertex,properties:[firstName:[[id:872090b6-6a77-456a-9a55-a59141d4ebc2,value:Thomas]],lastName:[[id:7ee7a39a-a414-4127-89b4-870bc4ef99f3,value:Andersen]],age:[[id:a2a75d5a-ae70-4095-806d-a35abcbfe71d,value:45]]]]
```

## <a name="query-your-graph"></a>Fråga diagrammet

Nu ska vi köra en mängd olika frågor mot grafen.

Vi försöker först fråga med ett filter för att endast returnera personer som är äldre än 40 år.

Indata (filterfråga):

```
:> g.V().hasLabel('person').has('age', gt(40))
```

Resultat:

```
==>[id:ae36f938-210e-445a-92df-519f2b64c8ec,label:person,type:vertex,properties:[firstName:[[id:872090b6-6a77-456a-9a55-a59141d4ebc2,value:Thomas]],lastName:[[id:7ee7a39a-a414-4127-89b4-870bc4ef99f3,value:Andersen]],age:[[id:a2a75d5a-ae70-4095-806d-a35abcbfe71d,value:45]]]]
```

Sedan projicerar vi förnamnet på personerna som är över 40 år.

Indata (filter + projektionsfråga):

```
:> g.V().hasLabel('person').has('age', gt(40)).values('firstName')
```

Resultat:

```
==>Thomas
```

## <a name="traverse-your-graph"></a>Bläddra i grafen

Vi bläddrar i grafen för att returnera alla Thomas vänner.

Indata (Thomas vänner):

```
:> g.V().hasLabel('person').has('firstName', 'Thomas').outE('knows').inV().hasLabel('person')
```

Resultat: 

```
==>[id:f04bc00b-cb56-46c4-a3bb-a5870c42f7ff,label:person,type:vertex,properties:[firstName:[[id:14feedec-b070-444e-b544-62be15c7167c,value:Mary Kay]],lastName:[[id:107ab421-7208-45d4-b969-bbc54481992a,value:Andersen]],age:[[id:4b08d6e4-58f5-45df-8e69-6b790b692e0a,value:39]]]]
==>[id:91605c63-4988-4b60-9a30-5144719ae326,label:person,type:vertex,properties:[firstName:[[id:f760e0e6-652a-481a-92b0-1767d9bf372e,value:Robin]],lastName:[[id:352a4caa-bad6-47e3-a7dc-90ff342cf870,value:Wakefield]]]]
```

Nu ska vi hämta nästa lager med hörn. Bläddra i grafen för att returnera alla Thomas vänners vänner.

Indata (Thomas vänners vänner):

```
:> g.V().hasLabel('person').has('firstName', 'Thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```
Resultat:

```
==>[id:a801a0cb-ee85-44ee-a502-271685ef212e,label:person,type:vertex,properties:[firstName:[[id:b9489902-d29a-4673-8c09-c2b3fe7f8b94,value:Ben]],lastName:[[id:e084f933-9a4b-4dbc-8273-f0171265cf1d,value:Miller]]]]
```

## <a name="drop-a-vertex"></a>Släppa ett hörn

Vi ska nu ta bort ett hörn från Graph-databasen.

Indata (vi släpper Robins hörn):

```
:> g.V().hasLabel('person').has('firstName', 'Robin').drop()
```

## <a name="clear-your-graph"></a>Rensa diagrammet

Slutligen ska vi rensa databasen från alla hörn och gränser.

Indata:

```
:> g.V().drop()
```

Grattis! Du har slutfört den här självstudien om Azure Cosmos DB: Graph API!

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmosdb-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att fortsätta använda den här appen tar du bort alla resurser som skapades i snabbstarten i Azure Portal med följande steg:  

1. Klicka på **Resursgrupper** på den vänstra menyn i Azure Portal och sedan på namnet på den resurs du skapade. 
2. På sidan med resursgrupper klickar du på **Ta bort**, skriver in namnet på resursen att ta bort i textrutan och klickar sedan på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig att skapa ett Azure Cosmos DB-konto, skapa en graf med Datautforskaren, skapa hörn och gränser och bläddra i grafen med Gremlin-konsolen. Nu kan du skapa mer komplexa frågor och implementera kraftfull logik för grafbläddring med Gremlin. 

> [!div class="nextstepaction"]
> [Fråga med hjälp av Gremlin](tutorial-query-graph.md)

