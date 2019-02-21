---
title: Skapa en klassificerare – Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Lär dig hur du skapar en modell för klassificering av avbildning med hjälp av Custom Vision-webbplats.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: anroth
ms.openlocfilehash: d91d62c387fc7bcaef8b7f2cb7e8d865c882aeed
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56445465"
---
# <a name="how-to-build-a-classifier-with-custom-vision"></a>Hur du skapar en klassificerare med anpassad visuellt innehåll

Om du vill använda Custom Vision Service för klassificering av avbildning måste du först skapa en klassificerare-modell. I den här guiden lär du dig att skapa en klassificerare via Custom Vision-webbplatsen.

## <a name="prerequisites"></a>Förutsättningar

- En giltig Azure-prenumeration. [Skapa ett konto](https://azure.microsoft.com/free/) utan kostnad.
- En uppsättning avbildningar som kan lära din klassificerare. Nedan finns tips om hur du väljer bilder.


## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Skapa anpassade Vision resurser i Azure Portal
Om du vill använda Custom Vision Service kommer du behöva skapa Custom Vision-utbildning och förutsägelser resurser i den i den [Azure-portalen](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision). Detta skapar både för träning och förutsägelser resurs. 

## <a name="create-a-new-project"></a>Skapa ett nytt projekt

I din webbläsare, navigerar du till den [Custom Vision-webbsida](https://customvision.ai) och välj __logga in__. Logga in med samma konto som du använde för att logga in på Azure Portal.

![Bild av sidan logga in](./media/browser-home.png)


1. För att skapa ditt första projekt, Välj **nytt projekt**. Den **Skapa nytt projekt** dialogrutan visas.

    ![Dialogrutan Nytt projekt har fält för namn, beskrivning och domäner.](./media/getting-started-build-a-classifier/new-project.png)

1. Ange ett namn och en beskrivning för projektet. Välj sedan en resursgrupp. Om ditt inloggade konto är associerad med ett Azure-konto, visas resursgrupp listrutan alla dina Azure-resursgrupper som innehåller en resurs för Custom Vision Service. 

> [!NOTE]
> Om det finns ingen resursgrupp, bekräftar du att du är inloggad på [customvision.ai](https://customvision.ai) med samma konto som du använde för att logga in på [Azure-portalen](https://portal.azure.com/). Bekräfta också att du har valt samma ”Directory” i Custom Vision-portalen som katalog i Azure-portalen var Custom Vision-resurserna finns. I båda platserna, kan du välja din katalog från nedrullningsbara meny i det övre högra hörnet på skärmen. 

1. Välj __klassificering__ under __projekttyper__. Sedan, under __Klassificeringstyper__, väljer du antingen **Multilabel** eller **Multiclass**, beroende på ditt användningsområde. Multilabel klassificering används valfritt antal taggarna för en avbildning (noll eller fler), medan multiklass-baserad klassificering sorterar bilder i enkel kategorier (varje bild som du skickar sorteras i taggen mest sannolika). Du kommer att kunna ändra klassificeringstypen senare om du vill.

1. Välj sedan en av de tillgängliga domänerna. Varje domän optimerar klassificerare för vissa typer av bilder, enligt beskrivningen i följande tabell. Du kommer att kunna ändra domänen senare om du vill.

    |Domain|Syfte|
    |---|---|
    |__Generisk__| Optimerat för en mängd olika aktiviteter för klassificering av avbildning. Om ingen av de andra domänerna är lämpligt, eller du är osäker på vilken domän som du väljer, väljer du den allmänna domänen. |
    |__Mat__|Optimerat för fotografier av disk som du ser dem på en restaurang-meny. Om du vill klassificera fotografier av enskilda frukter och grönsaker använda mat domänen.|
    |__Landmärken__|Optimerat för identifierbara landmärken, både naturliga och konstgjorda. Den här domänen fungerar bäst när landmärken syns tydligt i fotot. Den här domänen fungerar även om landmärken något hindras av personer framför den.|
    |__Detaljhandel__|Optimerat för avbildningar som finns i en i katalogen eller Shopping. Om du vill hög precision klassificera mellan klänningar, byxor och skjortor, Använd den här domänen.|
    |__Vuxen__|Optimerad för att definiera en bättre vuxet innehåll och inte är vuxen innehåll. Till exempel om du vill blockera bilder av personer i badning passar kan den här domänen du skapa en anpassad klassificerare för att göra detta.|
    |__Compact domäner__| Optimerat för begränsningar i realtid klassificeringen på mobila enheter. Modeller som genererats av compact domäner kan exporteras för att köras lokalt.|
    
1. Välj slutligen __skapa projekt__.

## <a name="choose-training-images"></a>Välj inlärningsbilder

Minst rekommenderar vi du använder minst 30 bilder per tagg i första träningsmängden. Du också vill samla in några extra bilder för att testa din modell när den har installerats.

Använda bilder med visual rad för att träna din modell effektivt. Välj avbildningar med som varierar:
* kameravinkel
* belysning
* Bakgrund
* visualiseringsformat
* person/grupperade subject(s)
* storlek
* typ

Kontrollera dessutom alla dina inlärningsbilder uppfylla följande kriterier:
* .bmp, .jpg eller .png-format
* större än 6MB i storlek (4MB för förutsägelse avbildningar)
* mindre än 256 bildpunkter på kortaste kant. alla avbildningar som är kortare än så kommer automatiskt skalas med Custom Vision Service

## <a name="upload-and-tag-images"></a>Ladda upp och tagga bilder

I det här avsnittet ska du ladda upp och tagga bilder för att träna klassificeraren manuellt. 

1. Om du vill lägga till bilder, klickar du på den __lägga till avbildningar__ knappen och välj sedan __Bläddra bland lokala filer__. Välj __öppna__ att flytta till Taggning. Valet av taggen tillämpas i hela gruppen med avbildningar som du har valt att ladda upp, så det är enklare att överföra avbildningar i separata grupper utifrån deras önskade taggar. Du kan också ändra taggar för enskilda bilder när de har överförts.

    ![Lägg till bilder kontrollen visas i det övre vänstra hörnet och som en knapp på längst ned i mitten.](./media/getting-started-build-a-classifier/add-images01.png)


1. Skapa en tagg genom att ange text i den __Mina taggar__ fältet och trycker på RETUR. Om taggen finns redan, visas den i en listruta. Du kan lägga till mer än en etikett till bilderna i ett multilabel projekt, men i ett inom projekt kan du lägga till endast en. Slutför överföra genom att använda den __ladda upp filer [antal]__ knappen. 

    ![Bild av sidan tagg och ladda upp](./media/getting-started-build-a-classifier/add-images03.png)

1. Välj __klar__ när de har överförts.

    ![Förloppsindikatorn visar alla aktiviteter har slutförts.](./media/getting-started-build-a-classifier/add-images04.png)

Tillbaka till början av det här avsnittet för att ladda upp en annan uppsättning bilder och upprepa steg. Vid en viss tidpunkt i projektet kan du behöva lägga till _negativt exempel_ för att göra din klassificerare mer exakta. Negativa prov är de som inte matchar någon av de andra taggarna. När du överför dessa avbildningar, gäller särskilda **negativt** etiketten på dem.

> [!NOTE]
> Custom Vision Service har stöd för vissa automatiska negativt bildhantering. Om du skapar en delvis kontra bananer klassificerare och skicka in en avbildning av en sko för förutsägelse bör klassificeraren exempelvis score avbildningen som nära 0% för både delvis och bananer.

> Å andra sidan i fall där negativt bilderna är bara en variant av avbildningarna som används i utbildning, är det troligt att modellen klassificera negativt avbildningar som en klass som är märkta på grund av bra likheterna. Till exempel om du har en orange kontra grapefrukter klassificerare och du flöde i en avbildning av en clementine, kan det bedöma clementine som ett orange eftersom många funktioner i clementine liknar apelsiner. Om dina negativt avbildningar är av den här typen kan vi rekommenderar att du skapar en eller flera ytterligare taggar (till exempel **andra**) och märka negativt bilder med den här taggen under utbildning för att tillåta modellen bättre skilja mellan dessa klasser .

## <a name="train-the-classifier"></a>Träna klassificeraren

För att träna klassificeraren, Välj den **träna** knappen. Klassificeraren använder alla aktuella avbildningar för att skapa en modell som identifierar de visuella egenskaperna för varje tagg.

![Knappen träna uppe till höger i verktygsfältet för webbsidans rubrik](./media/getting-started-build-a-classifier/train01.png)

Utbildning processen tar bara några minuter. Under tiden visas information om processen för utbildning i den **prestanda** fliken.

![Webbläsarfönstret utbildning dialogruta i avsnittet huvudsakliga](./media/getting-started-build-a-classifier/train02.png)

## <a name="evaluate-the-classifier"></a>Utvärdera klassificeraren

När utbildning har slutförts kan uppskattade modellens prestanda och visas. Custom Vision Service använder de avbildningar som du skickade för utbildning för att beräkna precision och kanske kommer ihåg, med hjälp av en process som kallas [k-vikning mellan verifiering](https://en.wikipedia.org/wiki/Cross-validation_(statistics)). Precision och återkallande är två olika mått av effektiviteten i en klassificerare:

- **Precision** anger andelen av identifierade klassificeringar som har rätt. Till exempel om modellen identifierat 100 bilder som hundar och 99 av dem har faktiskt av hundar, blir sedan precisionen 99%.
- **Kom ihåg** anger andelen av faktiska klassificeringar som har identifierats på rätt sätt. Till exempel om det fanns faktiskt 100 bilder äpplen och modellen identifierat 80 som äpplen, blir återkallelsen 80%.

![Utbildning resultatet visar övergripande precision och återkallande och precision och återkalla för varje tagg i klassificeraren.](./media/getting-started-build-a-classifier/train03.png)

### <a name="probability-threshold"></a>Sannolikhetströskelvärdet

Obs den **Sannolikhetströskelvärdet** skjutreglaget på den vänstra rutan i den **prestanda** fliken. Det här är tröskelvärdet för en förväntad sannolikheten för att anses vara korrekt vid beräkning av precision och återkallande.

Tolka förutsägande anrop med en tröskel för hög sannolikhet tenderar att returnera resultat med hög precision på bekostnad av återkallande (hittades klassificeringarna är korrekta, men många hittades inte). ett låga sannolikhetströskelvärdet har motsatsen (de flesta av de faktiska klassificeringarna hittades, men det finns falska positiva identifieringar i uppsättningen). Med detta i åtanke, bör du ange sannolikhetströskelvärdet enligt de specifika behoven i ditt projekt. Senare, på klientsidan kan bör du använda samma sannolikheten tröskelvärdet som ett filter när du tar emot förutsagda resultaten från modellen.

## <a name="manage-training-iterations"></a>Hantera iterationer av utbildning

Varje gång du lära din klassificerare kan du skapa en ny _iteration_ med sin egen uppdaterade prestandamått. Du kan visa alla dina iterationer i den vänstra rutan i den **prestanda** fliken. När du väljer en har möjlighet att göra det i _standard iteration_ genom att klicka på den **ange som standard** längst upp. Den _standard iteration_ är den modell som ska användas som standard när du frågar den via API: et för förutsägelse (från en app, till exempel). Om du väljer att inte uppdatera den _standard iteration_, du kan fortsätta att träna din modell utan att påverka din Apps aktuella beteende; och sedan, när du är nöjd med förbättrad modellen kan du uppdatera standardvärdet.

I den vänstra rutan finns också i **ta bort** knappen som du kan använda för att ta bort en iteration om den är föråldrad. När du tar bort en iteration kan du ta bort eventuella avbildningar som är unikt associerade med den.

## <a name="next-steps"></a>Nästa steg

I den här handboken beskrivs hur du skapar och träna en modell för klassificering av avbildning med hjälp av Custom Vision-webbplats. Nu ska få mer information om iterativ process för att förbättra din modell.

[Testa och träna om en modell](test-your-model.md)

