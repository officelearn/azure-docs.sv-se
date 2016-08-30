<properties
    pageTitle="Importera data till Azure Search med hjälp av indexerare på Azure Portal | Microsoft Azure | Värdbaserad söktjänst i molnet"
    description="Hur du använder indexerare på Azure Portal."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="mblythe"
    editor=""
    tags="Azure Portal"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="03/10/2016"
    ms.author="heidist"/>

# Importera data till Azure Search med hjälp av portalen

Azure Portal innehåller kommandot **Importera data** på instrumentpanelen för Azure Search som hjälper dig att lägga in data i Azure Search. Kommandot är beroende av de inbyggda indexerarfunktionerna som söker igenom en befintlig datakälla och som skapar och laddar upp dokument baserat på raduppsättningen som hittas i datakällan.

I guiden består dataimporten av tre delar:

- en anslutning till en datakälla
- ett målindex som data laddas upp till (guiden kan ofta generera detta åt dig)
- ett schema som körs nu eller enligt regelbundna intervaller.

För att kunna använda en indexerare eller kommandot **Importera data** måste din primära datakälla vara en datakälla som stöds: Azure SQL Database, SQL Server-relationsdatabaser på en virtuell Azure-dator eller Azure DocumentDB.

Du kan bara importera från en enskild tabell, vy eller likvärdig datastruktur. Du kan behöva skapa den här datastrukturen i programmets datakälla först så att rätt metadata och indata hamnar i sökindexet.

Du kan testa det här arbetsflödet med exempeldata. Om du behöver hjälp med att komma igång läser du [Komma igång med Azure Search på Azure Portal](search-get-started-portal.md).

##Konfigurera dataimport

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Öppna instrumentpanelen för Azure Search-tjänsten. Här är några sätt som du kan hitta instrumentpanelen på.
    - Klicka på **Start** i snabbåtkomstfältet. Startsidan innehåller paneler för varje tjänst i din prenumeration. Klicka på panelen för att öppna instrumentpanelen för tjänsten.
    - Klicka på **Bläddra bland alla** > **Filtrera efter** > **Söktjänster** för att leta upp din söktjänst i listan.

3. Instrumentpanelen för tjänsten innehåller ett kommandofält överst, inklusive ett för **Importera data**. Klicka på **Importera data** för att öppna bladet Importera data.

4. Klicka på **Anslut till dina data** för att ange en definition för datakällan som används av en indexerare. Alternativen är:
    -   Befintlig datakälla syftar på en redan befintlig definition av datakällan som skapats för en indexerare. Om du redan har definierat indexerare i söktjänsten kan du återanvända en definition av en datakällan för en annan import.
    -   Azure SQL används för att ange en datakällsanslutning till en SQL-databas i Azure eller en SQL Server-databas på en virtuell dator i Azure.
    -   DocumentDB används för att ange en datakällsanslutning för den typen av datakälla.

   För både Azure SQL och DocumentDB måste databasen finnas i din prenumeration. Du kan klistra in en anslutningssträng om du känner till den eller välja en datakälla som tidigare har skapats av någon som har skrivbehörighet för din prenumeration.

5. Klicka på **Anpassa målindex** för att slutföra standardindexet.
    - **Nyckeln** är obligatorisk. Fältet som du väljer för nyckeln måste vara ett strängfält som innehåller unika värden.
    - Fältnamnet och typen fylls vanligtvis i automatiskt. Du kan ändra datatypen.
    - Välj attribut för varje fält:
        - Hämtningsbar returnerar fältet i sökresultatet.
        - Filtrerbar gör att du kan referera till fältet i filteruttryck.
        - Sorterbar gör att fältet kan användas i en sortering.
        - Fasettbar gör att fältet kan användas för fasetterad navigering.
        - Sökbar gör att du kan använda fulltextsökning.
    - Klicka på fliken **Analyzer** om du vill ange ett språkanalysverktyg på fältnivå. Mer information finns i [Skapa ett index för dokument på flera språk](search-language-support.md).
    - Klicka på **Förslagsställare** om du vill aktivera Komplettera automatiskt eller frågeifyllningsförslag.

6. Klicka på **Importera dina data** om du vill köra importåtgärden med alternativet Kör nu eller definiera ett återkommande schema.

Dataimporten som du precis har slutfört skapade en indexerare i bakgrunden. Nu kan du redigera indexeraren direkt om du vill ändra någon av dess komponenter.

##Redigera en befintlig indexerare

Dubbelklicka på panelen Indexerare på instrumentpanelen för tjänsten för att visa en lista med alla indexerare som skapats för din prenumeration. Dubbelklicka på en av indexerarna för att köra, redigera eller ta bort den.



<!--HONumber=jun16_HO2-->


