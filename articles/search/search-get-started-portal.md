---
title: "Komma igång med Azure Search| Microsoft Docs"
description: "Lär dig skapa ditt första Azure-sökindex med den här guiden och exempeldata för DocumentDB. Den här portalbaserade kodfria övningen använder guiden Importera data."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 21adc351-69bb-4a39-bc59-598c60c8f958
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.date: 10/03/2016
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: 4fc33ba185122496661f7bc49d14f7522d6ee522
ms.openlocfilehash: 02623fc3d663a674e2184380915d651dff5760bc


---
# <a name="get-started-with-azure-search-in-the-portal"></a>Komma igång med Azure Search på portalen
Den här introduktionen utan kod hjälper dig att komma igång med Microsoft Azure Search med funktioner som är inbyggda i portalen. 

I självstudiekursen används en [Azure DocumentDB-exempeldatabas](#apdx-sampledata), som du enkelt kan skapa med våra data och instruktioner, men du kan också anpassa stegen till dina befintliga data i en DocumentDB- eller SQL-databas.

> [!NOTE]
> Den här introduktionskursen kräver en [Azure-prenumeration](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) och en [Azure Search-tjänst](search-create-service-portal.md). 
> 
> 

## <a name="find-your-service"></a>Hitta din tjänst
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Öppna instrumentpanelen för Azure Search-tjänsten. Här är några sätt som du kan hitta instrumentpanelen på.
   
   * Klicka på **Söktjänster** i snabbåtkomstfältet. Alla etablerade tjänster i din prenumeration visas i snabbåtkomstfältet. Om en söktjänst har definierats kan du se **Söktjänster** i listan.
   * Klicka på **Bläddra** i snabbåtkomstfältet och skriv ”search” i sökrutan för att visa en lista med alla söktjänster som skapats i dina prenumerationer.

## <a name="check-for-space"></a>Kontrollera utrymmet
Många kunder börjar med den kostnadsfria tjänsten. Den här versionen är begränsad till tre index, tre datakällor och tre indexerare. Kontrollera att du har plats för extra objekt innan du börjar. Den här guiden skapar ett av varje sorts ibjekt.

## <a name="create-an-index-and-load-data"></a>Skapa ett index och läsa in data
Sökfrågor iterera över ett *index* med sökbara data, metadata och konstruktioner som används för att optimera vissa sökbeteenden. Det första steget är att definiera och fyll i ett index.

Du kan skapa ett index på flera sätt. Om dina data finns i ett datalager som Azure Search kan crawla – till exempel Azure SQL Database, SQL Server på en virtuell Azure-dator eller DocumentDB – kan du enkelt skapa och fylla ett index med hjälp av en *indexerare*.

För att den här uppgiften ska förbli portalbaserad använder vi data från DocumentDB som kan crawlas med en indexerare via guiden **Importera data**. 

Innan du fortsätter måste du skapa en [DocumentDB-exempeldatabas](#apdx-sampledata) som du kan använda med den här självstudiekursen. När du är klar kommer du tillbaka till det här avsnittet och följer stegen nedan.

<a id="defineDS"></a>

#### <a name="step-1-define-the-data-source"></a>Steg 1: Definiera datakällan
1. Klicka på **Importera data** i kommandofältet på instrumentpanelen för Azure Search-tjänsten för att starta en guide som hjälper dig att skapa och fylla ett index.
   
    ![][7]
2. I guiden klickar du på **Datakälla** > **DocumentDB** > **Namn** och skriver ett namn för datakällan. En datakälla är ett anslutningsobjekt i Azure Search som kan användas med andra indexerare. När du har skapat det blir det tillgängligt som en ”befintlig datakälla” i din tjänst.
3. Välj ditt befintliga DocumentDB-konto, databasen och samlingen. Om du använder exempeldata som vi tillhandahåller kommer definitionen av din datakälla att se ut så här:
   
    ![][2]

Observera att vi hoppar över frågan. Det beror på att vi inte implementerar för ändringsspårning i vår datamängd den här gången. Om datamängden innehåller ett fält som håller reda på när en post uppdateras kan du konfigurera en Azure Search-indexerare att använda ändringsspårning för selektiva uppdateringar av ditt index.

Slutför det här steget i guiden genom att klicka på **OK**.

#### <a name="step-2-define-the-index"></a>Steg 2: Definiera indexet
Klicka på **Index** i guiden och ta en titt på designytan som används för att skapa ett Azure Search-index. Ett index kräver minst ett namn och en samling fält, där ett fält är markerat som dokumentnyckeln. Eftersom vi använder en DocumentDB-datamängd identifieras fälten automatiskt av guiden och indexet har redan inlästa fält och datatypstilldelningar. 

  ![][3]

Även om fälten och datatyperna har konfigurerats måste du fortfarande tilldela attribut. Kryssrutorna längs överkanten av fältlistan är *indexattribut* som styr hur fältet används. 

* **Hämtningsbar** innebär att det visas i listor med sökresultat. Du kan markera enskilda fält som ska utelämnas från sökresultat genom att avmarkera den här kryssrutan, till exempel om fält endast används i filteruttryck. 
* **Filtrerbar**, **Sorterbar** och **Fasettbar** avgör om ett fält kan användas i ett filter, en sortering eller en struktur för aspektbaserad navigering. 
* **Sökbar** innebär att ett fält ingår i fulltextsökning. Strängar är vanligtvis sökbara. Numeriska fält och fält för booleska värden är ofta markerade som icke sökbara. 

Innan du lämnar den här sidan markerar du fälten i indexet så att följande alternativ används (Hämtningsbar, Sökbar osv.). De flesta fält är hämtningsbara. De flesta strängfält är sökbara (du behöver inte göra nyckeln sökbar). Några fält som genre, orderableOnline, rating och tags är också filtrerbara, sorterbara och fasettbara. 

| Fält | Typ | Alternativ |
| --- | --- | --- |
| id |Edm.String | |
| albumTitle |Edm.String |Hämtningsbar, Sökbar |
| albumUrl |Edm.String |Hämtningsbar, Sökbar |
| genre |Edm.String |Hämtningsbar, Sökbar, Filtrerbar, Sorterbar, Fasettbar |
| genreDescription |Edm.String |Hämtningsbar, Sökbar |
| artistName |Edm.String |Hämtningsbar, Sökbar |
| orderableOnline |Edm.Boolean |Hämtningsbar, Filtrerbar, Sorterbar, Fasettbar |
| tags |Collection(Edm.String) |Hämtningsbar, Filtrerbar, Fasettbar |
| price |Edm.Double |Hämtningsbar, Filtrerbar, Fasettbar |
| margin |Edm.Int32 | |
| rating |Edm.Int32 |Hämtningsbar, Filtrerbar, Sorterbar, Fasettbar |
| inventory |Edm.Int32 |Hämtningsbar |
| lastUpdated |Edm.DateTimeOffset | |

Som en jämförelse är följande skärmbild en illustration av ett index som skapats enligt specifikationen i föregående tabell.

 ![][4]

Slutför det här steget i guiden genom att klicka på **OK**.

#### <a name="step-3-define-the-indexer"></a>Steg 3: Definiera indexeraren
Klicka på **Indexer** > **Namn** i guiden **Importera data**, skriv ett namn för indexeraren och använd standardinställningarna för alla andra värden. Det här objektet definierar en körbar process. När du har skapat det kan du lägga till det i ett återkommande schema, men för stunden ska du använda standardalternativet och köra indexeraren en gång direkt när du klickar på **OK**. 

Dina importerade dataposter bör alla vara ifyllda och klara att användas.

  ![][5]

Kör guiden genom att klicka på **OK** för att starta importen och stänga guiden.

## <a name="check-progress"></a>Kontrollera förloppet
Kontrollera förloppet genom att gå tillbaka till instrumentpanelen för tjänsten, rulla nedåt och dubbelklicka på panelen **Indexerare** för att öppna listan med indexerare. Du bör se de indexerare som du nyss skapade i listan och du bör se statusen ”pågående” eller ”lyckades”, tillsammans med antalet dokument som indexerats i Azure Search.

  ![][6]

## <a name="query-the-index"></a>Skicka frågor mot indexet
Nu har du ett sökindex som du kan börja skicka frågor mot. 

**Sökutforskaren** är ett frågeverktyg som är inbyggt i portalen. Det innehåller en kryssruta så att du kan bekräfta att en sökning returnerar de data som du förväntar dig. 

1. Klicka på **Sökutforskaren** i kommandofältet.
2. Notera vilket index som är aktivt. Om det inte är det index som du precis har skapat klickar du på **Ändra index** i kommandofältet för att välja det index som du vill använda.
3. Lämna sökrutan tom och klicka sedan på **Sök** för att köra en sökning med jokertecken som returnerar alla dokument.
4. Ange några textsökningsfrågor. Du kan granska resultatet från sökningen med jokertecken för att få en uppfattning om de artister, album och genrer som du kan skicka frågor mot.
5. Prova med andra frågesyntaxer med hjälp av [exemplen i slutet av den här artikeln](https://msdn.microsoft.com/library/azure/dn798927.aspx) om du vill ha idéer om hur du kan ändra en fråga så att den använder söksträngar som kan hittas i indexet.

## <a name="next-steps"></a>Nästa steg
När du har kört guiden en gång kan du gå tillbaka och visa eller ändra enskilda komponenter: index, indexerare eller datakälla. Vissa ändringar, till exempel ändring av ett fälts datatyp, är inte tillåtet i indexet, men de flesta egenskaper och inställningar kan ändras. Du kan visa enskilda komponenter genom att klicka på panelerna **Index**, **Indexerare** eller **Datakällor** på instrumentpanelen för att visa en lista med befintliga objekt.

Mer information om andra funktioner som anges i den här artikeln finns på dessa länkar:

* [Indexerare](search-indexer-overview.md)
* [Skapa index (innehåller en detaljerad förklaring av indexattributen)](https://msdn.microsoft.com/library/azure/dn798941.aspx)
* [Sökutforskaren](search-explorer.md)
* [Söka efter dokument (innehåller exempel på frågesyntax)](https://msdn.microsoft.com/library/azure/dn798927.aspx)

Du kan prova med samma arbetsflöde och använda guiden Importera data för andra datakällor som Azure SQL Database eller SQL Server på virtuella datorer i Azure.

> [!NOTE]
> En ny funktion är indexeringsstöd för crawling i Azure Blob Storage. Funktionen är dock fortfarande i förhandsgranskningsfasen och är inte tillgänglig på portalen än. Om du vill försöka att använda indexeraren måste du skriva kod. Mer information finns i [Indexera Azure Blob Storage i Azure Search](search-howto-indexing-azure-blob-storage.md).
> <a id="apdx-sampledata"></a>
> 
> 

## <a name="appendix-create-sample-data-in-documentdb"></a>Tillägg: Skapa exempeldata i DocumentDB
Det här avsnittet beskriver hur du skapar en liten databas i DocumentDB som kan användas för att utföra åtgärderna i den här självstudiekursen.

Följande anvisningar ger allmänna riktlinjer, men är inte en fullständig beskrivning. Om du behöver mer hjälp med navigera eller använda DocumentDB portal kan du läsa DocumentDB-dokumentationen, men de flesta kommandon som du behöver finns i tjänstkommandofältet överst i instrumentpanelen eller i databasbladet. 

  ![][1]

### <a name="create-musicstoredb-for-this-tutorial"></a>Skapa musicstoredb för den här självstudiekursen
1. [Klicka här](https://github.com/HeidiSteen/azure-search-get-started-sample-data) för att ladda ned en ZIP-fil som innehåller JSON-datafilerna för musikarkivet. Vi tillhandahåller 246 JSON-dokument för den här datauppsättningen.
2. Lägg till DocumentDB i din prenumeration och öppna instrumentpanelen för tjänsten.
3. Klicka på **Lägg till databas** för att skapa en ny databas med ID:t `musicstoredb`. Den visas i databaspanelen längre ned på sidan efter att den har skapats.
4. Klicka på namnet på databasen för att öppna databasbladet.
5. Klicka på **Lägg till samling** för att skapa en samling med ID:t `musicstorecoll`.
6. Klicka på **Dokumentutforskaren**.
7. Klicka på **Överför**.
8. I **Överför dokument** navigerar du till den lokala mappen som innehåller de JSON-filer som du hämtade tidigare. Välj JSON-filer i batchar om 100 eller färre.
   * 386.json
   * 387.json
   * . . .
   * 486.json
9. Upprepa samma steg för att hämta nästa batch med filer tills du har laddat upp den sista, 669.json.
10. Klicka på **Frågeutforskaren** för att bekräfta att data har laddats upp och uppfyller överföringskraven för Dokumentutforskaren.

Ett enkelt sätt att göra detta är att använda standardfrågan, men du kan också ändra standardfrågan så att den filtrerar upp 300 (det finns färre än 300 objekt i den här datauppsättningen).

Du bör få tillbaka JSON-utdata, som börjar med dokument 386 och slutar med 669. När data har lästs in kan du [gå tillbaka till stegen i den här genomgången](#defineDS) för att skapa ett index med hjälp av **guiden Importera data**.

<!--Image references-->
[1]: ./media/search-get-started-portal/AzureSearch-GetStart-Docdbmenu1.png
[2]: ./media/search-get-started-portal/AzureSearch-GetStart-DataSource.png
[3]: ./media/search-get-started-portal/AzureSearch-GetStart-DefaultIndex.png
[4]: ./media/search-get-started-portal/AzureSearch-GetStart-FinishedIndex.png
[5]: ./media/search-get-started-portal/AzureSearch-GetStart-ImportReady.png
[6]: ./media/search-get-started-portal/AzureSearch-GetStart-IndexerList.png
[7]: ./media/search-get-started-portal/search-data-import-wiz-btn.png



<!--HONumber=Dec16_HO1-->


