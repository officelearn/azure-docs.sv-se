---
title: 'Självstudie: Så här laddar du upp en bild – Visuell sökning i Bing'
titleSuffix: Azure Cognitive Services
description: Delar upp processen att ladda upp en bild till Bing för att få information om den, samt parsar och visar svaret.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: scottwhi
ms.openlocfilehash: 42a7db316e844e5dbd09fb75a07e1c7883a9cec9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60829573"
---
# <a name="tutorial-upload-images-to-the-bing-visual-search-api"></a>Självstudier: Ladda upp bilder till Bing Visual Search API

Med API för visuell sökning i Bing kan du söka på webben efter bilder som liknar dem som du laddar upp. Använd den här självstudien för att skapa en webbapp som kan skicka sökfrågor till API:et och visa den information som den returnerar på webbplatsen. Observera att det här programmet inte följer alla [användnings- och visningskrav för Bing](../bing-web-search/use-display-requirements.md) för användning av API:et.

Du hittar den fullständiga källkoden för det här exemplet med ytterligare felhantering och kommentarer på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchUploadImage.html).

I den här självstudieappen visas hur du:

> [!div class="checklist"]
> * Ladda upp en bild till API för visuell sökning i Bing
> * Visa sökresultat för bilder i en webbapp
> * Utforska olika typer av information som tillhandahålls av API:et

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-structure-the-webpage"></a>Skapa och strukturera webbplatsen

Skapa en HTML-sida som skickar en bild till Bing Visual Search API, får insikter och visar dem. Skapa en fil med namnet ”uploaddemo.html” i din favoritredigerare eller IDE. Lägg till följande grundläggande HTML-strukturen i filen:

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <title>Visual Search Upload Demo</title>
    </head>

    <body>
    </body>
</html>
```

Dela upp sidan i en begäran, där användaren tillhandahåller den information som krävs för begäran, och ett svar avsnitt där insikterna som visas. Lägg till följande `<div>`-taggar i `<body>`. Den `<hr>` taggen skiljer visuellt avsnittet begäran från svarsavsnittet:

```html
<div id="requestSection"></div>
<hr />
<div id="responseSection"></div>
```

Lägg till en `<script>` tagg i `<head>` tagg som innehåller JavaScript för programmet:

```html
<script>
<\script>
```

## <a name="get-the-upload-file"></a>Hämta uppladdningsfilen

För att användaren ska kunna välja en bild som ska laddas upp använder programmet taggen `<input>` med typattributet angett till `file`. Användargränssnittet måste visa tydligt att programmet använder Bing till att hämta sökresultaten.

Lägg till följande `<div>` till den `requestSection` `<div>`. Filens indata godkänner en enskild fil av valfri bildtyp (till exempel .jpg, .gif, .png). `onchange`-händelsen anger vilken hanterare som anropas när en användare väljer en fil.

Den `<output>` indatatagg används för att visa en miniatyrbild för den valda avbildningen:

```html
<div>
    <p>Select image to get insights from Bing:
        <input type="file" accept="image/*" id="uploadImage" name="files[]" size=40 onchange="handleFileSelect('uploadImage')" />
    </p>

    <output id="thumbnail"></output>
</div>
```

## <a name="create-a-file-handler"></a>Skapa en filhanterare

Skapa en hanterarfunktion som kan läsa in den bild som du vill ladda upp. När du går igenom filerna i objektet `FileList` bör hanteraren kontrollera att den valda filen är en bildfil och att dess storlek är 1 MB eller mindre. Om bilden är större, måste du minska storleken innan du skickar den. Till sist visas hanteraren en miniatyr för avbildningen:

```javascript
function handleFileSelect(selector) {

    var files = document.getElementById(selector).files; // A FileList object

    for (var i = 0, f; f = files[i]; i++) {

        // Ensure the file is an image file.
        if (!f.type.match('image.*')) {
            alert("Selected file must be an image file.");
            document.getElementById("uploadImage").value = null;
            continue;
        }

        // Image must be <= 1 MB and should be about 1500px.
        if (f.size > 1000000) {
            alert("Image must be less than 1 MB.");
            document.getElementById("uploadImage").value = null;
            continue;
        }

        var reader = new FileReader();

        // Capture the file information.
        reader.onload = (function(theFile) {
            return function(e) {
                var fileOutput = document.getElementById('thumbnail');

                if (fileOutput.childElementCount > 0) {
                    fileOutput.removeChild(fileOutput.lastChild);  // Remove the current pic, if it exists
                }

                // Render thumbnail.
                var span = document.createElement('span');
                span.innerHTML = ['<img class="thumb" src="', e.target.result,
                                    '" title="', escape(theFile.name), '"/>'].join('');
                fileOutput.insertBefore(span, null);
            };
        })(f);

        // Read in the image file as a data URL.
        reader.readAsDataURL(f);
    }
}
```

## <a name="add-and-store-a-subscription-key"></a>Lägga till och lagra en prenumerationsnyckel

Programmet kräver en prenumeration för att göra anrop till Bing Visual Search API. För den här självstudien tillhandahåller du den i användargränssnittet. Lägg till följande `<input>` tagga (med attributet type inställd text) till den `<body>` nedanför filens `<output>` tagg:

```html
    <div>
        <p>Subscription key: 
            <input type="text" id="key" name="subscription" size=40 maxlength="32" />
        </p>
    </div>
```

Nu när du har bilden och prenumerationsnyckeln kan du anropa Visuell sökning i Bing för att få information om bilden. I de här självstudierna använder anropet standard marknaden (`en-us`) och värdet för säker sökning (`moderate`).

Det här programmet har ett alternativ för att ändra dessa värden. Lägg till följande `<div>` nedan prenumerationsnyckeln `<div>`. Programmet använder en `<select>`-tagg för att visa en listruta med marknaden och värden för säker sökning. Båda listorna visar standardvärdet.

```html
<div>
    <p><a href="#" onclick="expandCollapse(options.id)">Query options</a></p>

    <div id="options" style="display:none">
        <p style="margin-left: 20px">Market: 
            <select id="mkt">
                <option value="es-AR">Argentina (Spanish)</option>
                <option value="en-AU">Australia (English)</option>
                <option value="de-AT">Austria (German)</option>
                <option value="nl-BE">Belgium (Dutch)</option>
                <option value="fr-BE">Belgium (French)</option>
                <option value="pt-BR">Brazil (Portuguese)</option>
                <option value="en-CA">Canada (English)</option>
                <option value="fr-CA">Canada (French)</option>
                <option value="es-CL">Chile (Spanish)</option>
                <option value="da-DK">Denmark (Danish)</option>
                <option value="fi-FI">Finland (Finnish)</option>
                <option value="fr-FR">France (French)</option>
                <option value="de-DE">Germany (German)</option>
                <option value="zh-HK">Hong Kong SAR(Traditional Chinese)</option>
                <option value="en-IN">India (English)</option>
                <option value="en-ID">Indonesia (English)</option>
                <option value="it-IT">Italy (Italian)</option>
                <option value="ja-JP">Japan (Japanese)</option>
                <option value="ko-KR">Korea (Korean)</option>
                <option value="en-MY">Malaysia (English)</option>
                <option value="es-MX">Mexico (Spanish)</option>
                <option value="nl-NL">Netherlands (Dutch)</option>
                <option value="en-NZ">New Zealand (English)</option>
                <option value="no-NO">Norway (Norwegian)</option>
                <option value="zh-CN">People's Republic of China (Chinese)</option>
                <option value="pl-PL">Poland (Polish)</option>
                <option value="pt-PT">Portugal (Portuguese)</option>
                <option value="en-PH">Philippines (English)</option>
                <option value="ru-RU">Russia (Russian)</option>
                <option value="ar-SA">Saudi Arabia (Arabic)</option>
                <option value="en-ZA">South Africa (English)</option>
                <option value="es-ES">Spain (Spanish)</option>
                <option value="sv-SE">Sweden (Swedish)</option>
                <option value="fr-CH">Switzerland (French)</option>
                <option value="de-CH">Switzerland (German)</option>
                <option value="zh-TW">Taiwan (Traditional Chinese)</option>
                <option value="tr-TR">Turkey (Turkish)</option>
                <option value="en-GB">United Kingdom (English)</option>
                <option value="en-US" selected>United States (English)</option>
                <option value="es-US">United States (Spanish)</option>
            </select>
        </p>
        <p style="margin-left: 20px">Safe search: 
            <select id="safesearch">
                <option value="moderate" selected>Moderate</option>
                <option value="strict">Strict</option>
                <option value="off">off</option>
            </select>
        </p>
    </div>
</div>
```

## <a name="add-search-options-to-the-webpage"></a>Lägga till sökalternativ till webbplatsen

Programmet döljer listor i ett komprimerbart `<div>` som styrs av länken fråga. När du klickar på länken fråga den `<div>` expanderar så att du kan se och ändra frågealternativ. Om du klickar på länken frågan igen, den `<div>` döljer och är dold. Följande utdrag visar frågan alternativ länkens `onclick` hanterare. Hanteraren kontroller om den `<div>` visas eller döljs. Lägg till den här hanteraren i avsnittet `<script>`. Hanteraren används av alla komprimerbart `<div>` avsnitt i demon.

```javascript
// Contains the toggle state of divs.
var divToggleMap = {};  // divToggleMap['foo'] = 0;  // 1 = show, 0 = hide


// Toggles between showing and hiding the specified div.
function expandCollapse(divToToggle) {
    var div = document.getElementById(divToToggle);

    if (divToggleMap[divToToggle] == 1) {   // if div is expanded
        div.style.display = "none";
        divToggleMap[divToToggle] = 0;
    }
    else {                                  // if div is collapsed
        div.style.display = "inline-block";
        divToggleMap[divToToggle] = 1;
    }
}
```

## <a name="call-the-onclick-handler"></a>Anropa den `onclick` hanterare

Lägg till följande `"Get insights"` knappen under alternativen `<div>` i brödtexten. Med den här knappen kan du initiera anropet. När du klickar på knappen markören ändras till att vänta markören, och `onclick` hanteraren anropas.

```html
<p><input type="button" id="query" value="Get insights" onclick="document.body.style.cursor='wait'; handleQuery()" /></p>
```

Lägg till knappens `onclick` hanteraren, `handleQuery()` till den `<script>` tagg.

## <a name="handle-the-query"></a>Hantera frågan

Hanteraren `handleQuery()` garanteras prenumerationsnyckeln finns och är 32 tecken långt och att du har markerat en bild. Den rensar också bort eventuella insikter från tidigare frågor. Därefter anropar den funktionen `sendRequest()` för att göra anropet.

```javascript
function handleQuery() {
    var subscriptionKey = document.getElementById('key').value;

    // Make sure user provided a subscription key and image.
    // For this demo, the user provides the key but typically you'd
    // get it from secured storage.
    if (subscriptionKey.length !== 32) {
        alert("Subscription key length is not valid. Enter a valid key.");
        document.getElementById('key').focus();
        return;
    }

    var imagePath = document.getElementById('uploadImage').value;

    if (imagePath.length === 0)
    {
        alert("Please select an image to upload.");
        document.getElementById('uploadImage').focus();
        return;
    }

    var responseDiv = document.getElementById('responseSection');

    // Clear out the response from the last query.
    while (responseDiv.childElementCount > 0) {
        responseDiv.removeChild(responseDiv.lastChild);
    }

    // Send the request to Bing to get insights about the image.
    var f = document.getElementById('uploadImage').files[0];
    sendRequest(f, subscriptionKey);
}
```

## <a name="send-the-search-request"></a>Skicka sökbegäran

Den `sendRequest()` funktionen formaterar slutpunkts-URL, anger den `Ocp-Apim-Subscription-Key` rubrik för prenumerationsnyckeln, lägger till den binära filen för avbildningen som ska ladda upp, anger svarshanteraren och gör anropet:

```javascript
function sendRequest(file, key) {
    var market = document.getElementById('mkt').value;
    var safeSearch = document.getElementById('safesearch').value;
    var baseUri = `https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch?mkt=${market}&safesearch=${safeSearch}`;

    var form = new FormData();
    form.append("image", file);

    var request = new XMLHttpRequest();

    request.open("POST", baseUri);
    request.setRequestHeader('Ocp-Apim-Subscription-Key', key);
    request.addEventListener('load', handleResponse);
    request.send(form);
}
```

## <a name="get-and-handle-the-api-response"></a>Hämta och hantera API-svaret

Funktionen `handleResponse()` hanterar svaret från anropet till Visuell sökning i Bing. Om anropet lyckas parsas JSON-svaret till separata taggar som innehåller insikterna. Sedan lägger den till sökresultaten till sidan. Programmet skapar sedan ett komprimerbart `<div>` för varje tagg att hantera hur mycket data som visas. Lägg till hanteraren i avsnittet `<script>`.

```javascript
function handleResponse() {
    if(this.status !== 200){
        alert("Error calling Bing Visual Search. See console log for details.");
        console.log(this.responseText);
        return;
    }

    var tags = parseResponse(JSON.parse(this.responseText));
    var h4 = document.createElement('h4');
    h4.textContent = 'Bing internet search results';
    document.getElementById('responseSection').appendChild(h4);
    buildTagSections(tags);

    document.body.style.cursor = 'default'; // reset the wait cursor set by query insights button
}
```

### <a name="parse-the-response"></a>Parsa svaret

Funktionen `parseResponse` konverterar JSON-svaret till ett ordlisteobjekt genom att iterera genom `json.tags`.

```javascript
function parseResponse(json) {
    var dict = {};

    for (var i =0; i < json.tags.length; i++) {
        var tag = json.tags[i];

        if (tag.displayName === '') {
            dict['Default'] = JSON.stringify(tag);
        }
        else {
            dict[tag.displayName] = JSON.stringify(tag);
        }
    }

    return(dict);
}
```

### <a name="build-a-tag-section"></a>Skapa ett taggavsnitt

Den `buildTagSections()` funktionen upprepas Analyserad JSON-taggar och anropar den `buildDiv()` funktionen för att skapa en `<div>` för varje tagg. Varje tagg visas som en länk. När en användare klickar på länken expanderas taggen och visar de insikter som är associerade med den. Avsnittet att komprimera leder till att klicka på länken igen.

```javascript
function buildTagSections(tags) {
    for (var tag in tags) {
        if (tags.hasOwnProperty(tag)) {
            var tagSection = buildDiv(tags, tag);
            document.getElementById('responseSection').appendChild(tagSection);
        }
    }  
}

function buildDiv(tags, tag) {
    var tagSection = document.createElement('div');
    tagSection.setAttribute('class', 'subSection');

    var link = document.createElement('a');
    link.setAttribute('href', '#');
    link.setAttribute('style', 'float: left;')
    link.text = tag;
    tagSection.appendChild(link);

    var contentDiv = document.createElement('div');
    contentDiv.setAttribute('id', tag);
    contentDiv.setAttribute('style', 'clear: left;')
    contentDiv.setAttribute('class', 'section');
    tagSection.appendChild(contentDiv);

    link.setAttribute('onclick', `expandCollapse("${tag}")`);
    divToggleMap[tag] = 0;  // 1 = show, 0 = hide

    addDivContent(contentDiv, tag, tags[tag]);

    return tagSection;
}
```

## <a name="display-the-search-results-in-the-webpage"></a>Visa sökresultatet på webbplatsen

Den `buildDiv()` fungera anrop den `addDivContent` funktionen för att skapa innehållet i varje tagg är komprimerbart `<div>`.

Ett tagginnehåll inkluderar JSON från taggsvaret. Första gången visas endast de första 100 tecknen i JSON, men du kan klicka på JSON-sträng för att visa alla JSON. Om du klickar på den igen minimeras JSON-strängen till 100 tecken.

Lägg sedan till de åtgärdstyper som hittades i taggen. Anropa lämpliga funktioner för att lägga till dess insikter för varje åtgärdstyp:

```javascript
function addDivContent(div, tag, json) {

    // Adds the first 100 characters of the json that contains
    // the tag's data. The user can click the text to show the
    // full json. They can click it again to collapse the json.
    var para = document.createElement('p');
    para.textContent = String(json).substr(0, 100) + '...';
    para.setAttribute('title', 'click to expand');
    para.setAttribute('style', 'cursor: pointer;')
    para.setAttribute('data-json', json);
    para.addEventListener('click', function(e) {
        var json = e.target.getAttribute('data-json');

        if (e.target.textContent.length <= 103) {  // 100 + '...'
            e.target.textContent = json;
            para.setAttribute('title', 'click to collapse');
        }
        else {
            para.textContent = String(json).substr(0, 100) + '...';
            para.setAttribute('title', 'click to expand');
        }
    });
    div.appendChild(para); 

    var parsedJson = JSON.parse(json);

    // Loop through all the actions in the tag and display them.
    for (var j = 0; j < parsedJson.actions.length; j++) {
        var action = parsedJson.actions[j];

        var subSectionDiv = document.createElement('div');
        subSectionDiv.setAttribute('class', 'subSection');
        div.appendChild(subSectionDiv);

        var h4 = document.createElement('h4');
        h4.innerHTML = action.actionType;
        subSectionDiv.appendChild(h4);

        if (action.actionType === 'ImageResults') {
            addImageWithWebSearchUrl(subSectionDiv, parsedJson.image, action);
        }
        else if (action.actionType === 'DocumentLevelSuggestions') {
            addRelatedSearches(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'RelatedSearches') {
            addRelatedSearches(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'PagesIncluding') {
            addPagesIncluding(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'VisualSearch') {
            addRelatedImages(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'Recipes') {
            addRecipes(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'ShoppingSources') {
            addShopping(subSectionDiv, action.data.offers);
        }
        else if (action.actionType === 'ProductVisualSearch') {
            addProducts(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'TextResults') {
            addTextResult(subSectionDiv, action);
        }
        else if (action.actionType === 'Entity') {
            addEntity(subSectionDiv, action);
        }
    }
}
```

## <a name="display-insights-for-different-actions"></a>Visar insikter för olika åtgärder

Följande funktioner visar insikter för olika åtgärder. Funktionerna ger antingen en klickbar bild eller en klickbar länk som dirigerar dig till en webbplats med mer information om bilden. Den här sidan hanteras antingen av Bing.com eller av bildens ursprungliga webbplats. Inte alla av insikternas data visas i det här programmet. Du hittar alla fält som är tillgängliga för en insikt i de [avbildningar - Visual Search](https://aka.ms/bingvisualsearchreferencedoc) referens.

> [!NOTE]
> Det finns en minsta mängd insiktsinformation som du måste visa på sidan. Se den [i Bing använder och visa kraven](../bing-web-search/use-display-requirements.md) mer.

### <a name="relatedimages-insights"></a>RelatedImages-insikter

Den `addRelatedImages()` funktionen skapar en rubrik för var och en av de webbplatser som är värd för den relaterade avbildningen genom att gå igenom listan över `RelatedImages` åtgärder och lägga till en `<img>` tagg utsidan `<div>` för var och en:

```javascript
    function addRelatedImages(div, images) {
        var length = (images.length > 10) ? 10 : images.length;

        // Set the title to the website that hosts the image. The title displays
        // when the user hovers over the image.

        // Make the image clickable. If the user clicks the image, they're taken
        // to the image in Bing.com.

        for (var j = 0; j < length; j++) {
            var img = document.createElement('img');
            img.setAttribute('src', images[j].thumbnailUrl + '&w=120&h=120');
            img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
            img.setAttribute('title', images[j].hostPageDisplayUrl);
            img.setAttribute('data-webSearchUrl', images[j].webSearchUrl)

            img.addEventListener('click', function(e) {
                var url = e.target.getAttribute('data-webSearchUrl');
                window.open(url, 'foo');
            })

            div.appendChild(img);
        }
    }
```

### <a name="pagesincluding-insights"></a>PagesIncluding-insikter

Den `addPagesIncluding()` funktionen skapar en länk för var och en av de webbplatser som är värd för den uppladdade bilden genom att gå igenom listan över `PagesIncluding` åtgärder och lägga till en `<img>` tagg utsidan `<div>` för var och en:

```javascript

    // Display links to the first 5 webpages that include the image.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addPagesIncluding(div, pages) {
        var length = (pages.length > 5) ? 5 : pages.length;

        for (var j = 0; j < length; j++) {
            var page = document.createElement('a');
            page.text = pages[j].name;
            page.setAttribute('href', pages[j].hostPageUrl);
            page.setAttribute('style', 'margin: 20px 20px 0 0');
            page.setAttribute('target', '_blank')
            div.appendChild(page);

            div.appendChild(document.createElement('br'));
        }
    }
```

### <a name="relatedsearches-insights"></a>RelatedSearches-insikter

Den `addRelatedSearches()` funktionen skapar en länk till webbplatsen som är värd för avbildningen, genom att gå igenom listan över `RelatedSearches` åtgärder och lägga till en `<img>` tagg utsidan `<div>` för var och en:

```javascript

    // Display the first 10 related searches. Include a link with the image
    // that when clicked, takes the user to Bing.com and displays the 
    // related search results.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addRelatedSearches(div, relatedSearches) {
        var length = (relatedSearches.length > 10) ? 10 : relatedSearches.length;

        for (var j = 0; j < length; j++) {
            var childDiv = document.createElement('div');
            childDiv.setAttribute('class', 'stackLink');
            div.appendChild(childDiv);

            var img = document.createElement('img');
            img.setAttribute('src', relatedSearches[j].thumbnail.url + '&w=120&h=120');
            img.setAttribute('style', 'margin: 20px 20px 0 0;');
            childDiv.appendChild(img);

            var relatedSearch = document.createElement('a');
            relatedSearch.text = relatedSearches[j].displayText;
            relatedSearch.setAttribute('href', relatedSearches[j].webSearchUrl);
            relatedSearch.setAttribute('target', '_blank');
            childDiv.appendChild(relatedSearch);

        }
    }
```

### <a name="recipes-insights"></a>Recipes-insikter

Den `addRecipes()` funktionen skapar en länk för var och en av recept returneras genom att gå igenom listan över `Recipes` åtgärder och lägga till en `<img>` tagg utsidan `<div>` för var och en:

```javascript
    // Display links to the first 10 recipes. Include the recipe's rating,
    // if available.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addRecipes(div, recipes) {
        var length = (recipes.length > 10) ? 10 : recipes.length;

        for (var j = 0; j < length; j++) {
            var para = document.createElement('p');

            var recipe = document.createElement('a');
            recipe.text = recipes[j].name;
            recipe.setAttribute('href', recipes[j].url);
            recipe.setAttribute('style', 'margin: 20px 20px 0 0');
            recipe.setAttribute('target', '_blank')
            para.appendChild(recipe);

            if (recipes[j].hasOwnProperty('aggregateRating')) {
                var span = document.createElement('span');
                span.textContent = 'rating: ' + recipes[j].aggregateRating.text;
                para.appendChild(span);
            }

            div.appendChild(para);
        }
    }
```

### <a name="shopping-insights"></a>Shopping-insikter

Den `addShopping()` funktionen skapar en länk för någon returnerade perioder resultat genom att gå igenom listan över `RelatedImages` åtgärder och lägga till en `<img>` tagg utsidan `<div>` för var och en:

```javascript
    // Display links for the first 10 shopping offers.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addShopping(div, offers) {
        var length = (offers.length > 10) ? 10 : offers.length;

        for (var j = 0; j < length; j++) {
            var para = document.createElement('p');

            var offer = document.createElement('a');
            offer.text = offers[j].name;
            offer.setAttribute('href', offers[j].url);
            offer.setAttribute('style', 'margin: 20px 20px 0 0');
            offer.setAttribute('target', '_blank')
            para.appendChild(offer);

            var span = document.createElement('span');
            span.textContent = 'by ' + offers[j].seller.name + ' | ' + offers[j].price + ' ' + offers[j].priceCurrency;
            para.appendChild(span);

            div.appendChild(para);
        }
    }
```

### <a name="products-insights"></a>Products-insikter

Den `addProducts()` funktionen skapar en länk för någon returnerade produkter resultat genom att gå igenom listan över `Products` åtgärder och lägga till en `<img>` tagg utsidan `<div>` för var och en:

```javascript

    // Display the first 10 related products. Display a clickable image of the
    // product that takes the user to Bing.com search results for the product.
    // If there are any offers associated with the product, provide links to the offers.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addProducts(div, products) {
        var length = (products.length > 10) ? 10 : products.length;

        for (var j = 0; j < length; j++) {
            var childDiv = document.createElement('div');
            childDiv.setAttribute('class', 'stackLink');
            div.appendChild(childDiv);

            var img = document.createElement('img');
            img.setAttribute('src', products[j].thumbnailUrl + '&w=120&h=120');
            img.setAttribute('title', products[j].name);
            img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
            img.setAttribute('data-webSearchUrl', products[j].webSearchUrl)
            img.addEventListener('click', function(e) {
                var url = e.target.getAttribute('data-webSearchUrl');
                window.open(url, 'foo');
            })
            childDiv.appendChild(img);

            if (products[j].insightsMetadata.hasOwnProperty('aggregateOffer')) {
                if (products[j].insightsMetadata.aggregateOffer.offerCount > 0) {
                    var offers = products[j].insightsMetadata.aggregateOffer.offers;

                    // Show all the offers. Not all markets provide links to offers.
                    for (var i = 0; i < offers.length; i++) {  
                        var para = document.createElement('p');

                        var offer = document.createElement('a');
                        offer.text = offers[i].name;
                        offer.setAttribute('href', offers[i].url);
                        offer.setAttribute('style', 'margin: 20px 20px 0 0');
                        offer.setAttribute('target', '_blank')
                        para.appendChild(offer);

                        var span = document.createElement('span');
                        span.textContent = 'by ' + offers[i].seller.name + ' | ' + offers[i].price + ' ' + offers[i].priceCurrency;
                        para.appendChild(span);

                        childDiv.appendChild(para);
                    }
                }
                else {  // Otherwise, just show the lowest price that Bing found.
                    var offer = products[j].insightsMetadata.aggregateOffer;

                    var para = document.createElement('p');
                    para.textContent = `${offer.name} | ${offer.lowPrice} ${offer.priceCurrency}`; 

                    childDiv.appendChild(para);
                }
            }
        }
    }
```

### <a name="textresult-insights"></a>TextResult-insikter

Den `addTextResult()` funktionen visar valfri text som identifierades i bilden:

```javascript

    function addTextResult(div, action) {
        var text = document.createElement('p');
        text.textContent = action.displayName;
        div.appendChild(text);
    }
```

Den `addEntity()` funktionen visas en länk som leder användaren till Bing.com där de kan få information om entitetstypen i bilden, om någon har identifierats:

```javascript
    // If the image is of a person, the tag might include an entity
    // action type. Display a link that takes the user to Bing.com
    // where they can get details about the entity.
    function addEntity(div, action) {
        var entity = document.createElement('a');
        entity.text = action.displayName;
        entity.setAttribute('href', action.webSearchUrl);
        entity.setAttribute('style', 'margin: 20px 20px 0 0');
        entity.setAttribute('target', '_blank');
        div.appendChild(entity);
    }
```

Den `addImageWithWebSearchUrl()` funktion visar en klickbar bild till den `<div>` som tar användaren till sökresultat på Bing.com:

```javascript
    function addImageWithWebSearchUrl(div, image, action) {
        var img = document.createElement('img');
        img.setAttribute('src', image.thumbnailUrl + '&w=120&h=120');
        img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
        img.setAttribute('data-webSearchUrl', action.webSearchUrl);
        img.addEventListener('click', function(e) {
            var url = e.target.getAttribute('data-webSearchUrl');
            window.open(url, 'foo');
        })
        div.appendChild(img);
    }

```

## <a name="add-a-css-style"></a>Lägga till ett CSS-format

Lägg till följande `<style>` avsnitt i den `<head>` taggen för att organisera layouten för webbsidan:

```html
        <style>

            .thumb {
                height: 75px;
                border: 1px solid #000;
            }

            .stackLink {
                width:180px;
                min-height:210px;
                display:inline-block;
            }
            .stackLink a {
                float:left;
                clear:left;
            }

            .section {
                float:left;
                display:none;
            }

            .subSection {
                clear:left;
                float:left;
            }

        </style>
```

## <a name="next-steps"></a>Nästa steg

>[!div class="nextstepaction"]
> [Självstudie: Hitta liknande bilder från tidigare sökningar med ImageInsightsToken](./tutorial-visual-search-insights-token.md)
