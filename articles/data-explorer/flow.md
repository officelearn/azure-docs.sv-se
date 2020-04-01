---
title: Microsoft Azure Data Explorer Flow-anslutningsappen (förhandsversion)
description: Lär dig mer om hur du använder Microsoft Flow-kopplingen för att skapa flöden av automatiskt schemalagda eller utlösta aktiviteter.
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 198b78d5bab15057fdb6c7f6d4e8fff9f77d496e
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397103"
---
# <a name="microsoft-flow-connector-preview"></a>Microsoft Flow-anslutningsapp (förhandsgranskning)

Azure Data Explorer-flödesappen gör att Azure Data Explorer kan använda [Microsoft Power Automates flödesfunktioner](https://flow.microsoft.com/) för att köra Kusto-frågor och kommandon automatiskt som en del av en schemalagd eller utlöst aktivitet.

Vanliga användningsscenarier är:

* Skicka dagliga rapporter som innehåller tabeller och diagram
* Ange meddelanden baserat på frågeresultat
* Schemalägga kontrollkommandon i kluster
* Exportera och importera data mellan Azure Data Explorer och andra databaser 

Mer information finns i [exempel på användning av Microsoft Flow-anslutningsappar](flow-usage.md).

##  <a name="log-in"></a>Logga in 

1. Logga in på [Microsoft Power Automate](https://flow.microsoft.com/).

1. När du ansluter för första gången uppmanas du att logga in.

1. Välj **Logga in** och ange dina autentiseringsuppgifter.

![Dialogrutan Logga in](./media/flow/flow-signin.png)

## <a name="authentication"></a>Autentisering

Du kan autentisera med användaruppgifter eller ett AAD-program.

> [!Note]
> Kontrollera att ditt program är ett [AAD-program](https://docs.microsoft.com/azure/kusto/management/access-control/how-to-provision-aad-app) och har behörighet att köra frågor i klustret.

1. Markera de tre punkterna längst upp till höger ![i Microsoft Flow-kopplingen: Lägg till en anslutning](./media/flow/flow-addconnection.png)

1. Välj **Lägg till ny anslutning** och välj sedan Anslut med **tjänstens huvudnamn**.
![Dialogrutan Logga in](./media/flow/flow-signin.png)

1. Ange den information som krävs:
    * Anslutningsnamn: Ett beskrivande och meningsfullt namn för den nya anslutningen
    * Klient-ID: Ditt program-ID
    * Klienthemlighet: Din programnyckel
    * Klient: ID:t för AAD-katalogen där du skapade programmet. Microsofts klient-ID är till exempel: 72f988bf-86f1-41af-91ab-2d7cd011db47

![Autentisering av program](./media/flow/flow-appauth.png)

När autentiseringen är klar ser du att flödet använder den nyligen tillagda anslutningen.

![Slutförd programautentisering](./media/flow/flow-appauthcomplete.png)

Från och med nu kommer det här flödet att köras med hjälp av dessa programautentiseringsuppgifter.

## <a name="find-the-azure-kusto-connector"></a>Hitta Azure Kusto-anslutningen

Om du vill använda Microsoft Flow-kopplingen måste du först lägga till en utlösare. En utlösare kan definieras baserat på en återkommande tidsperiod eller som svar på en tidigare flödesåtgärd.

1. [Skapa ett nytt flöde](https://flow.microsoft.com/manage/flows/new) eller välj åtgärden **Mina flöden** på startsidan och välj sedan **+ Nytt**.

    ![Skapa ett nytt flöde](./media/flow/flow-newflow.png)

1. Lägg till schemalagd från tom.

    ![Nytt schemalagt flöde](./media/flow/flow-scheduled-from-blank.png)

1. Ange nödvändig information på sidan Skapa ett schemalagt flöde.
    ![Skapa ett schemalagt flöde](./media/flow/flow-build-scheduled-flow.png)
1. Välj **Skapa**.
1. Välj **+ Nytt steg**.
1. Skriv "Kusto" i sökrutan.

    ![Välj flödesåtgärder](./media/flow/flow-actions.png)

1. Välj **Azure Data Explorer**.

## <a name="flow-actions"></a>Flödesåtgärder

När du öppnar Azure Data Explorer-kopplingen finns det tre möjliga åtgärder som du kan lägga till i flödet.

I det här avsnittet beskrivs funktionerna och parametrarna för varje Microsoft Flow-åtgärd.

![Flöde Azure Data Explorer-åtgärder](./media/flow/flow-adx-actions.png)

### <a name="run-control-command-and-visualize-results"></a>Kör kontrollkommando och visualisera resultat

Använd kommandot Kör kontroll och visualisera resultat för att köra ett [kontrollkommando](https://docs.microsoft.com/azure/kusto/management/index).

1. Ange kluster-URL:en. Till exempel, https://clusterName.eastus.kusto.windows.net
1. Ange namnet på databasen.
1. Ange kontrollkommandot:
    * Välj dynamiskt innehåll från de appar och kopplingar som används i flödet
    * Lägga till ett uttryck för att komma åt, konvertera och jämföra värden
1. Om du vill skicka resultatet av den här åtgärden via e-post som en tabell eller ett diagram anger du diagramtypen, som kan vara:
    * En HTML-tabell
    * Ett cirkeldiagram
    * Ett tidsschema
    * Ett stapeldiagram

![Kommandot Kör flödeskontroll](./media/flow/flow-runcontrolcommand.png)

> [!IMPORTANT]
> Ange kluster-URL:en i fältet *Klusternamn.*

### <a name="run-query-and-list-results"></a>Kör fråge- och listresultat

> [!Note]
> Om frågan börjar med en punkt (vilket innebär att det är ett [kontrollkommando)](https://docs.microsoft.com/azure/kusto/management/index)använder du [kommandot Kör kontroll och visualiserar resultat](#run-control-command-and-visualize-results).

Den här åtgärden skickar en fråga till Kusto-klustret. De åtgärder som läggs till efteråt iterera över varje rad i resultatet av frågan.

I följande exempel utlöses en fråga varje minut och skickar ett e-postmeddelande baserat på frågeresultatet. Frågan kontrollerar antalet rader i databasen och skickar sedan ett e-postmeddelande endast om antalet rader är större än 0. 

![Kör frågelisteresultat](./media/flow/flow-runquerylistresults-2.png)

> [!Note]
> Om kolumnen har flera linjer körs kopplingen för varje rad i kolumnen.

### <a name="run-query-and-visualize-results"></a>Kör fråge- och visualisera resultat
        
> [!Note]
> Om frågan börjar med en punkt (vilket innebär att det är ett [kontrollkommando)](https://docs.microsoft.com/azure/kusto/management/index)använder du [kommandot Kör kontroll och visualiserar resultat](#run-control-command-and-visualize-results).
        
Använd åtgärden Kör fråga och visualisera resultat för att visualisera Kusto-frågeresultatet som en tabell eller ett diagram. Använd till exempel det här flödet för att ta emot dagliga ICM-rapporter via e-post. 
    
I det här exemplet returneras resultatet av frågan som en HTML-tabell.
            
![Kör fråge- och visualisera resultat](./media/flow/flow-runquery.png)

> [!IMPORTANT]
> Ange kluster-URL:en i fältet *Klusternamn.*

## <a name="email-kusto-query-results"></a>E-post Kusto frågeresultat

Du kan inkludera ett steg i valfritt flöde för att skicka rapporter via e-post till valfri e-postadress. 

1. Välj **+ Nytt steg** om du vill lägga till ett nytt steg i flödet.
1. I sökfältet anger du Office 365 och väljer **Office 365 Outlook**.
1. Välj **Skicka ett e-postmeddelande (V2)**.
1. Ange e-postadressen till den plats där du vill att e-postrapporten ska skickas.
1. Ange ämnet för e-postmeddelandet.
1. Välj **kodvy**.
1. Placera markören i fältet *Brödtext* och välj **Lägg till dynamiskt innehåll**.
1. Välj **BodyHtml**.
    ![Skicka e-post](./media/flow/flow-send-email.png)
1. Välj **visa avancerade alternativ**.
1. Välj *Bifogadenknamn* i fältet **Bifogade**filer namn .
1. Välj *Bifogat* innehåll i fältet **Bifogade filer.**
1. Om det behövs lägger du till fler bilagor. 
1. Om det behövs, ställ in betydelsenivån.
1. Välj **Spara**.

![Skicka e-post](./media/flow/flow-add-attachments.png)

## <a name="check-if-your-flow-succeeded"></a>Kontrollera om flödet lyckades

Kontrollera om flödet lyckades genom flödet:
1. Gå till [startsidan](https://flow.microsoft.com/)för Microsoft Flow .
1. Välj [Mina flöden](https://flow.microsoft.com/manage/flows)på huvudmenyn .
    ![Sidan Mina flöden](./media/flow/flow-myflows.png)
1. På raden i flödet som du vill undersöka markerar du ikonen fler kommandon och kör sedan **historik**.

    ![Kör historikmeny](./media/flow//flow-runhistory.png)

    Alla flödeskörningar visas med starttid, varaktighet och status.
    ![Sidan Kör historikresultat](./media/flow/flow-runhistoryresults.png)

    Om du vill ha fullständig information om flödet väljer du det flöde du vill undersöka på sidan [Mina flöden.](https://flow.microsoft.com/manage/flows)

    ![Kör historikens fullständiga resultatsida](./media/flow/flow-fulldetails.png) 

Om du vill se varför en körning misslyckades väljer du starttiden för körningen. Flödet visas och steget i flödet som misslyckades indikeras av ett rött utropstecken. Expandera det misslyckade steget för att visa dess information. Den högra rutan innehåller information om felet så att du kan felsöka det.

![Flödesfel](./media/flow/flow-error.png)

## <a name="timeout-exceptions"></a>Undantag för timeout

Ditt flöde kan misslyckas och returnera ett "RequestTimeout"-undantag om det körs i mer än sju minuter.

Läs mer om [Begränsningar i Microsoft Flow](#limitations).
    
Samma fråga kan köras i Azure Data Explorer där tiden inte är begränsad och kan ändras.
            
Undantaget "RequestTimeout" visas i bilden nedan:
    
![Undantagsfel för tidsgränsen för flödesbegäran](./media/flow/flow-requesttimeout.png)
    
Om du vill åtgärda ett timeout-problem kan du försöka göra frågan mer effektiv så att den körs snabbare eller separera den i segment. Varje segment kan köras på en annan del av frågan.

Mer information finns i [metodtips för frågor](https://docs.microsoft.com/azure/kusto/query/best-practices).

## <a name="limitations"></a>Begränsningar

* Resultaten som returneras till klienten är begränsade till 500 000 poster. Det totala minnet för dessa poster kan inte överstiga 64 MB och sju minuters körningstid.
* Kontakten stöder inte [gaffel-](https://docs.microsoft.com/azure/kusto/query/forkoperator) och fasningsoperatorerna. [facet](https://docs.microsoft.com/azure/kusto/query/facetoperator)
* Flow fungerar bäst på Microsoft Edge och Chrome.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [Microsoft Azure Explorer Logic App-kopplingen](https://docs.microsoft.com/azure/kusto/tools/logicapps) som är ett annat sätt att köra Kusto-frågor och kommandon automatiskt som en del av en schemalagd eller utlöst aktivitet.
