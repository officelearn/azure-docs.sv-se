---
title: 'Gör så här: Sök i Data Catalog'
description: Den här artikeln ger en översikt över hur du söker i en data katalog.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/15/2020
ms.openlocfilehash: 87ca3c49d6a96f083cc50f01f5b9a4a739d688a1
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96554029"
---
# <a name="search-the-azure-purview-data-catalog"></a>Sök i Azure avdelningens kontroll-Data Catalog

Den här artikeln beskriver hur du använder de olika Sök funktionerna i Azure avdelningens kontroll-Data Catalog.

## <a name="search-the-catalog-for-assets"></a>Sök i katalogen för till gångar

Stegen för att genomföra en till gångs sökning är:

1. [Öppna dialog rutan för till gångs sökning](#open-the-asset-search-dialog) genom att välja **Sök katalog**.
1. [Ange Sök villkor](#enter-search-terms) för att hitta till gångar med egenskaper som matchar villkoren.
1. [Ange snabb filter](#set-quick-filters) för att begränsa sökningen.
1. [Starta sökningen](#start-the-search) och gå till Sök resultaten.

Det spelar ingen roll om du anger snabb filter före eller efter att du har angett Sök termer.

Om det inte finns några Sök villkor och inga filter, innehåller Sök resultatet alla till gångar.

### <a name="open-the-asset-search-dialog"></a>Öppna dialog rutan för till gångs sökning

Öppna dialog rutan för till gångs sökning genom att välja **Sök katalog**.

:::image type="content" source="./media/how-to-search-catalog/search-catalog.png" alt-text="Under Sök katalogen finns det ett vänster fönster med Sök filter och ett höger fönster med senaste sökningar." border="true":::

I dialog rutan Sök visas snabb filter, Sök historik och en lista över nyligen använda till gångar.

:::image type="content" source="./media/how-to-search-catalog/asset-search-dialog.png" alt-text="Sök listan finns i den högra rutan under Sök katalogen." border="true":::

### <a name="enter-search-terms"></a>Ange Sök villkor

Ange en eller flera Sök villkor i **Sök katalogen**. När du skriver visas matchande Sök villkor från de senaste sökningarna i **de senaste sökningarna**, föreslagna matchande Sök villkor visas i **Sök förslag** och matchande data till gångar visas i **till gångs förslag**.

:::image type="content" source="./media/how-to-search-catalog/enter-search-terms.png" alt-text="Skärm bild som visar resultatet av en sökning som anges i rutan Sök katalog":::

Sök resultaten innehåller bara till gångar med en eller flera egenskaper som matchar Sök villkoren. Dessa egenskaper omfattar till gångs namn, till gångs typ, klassificeringar och kontakter.

#### <a name="types-of-search-criteria"></a>Typer av Sök villkor

Azure avdelningens kontroll stöder följande typer av Sök kriterier.

> [!Note]
> Ange alltid booleska operatorer (**och**, **eller**, **inte**) med versaler. I annat fall spelar det ingen roll eller gör extra blank steg.

- **Hive**: hitta dokument som innehåller **Hive**.
- **Hive-databas**: hitta dokument som innehåller exakt **Hive-databas**.
- **HIVE eller databas**: hitta dokument som innehåller **Hive** eller **databas**, eller både och.
- **HIVE och databas**, **Hive && databas**: hitta dokument som innehåller både **Hive** och **databas**.
- **HIVE och (databas eller lager)**: hitta dokument som innehåller **Hive** och antingen **databas** eller **lager**, eller båda.
- **Hive, inte databas**: hitta dokument som innehåller **Hive**, men inte **databas**.
- **hiv**: hitta dokument som innehåller ett ord som börjar med **hiv**. Till exempel **hiv**, **Hive**, **hivbar** (* är ett jokertecken som matchar valfritt antal tecken).

### <a name="set-quick-filters"></a>Ange snabb filter

Sök Resultat listan baseras på Sök villkoren som du anger i **Sök katalogen** och på de värden som du väljer för snabb filtren.

Ett snabb filter begränsar Sök Resultat listan till till gångar som har ett valt värde för en egenskap. Filtret har en nedrullningsbar listruta och en text ruta. List rutan visar värden för de egenskaper som finns i de *aktuella* Sök resultaten. Bredvid varje värde i listan är antalet till gångar i de aktuella Sök resultaten som har det värdet. Om du väljer ett värde i listan begränsas Sök resultatet till till gångar som har det värdet. Du kan bara välja ett värde.

De aktuella Sök resultaten som används i den nedrullningsbara listan bestäms av:

- Sök villkoren som anges i **Sök katalogen**. 
- De värden som väljs i snabb filtren.

Här är ett exempel på snabb filtret "till gångs typ".

:::image type="content" source="./media/how-to-search-catalog/asset-type-quick-filter.png" alt-text="Exempel på exempel på till gångs typ." border="true":::

Du kan ange text i text rutan om du vill begränsa värdena i den nedrullningsbara listan till värden som matchar eller delvis matchar texten. Exempel på hur du använder text rutan finns i [snabb söknings snabb filter: Filtrera efter till gångs typ](#search-quick-filter-filter-by-asset-type)och [Sök efter snabb filter: Filtrera efter klassificering](#search-quick-filter-filter-by-classification).

#### <a name="search-quick-filter-filter-by-asset-type"></a>Sök snabb filter: Filtrera efter till gångs typ

Använd snabb filtret för **till gångs typ** för att filtrera efter till gångs typ. Den nedrullningsbara listan visar de resurs typer som finns i de aktuella Sök resultaten, enligt Sök villkoren och snabb filtren. För varje typ visas antalet till gångar av den typen.

:::image type="content" source="./media/how-to-search-catalog/asset-type-quick-filter.png" alt-text="Snabb filter för till gångs typ är markerat. Den visar till gångs typer och ett antal för var och en." border="true":::

Välj en till gångs typ för att begränsa Sök resultatet till till gångar av den typen. Du kan bara välja en typ.

Om du bara vill visa till gångs typer vars namn matchar en sträng anger du strängen i text rutan. Om du till exempel bara vill visa till gångs typer med **SQL** i deras namn anger du **SQL**.

:::image type="content" source="./media/how-to-search-catalog/filter-asset-types.png" alt-text="Fönstret snabb filter har SQL för &quot;till gångs typ&quot;. Listan med till gångar som innehåller SQL visar tre poster." border="true":::

Välj en till gångs typ för att begränsa Sök resultatet till till gångar av den typen. Du kan bara välja en typ.

#### <a name="search-quick-filter-filter-by-classification"></a>Sök snabb filter: Filtrera efter klassificering

Om du vill filtrera efter till gångs klassificering använder du snabb filtret för **klassificering** . I list rutan visas de klassificeringar som har tilldelats till en eller flera till gångar i de aktuella Sök resultaten, som definieras av Sök villkoren och snabb filtren. För varje klassificering visas antalet till gångar som har tilldelats klassificeringen.

:::image type="content" source="./media/how-to-search-catalog/classification-quick-filter.png" alt-text="Snabb filter för klassificering är markerat." border="true":::

Välj en klassificering som begränsar Sök resultatet till till gångar tilldelade den klassificeringen. Du kan bara välja en klassificering.

Om du bara vill visa klassificeringar vars namn matchar en sträng anger du strängen i text rutan. Om du till exempel bara vill visa klassificeringar med **nummer** i deras namn anger du **Number**.

:::image type="content" source="./media/how-to-search-catalog/filter-classifications.png" alt-text="I fönstret snabb filter är klassificeringen &quot;bank&quot; och klassificeringarna i listan innehåller det värdet." border="true":::

Välj en klassificering för att begränsa Sök resultaten till till gångar som har tilldelats den klassificeringen. Du kan inte välja fler än en klassificering.

#### <a name="search-quick-filter-filter-by-contacts"></a>Sök snabb filter: Filtrera efter kontakter

En *kontakt* är en person som är tilldelad till en till gång som en ägare eller expert. När du visar till gångs information visas kontakter på fliken **kontakter** .

Det finns två sätt att söka efter till gångar där en viss kontakt har tilldelats.

- Ange hela eller en del av kontakt namnet i **Sök katalogen** och gör en sökning. Sök resultaten innehåller till gångar som har kontakter vars namn matchar dina Sök villkor.
- Välj kontakt med intresse i snabb filtret för **kontakter** och gör en sökning.

:::image type="content" source="./media/how-to-search-catalog/contact-quick-filter.png" alt-text="Värdet för person i rutan snabb filter är ' Darren '. Det finns tre förslag i fönstret förslag." border="true":::

## <a name="search-example"></a>Sök exempel

Nu ska vi titta på ett hypotetiskt exempel för att se hur Sök villkoren och snabb filtren interagerar för att fastställa Sök resultaten. I synnerhet övervakas antalet **Azure-Blob Storage** för till gångs typ.

- Vi gör den första sökningen utan att ange några Sök villkor och inga värden har marker ATS i snabb filtren. Sökningen hittar alla till gångar i katalogen. Listan Sök Resultat och snabb filtret för **till gångs typ** visas:

    - Sök Resultat listan har 164 230 till gångar, som är alla till gångar i katalogen.
    - List rutan **till gångs typ** har 43 poster. Detta är alla till gångs typer i katalogen. Eftersom varje till gång är av en och endast en typ, är summan av antalet 43 till gångs typer 164 230.
    - **Azure Blob Storage** till gångs typ är den första posten i list rutan för snabb filtret för **till gångs typ** . Värdena sorteras efter antal, störst först, så **Azure Blob Storage** är den vanligaste till gångs typen. Antalet är 118 174.

- Nu anger vi **Parquet** i **Sök katalogen** och gör en ny sökning. Sök resultaten innehåller endast till gångar med egenskaper som matchar **Parquet**. Detta minskar alla antal, enligt följande:

    - Sök Resultat listan har 493 till gångar. Endast 493 av 164 230-till gångarna i katalogen har egenskaper som matchar "Parquet".
    - List rutan **till gångs typ** innehåller 15 poster. Var och en av de 493 resurserna är av någon av dessa 15 typer och summan av antalet av de 15 typerna är 493.
    - Det finns 456 till gångar av typen **Azure Blob Storage**. De andra 37 (493 minus 456) till gångarna är av någon av de andra 14 typerna.

- Nu tittar vi på den nedrullningsbara listan med ett annat snabb filter, **klassificering**:

    - Det finns 12 klassificeringar för 493-till gångar i Sök Resultat listan. Antalet för 493-till gångar summeras inte till 493, eftersom ett valfritt antal klassificeringar kan tilldelas till en till gång.
    - **Personens namn** klassificering tilldelas till 36 till gångar, mer än någon annan klassificering.

- Vi väljer **personens namn** klassificering. Listan över Sök Resultat sjunker till 36 till gångar, som förväntat eftersom antalet för **personens namn** var 36. Inget av dessa resultat är av typen **Azure Blob Storage**.

Vi kan avsluta att det inte finns någon till gång vars typ är **Azure Blob Storage** som matchar **Parquet**, och som har en klassificering av **personens namn**.

## <a name="start-the-search"></a>Starta sökningen

När du söker, matchas Sök villkoren som du anger i **Sök katalogen** mot till gångens egenskaper. Dessa egenskaper är namn, typ, klassificering och kontakter. Till gångarna med matchande egenskaper visas i Sök Resultat listan om de inte utesluts av ett snabb filter.

När du har angett Sök villkoren och angett snabb filter, startar du sökningen på något av följande sätt:

- Om du vill söka baserat på de villkor som du har angett väljer du Sök ikonen ( :::image type="icon" source="./media/how-to-search-catalog/search-icon.png"::: ), trycker på **RETUR** eller väljer **Visa Sök Resultat**.
- Om du vill söka med villkor från en tidigare sökning väljer du dem från **dina senaste sökningar**.
- Om du vill söka med föreslagna villkor väljer du dem från **Sök förslag**.

Välj en till gång från **till gångs förslag** för att gå direkt till sidan med information för till gången. Ingen sökning görs.

Resultat listan för förslag och användars ökningar kan skilja sig något. Resultaten i listan över förslag baseras på suddiga matchningar, medan användardefinierade Sök resultat baseras på exakta matchningar.

När du söker visas sidan **Sök Resultat** och de till gångar som hittas av sökningen visas.

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="Skärm bild som visar Sök resultatet för ett Sök värde på contoso.":::

Om du vill visa till gångs information väljer du ett till gångs namn.

Använd kontrollerna längst ned på sidan med Sök Resultat för att navigera till andra Sök Resultat sidor.

:::image type="content" source="./media/how-to-search-catalog/page-navigation.png" alt-text="Skärm bild som visar hur du navigerar genom sidan med Sök resultat.":::

### <a name="sort-search-results"></a>Sortera Sök Resultat

Använd **Sortera efter** för att sortera Sök resultaten efter **relevans** eller **namn**.

:::image type="content" source="./media/how-to-search-catalog/sort-by.png" alt-text="Skärm bild som visar hur du sorterar Sök resultaten. I det här exemplet är List rutan Sortera efter inställd på relevans.":::

### <a name="search-results-dynamic-filters"></a>Dynamiska filter för Sök Resultat

**Filter** fönstret på sidan **Sök Resultat** har filter som ger dynamisk filtrering av till gångarna i Sök Resultat listan. Filtreringen är dynamisk i att ytterligare filter kan visas baserat på filter val.

De dynamiska filtren har en kryss ruta för varje värde i den nedrullningsbara listan. Använd de här kryss rutorna för att filtrera på så många värden som du vill.

#### <a name="search-results-dynamic-filter-filter-by-asset-type"></a>Dynamiska filter för Sök Resultat: Filtrera efter till gångs typ

Om du väljer en till gångs typ i list rutan **till gångs typ** visas dynamiska filter som ger dig ytterligare sätt att begränsa dina Sök resultat. Filtren varierar beroende på vilken till gångs typ som valts. Om du till exempel väljer **Azure SQL Database** visas dynamiska filter för Server, databas och schema. Värdena i dessa filter är från till gångarna i Sök resultatet av den valda typen.

:::image type="content" source="./media/how-to-search-catalog/asset-type-dynamic-filter.png" alt-text="Azure SQL Database filter objekt är det enda objekt som är valt för den till gångs typen. Ett Sök Resultat av den här typen av till gång är också markerat." border="true":::

#### <a name="search-results-dynamic-filter-filter-by-classification"></a>Dynamiska filter för Sök Resultat: Filtrera efter klassificering

Varje klassificering i **klassificerings** listan gäller för minst ett objekt i Sök Resultat listan. Välj en eller flera klassificeringar för att begränsa Sök resultaten till till gångar med de valda klassificeringarna.

:::image type="content" source="./media/how-to-search-catalog/classification-dynamic-filter.png" alt-text="Klassificerings filtret för Sök resultat är markerat." border="true":::

#### <a name="edit-and-delete-search-results-filters"></a>Redigera och ta bort Sök Resultat filter

Avmarkera kryss rutan bredvid filter namnet om du vill ta bort ett filter.

### <a name="recently-accessed-assets"></a>Nyligen använda till gångar

I det **senast använda** avsnittet i den utökade sökrutan visas dina senast använda till gångar, om det finns några.

- Välj **Visa alla** i det **senast använda** avsnittet om du vill se en fullständig lista över nyligen använda till gångar.

   :::image type="content" source="./media/how-to-search-catalog/get-to-recent-view.png" alt-text="Skärm bild som visar det senast använda avsnittet i den expanderade Sök rutan.":::

   En lista över nyligen använda till gångar visas.

   :::image type="content" source="./media/how-to-search-catalog/recent.png" alt-text="Skärm bild som visar en lista över nyligen använda till gångar.":::

- Om du vill filtrera efter namn anger du en Sök sträng i **Filtrera efter namn**.

- Om du vill ta bort objekt från listan markerar du dem med deras kryss rutor och väljer sedan **ta bort**.

   :::image type="content" source="./media/how-to-search-catalog/remove-from-recent-view.png" alt-text="Skärm bild som visar hur du tar bort objekt från en lista med nyligen använda till gångar.":::

- Om du vill rensa hela listan väljer du **Rensa**.

   :::image type="content" source="./media/how-to-search-catalog/clear-recent-view-selections.png" alt-text="Skärm bild som visar hur du rensar en lista med nyligen använda till gångar":::

### <a name="search-assets"></a>Sök till gångar

Många andra sidor än **Home** har rutan **Sök till gångar** överst. Till exempel visas sidan till gångs information med **Sök till gångar** markerade.

:::image type="content" source="./media/how-to-search-catalog/search-assets.png" alt-text="Skärm bild som visar sidan till gångs information med Sök till gångar markerade":::

Välj **Sök till gångar** för att starta en sökruta som du får från **Sök katalogen** **hemma**, med samma funktioner.

:::image type="content" source="./media/how-to-search-catalog/search-assets-dialog.png" alt-text="Skärm bild som visar en expanderad Sök till gång-ruta.":::

## <a name="next-steps"></a>Nästa steg

- [Skapa, importera och exportera ord listans villkor](how-to-create-import-export-glossary.md)
- [Så här hanterar du term mallar för företags ord lista](how-to-manage-term-templates.md)
