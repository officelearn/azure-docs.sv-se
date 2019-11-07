---
title: Hämta etiketter för data
titleSuffix: Azure Machine Learning
description: Den här artikeln visar hur du skapar och kör etiketter för projekt för att tagga data för Machine Learning.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 76f995901814c90ff9fd78585c98d56b3478e8b4
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73612758"
---
# <a name="get-labels-for-data"></a>Hämta etiketter för data

Att märka stora mängder data har ofta varit ett problem i Machine Learning-projekt. ML-projekt med en dator vision, till exempel bild klassificering eller objekt identifiering, kräver vanligt vis tusentals avbildningar och motsvarande etiketter. 
 
Azure Machine Learning Studio ger dig en central plats för att skapa, hantera och övervaka projekt med etiketter. Genom att namnge projekt kan du koordinera data, etiketter och grupp medlemmar, så att du effektivt kan hantera etikett uppgifter. Aktiviteter som stöds för närvarande är bild klassificering, antingen flera etiketter eller flera klasser och objekt identifiering med hjälp av gränser rutor.

Azure spårar förlopp och underhåller kön med ofullständiga etikett uppgifter. Etiketter kräver inget Azure-konto för att delta. När de har autentiserats med sitt Microsoft-konto (MSA) eller [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)kan de göra så mycket eller bara små etiketter som de tillåter. De kan tilldela och ändra etiketter med kortkommandon. 

Du kan starta och stoppa projektet, lägga till och ta bort personer och team och övervaka förloppet. Du kan exportera märkta data i antingen COCO-format eller som en Azure ML-datauppsättning. 

I den här artikeln får du lära dig att:

> [!div class="checklist"]
> * Skapa ett projekt
> * Ange projektets data och struktur
> * Hantera de team och personer som arbetar med projektet
> * Köra och övervaka projektet
> * Exportera etiketterna 

## <a name="prerequisites"></a>Nödvändiga komponenter

* De data som du vill märka, antingen i lokala filer eller redan i Azure Storage
* Den uppsättning etiketter som du vill använda
* Instruktioner för etikettering
* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag
* En Azure Machine Learning-arbetsyta. Se [skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

## <a name="create-a-labeling-project"></a>Skapa ett etikett projekt

Att märka projekt administreras från [Azure Machine Learning Studio](https://ml.azure.com/). På sidan **Märk projekt** kan du hantera dina projekt, team och personer. Ett projekt har ett eller flera team tilldelade till sig, och ett lag har tilldelats en eller flera personer. 

Om dina data redan är lagrade i Azure Blob Storage bör du göra dem tillgängliga som ett data lager innan du skapar ditt etikett-projekt. Mer information finns i [skapa och registrera data lager](https://docs.microsoft.com/azure/machine-learning/service/how-to-access-data#create-and-register-datastores). 

Välj **Lägg till projekt**om du vill skapa ett projekt. Ge den ett lämpligt namn och välj **uppgifts typ**. 

![Guiden skapa etikett för projekt](media/how-to-create-labeling-projects/labeling-creation-wizard.png)

* Välj **bild klassificering med flera etiketter** för projekt där **en _eller flera_**  etiketter från en uppsättning klasser kan tillämpas på en bild. Till exempel kan ett foto av en hund märkas med både *hund* och *dagtid*
* Välj **bild klassificering flera klasser** för projekt där endast en **enskild klass** från en uppsättning klasser kan tillämpas på en avbildning
* Välj **objekt identifiering (markerings ram)** för projekt där uppgiften ska båda tilldela en klass till ett objekt i en bild och för att ange en avgränsnings ruta runt objektet

Välj **Nästa** när du är redo att gå vidare.

## <a name="specify-data-to-be-labeled"></a>Ange data som ska märkas

Om du redan har skapat en data uppsättning som innehåller dina data kan du välja den från List rutan **Välj en befintlig data mängd** . Alternativt kan du välja **skapa en data uppsättning** för att antingen välja ett befintligt Azure-datalager eller ladda upp lokala filer. 

### <a name="create-a-dataset-from-an-azure-datastore"></a>Skapa en data uppsättning från ett Azure-datalager

Även om det är bra att välja direkt överföring av lokala filer är det bra att [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) både stabilt och snabbare för överföring av stora mängder data. Vi rekommenderar Azure Storage Explorer som standard sätt att flytta filer.

Så här skapar du en data uppsättning från data som du redan har lagrat i Azure Blob Storage:

1. Välj **skapa en data uppsättning** och **från data lager**
1. Tilldela ett **namn** för din data uppsättning
1. Du måste välja "Arkiv" som **data uppsättnings typ**  
1. Välj data lager 
1. Om dina data finns i en undermapp i Blob Storage väljer du **Bläddra** för att välja sökvägen. 
    * Du kan lägga till `/**` efter sökvägen för att inkludera alla filer i undermappar för den valda sökvägen
    * Använd `**/*.*` om du vill inkludera alla data i den aktuella behållaren och undermapparna
1. Ange en beskrivning av din data uppsättning
1. Välj **Nästa** 
1. Bekräfta informationen. Du kan välja **tillbaka** om du vill ändra inställningarna eller välja **skapa** för att skapa data uppsättningen

### <a name="create-a-dataset-by-uploading-data"></a>Skapa en data uppsättning genom att ladda upp data

Om du vill överföra dina data direkt:

1. Välj **skapa en data uppsättning** och **från lokala filer**
1. Tilldela ett **namn** för din data uppsättning
1. Du måste välja "Arkiv" som **data uppsättnings typ**
1. Om du väljer **Avancerade inställningar**kan du anpassa data lagret, behållaren och sökvägen till dina data
1. Välj **Bläddra** för att välja lokala filer för uppladdning
1. Ange en beskrivning av din data uppsättning
1. Välj **Nästa** 
1. Bekräfta informationen. Du kan välja **tillbaka** om du vill ändra inställningarna eller välja **skapa** för att skapa data uppsättningen

Data laddas upp till standard-BLOB-arkivet (`workspaceblobstore`) för din Azure ML-arbetsyta.

## <a name="specify-label-classes"></a>Ange etikett klasser

På sidan **etikett klasser** anger du den uppsättning klasser som används för att kategorisera dina data. Tänk på dessa klasser, eftersom dina etiketter stämmer överens med hur snabbt de kan välja mellan dem. I stället för att bokstavera hela släktet och arterna för växter eller djur kan det vara bättre att använda fält koder eller förkorta släktet. 

Ange en etikett per rad med knappen **+** för att lägga till en ny rad. Om du har fler än 3 eller 4 etiketter, men färre än 10, bör du överväga att åtgärda dem med "1:", "2:" osv. för att ge en vägledning till etiketter med hjälp av nummer nycklarna för att påskynda sitt arbete. 

## <a name="describe-the-labeling-task"></a>Beskriv etikettens uppgift

En tydlig förklaring av att märka uppgiften är viktigt. På sidan **etiketting-instruktioner** kan du länka till en extern webbplats för instruktioner som visas för etiketter. Se till att instruktionerna är orienterade och lämpligt för mål gruppen. 

* Vilka etiketter ser de ut och hur kommer de att välja mellan dem? Finns det en referens text som de ska referera till?
* Vad ska de göra om ingen etikett verkar vara lämplig? 
* Vad ska de göra om flera etiketter förefaller lämpliga?
* Vilket konfidens tröskelvärde bör gälla för en etikett? Vill du ha deras "bästa gissning" om de inte är säkra?
* Vad ska de göra med delvis Occluded eller överlappande objekt av intresse?
* Vad ska de göra om ett objekt av intresse klipps av bildens kant?
* Vad ska de göra om de tycker att de har gjort ett misstag med en bild när de har skickat in dem? 

Med avgränsnings rutor är andra viktiga frågor:

* Hur definieras avgränsnings rutan för den här uppgiften? Ska det vara helt intill objektet, beskurna så nära som möjligt, eller så är det en del av den godtagbara mängden utrymme? 
* Vilken nivå av omsorg och konsekvens förväntar du dig att Labeler ska gälla för att definiera markerings rutor?

>[!Note]
> Tänk på att Labeler kan välja bland de första 9 etiketterna med siffer nycklar 1-9. 

## <a name="initialize-the-labeling-project"></a>Initiera ett etikettande projekt

När du har initierat är vissa delar av etiketts projektet oföränderliga: du kan inte ändra aktivitets typen eller data uppsättningen. Du kan ändra etiketter och du kan ändra URL: en för aktivitets beskrivningen. Granska inställningarna noga innan du skapar projektet. När du har skickat projektet kommer du tillbaka till start sidan för **etiketten** som visar projektet som **initieras**. Den här sidan uppdateras inte automatiskt, så efter en viss tid uppdaterar den manuellt projektet när det **skapas**. 

## <a name="manage-teams-and-people"></a>Hantera team och personer

Ett etikett projekt får ett standard team och lägger till dig som standard medlem. Varje etikettande projekt får ett nytt standard team, men team kan delas mellan projekt. Projekt kan ha fler än ett team. Skapa ett team genom att välja **Lägg till Team** på sidan **team** . 

Personer hanteras på sidan **personer** . Du kan lägga till och ta bort personer som är inloggade på deras e-postadress. Varje Labeler måste autentiseras med antingen ett Microsoft-konto eller Azure Active Directory om du använder det.  

När du har lagt till en person kan du tilldela dem till ett eller flera team. Gå till sidan **team** , Välj det team som du är intresse rad av och Använd sedan **tilldela personer** eller **ta bort personer** som du vill.

Om du vill skicka ett e-postmeddelande till alla i teamet kan du göra det genom att välja teamet för att öppna sidan med **team information** . På den här sidan öppnar knappen **e-postteam** e-postredigeraren med adresserna för alla i teamet.

## <a name="run-and-monitor-the-project"></a>Köra och övervaka projektet

När projektet har initierats börjar Azure att köra det. Om du klickar på projektet på huvud sidan för **Etiketter** visas **projekt information**. Fliken **instrument panel** visar hur du gör för att märka uppgiften. 

På fliken **data** kan du titta på din data uppsättning och granska etiketterade data. Om du ser felaktigt märkta data kan du **välja** den och välja **avvisa**, vilket tar bort etiketterna och sätter tillbaka dem i den omärkta kön. 

På fliken **team** kan du tilldela eller ta bort tilldelning av team till det här projektet. 

Om du vill göra projektet offline eller online väljer du knappen **paus**/**Start** , som växlar körnings status för projektet.

Du kan märka data direkt från sidan **projekt information** genom att välja **etikett data**. Du får bara märka data när projektet körs. 

## <a name="export-the-labels"></a>Exportera etiketterna

Du kan när som helst exportera etikett data för Machine Learning-experimentering. Bild etiketter kan exporteras i [Coco-format](http://cocodataset.org/#format-data) eller som en Azure ml-datauppsättning. Du hittar knappen **Exportera** på sidan **projekt information** i ditt projekt med etiketter.

COCO-filen skapas i standard-BLOB-arkivet för Azure ML-arbetsytan i en mapp i **export-/Coco**. Du kan komma åt den exporterade Azure ML-datauppsättningen under avsnittet **data uppsättningar** i Studio. Information om data uppsättnings sidan innehåller också exempel kod för att komma åt dina etiketter från python.

![Exporterad data uppsättning](media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="next-steps"></a>Nästa steg

* Etikett bilder för [bild klassificering eller objekt identifiering](how-to-label-images.md)
* Läs mer om [Azure Machine Learning och Studio](../compare-azure-ml-to-studio-classic.md)