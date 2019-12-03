---
title: Hämta etiketter för data
titleSuffix: Azure Machine Learning
description: Den här artikeln visar hur du skapar och kör etiketter för projekt för att tagga data för Machine Learning.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: e66a9f8a775a46c906601ea08be52ca9dfbe0171
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689301"
---
# <a name="get-labels-for-data"></a>Hämta etiketter för data

Att märka Voluminous-data i Machine Learning-projekt är ofta ett problem. Projekt som har en dator vision komponent, till exempel bild klassificering eller objekt identifiering, kräver vanligt vis etiketter för tusentals avbildningar.
 
[Azure Machine Learning](https://ml.azure.com/) ger dig en central plats där du kan skapa, hantera och övervaka projekt med etiketter. Använd den för att koordinera data, etiketter och team medlemmar för att effektivt hantera etikett uppgifter. Machine Learning stöder bild klassificering, antingen flera etiketter eller flera klasser och objekt identifiering tillsammans med gränser rutor.

Machine Learning spårar förloppet och underhåller kön med ofullständiga etikett uppgifter. Etiketter behöver inte delta i ett Azure-konto. När de har autentiserats med Microsoft-konto eller [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)kan de göra så mycket etiketter som möjligt.

I Machine Learning startar och stoppar du projektet, lägger till och tar bort personer och team och övervakar förloppet. Du kan exportera märkta data i COCO-format eller som en Azure Machine Learning data uppsättning.

I den här artikeln får du lära dig att:

> [!div class="checklist"]
> * Skapa ett projekt
> * Ange projektets data och struktur
> * Hantera team och personer som arbetar med projektet
> * Köra och övervaka projektet
> * Exportera etiketterna

## <a name="prerequisites"></a>Krav

* De data som du vill märka, antingen i lokala filer eller i Azure Storage.
* Den uppsättning etiketter som du vill använda.
* Anvisningarna för att märka.
* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://aka.ms/AMLFree) innan du börjar.
* En Machine Learning-arbetsyta. Se [skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

## <a name="create-a-labeling-project"></a>Skapa ett etikett projekt

Att märka projekt administreras från Azure Machine Learning. Du kan använda sidan **Märk projekt** för att hantera dina projekt och personer. Ett projekt har ett eller flera team tilldelade till sig, och ett lag har tilldelats en eller flera personer.

Om dina data redan finns i Azure Blob Storage bör du göra det tillgängligt som ett data lager innan du skapar ett etikettande projekt. Mer information finns i [skapa och registrera data lager](https://docs.microsoft.com/azure/machine-learning/service/how-to-access-data#create-and-register-datastores).

Välj **Lägg till projekt**om du vill skapa ett projekt. Ge projektet ett lämpligt namn och välj **uppgifts typ för etiketter**.

![Guiden skapa etikett för projekt](media/how-to-create-labeling-projects/labeling-creation-wizard.png)

* Välj **bild klassificering med flera etiketter** för projekt om du vill tillämpa *en eller flera* etiketter från en uppsättning klasser i en bild. Till exempel kan ett foto av en hund vara märkt med både *hund* och *dagtid*.
* Välj **bild klassificering flera klasser** för projekt när du bara vill använda en *enda klass* från en uppsättning klasser till en avbildning.
* Välj **objekt identifiering (markerings ram)** för projekt när du vill tilldela en klass och en avgränsnings ruta till varje objekt i en bild.

Välj **Nästa** när du är redo att fortsätta.

## <a name="specify-the-data-to-label"></a>Ange de data som ska etiketteras

Om du redan har skapat en data uppsättning som innehåller dina data väljer du den från List rutan **Välj en befintlig data uppsättning** . Eller Välj **skapa en data uppsättning** för att använda ett befintligt Azure-datalager eller för att ladda upp lokala filer.

### <a name="create-a-dataset-from-an-azure-datastore"></a>Skapa en data uppsättning från ett Azure-datalager

I många fall är det bra att bara ladda upp lokala filer. Men [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) ger ett snabbare och mer robust sätt att överföra en stor mängd data. Vi rekommenderar Storage Explorer som standard sätt att flytta filer.

Så här skapar du en data uppsättning från data som du redan har lagrat i Azure Blob Storage:

1. Välj **skapa en data uppsättning** > **från data lagret**.
1. Tilldela ett **namn** till din data uppsättning.
1. Välj **fil** som **data uppsättnings typ**.  
1. Välj data lagret.
1. Om dina data finns i en undermapp i Blob Storage väljer du **Bläddra** för att välja sökvägen.
    * Lägg till "/* *" i sökvägen om du vill inkludera alla filer i undermappar för den valda sökvägen.
    * Lägg till "* */* . *" för att inkludera alla data i den aktuella behållaren och dess undermappar.
1. Ange en beskrivning för din data uppsättning.
1. Välj **Nästa**.
1. Bekräfta informationen. Välj **tillbaka** om du vill ändra inställningarna eller **skapa** för att skapa data uppsättningen.

### <a name="create-a-dataset-from-uploaded-data"></a>Skapa en data uppsättning från överförda data

Så här överför du dina data direkt:

1. Välj **skapa en data uppsättning** > **från lokala filer**.
1. Tilldela ett **namn** till din data uppsättning.
1. Välj "Arkiv" som **data uppsättnings typ**.
1. *Valfritt:* Välj **Avancerade inställningar** för att anpassa data lagret, behållaren och sökvägen till dina data.
1. Välj **Bläddra** för att välja de lokala filer som ska laddas upp.
1. Ange en beskrivning av din data uppsättning.
1. Välj **Nästa**.
1. Bekräfta informationen. Välj **tillbaka** om du vill ändra inställningarna eller **skapa** för att skapa data uppsättningen.

Data överförs till standard-BLOB-arkivet ("workspaceblobstore") på din Machine Learning-arbetsyta.

## <a name="specify-label-classes"></a>Ange etikett klasser

På sidan **etikett klasser** anger du uppsättningen klasser för att kategorisera dina data. Gör detta noggrant eftersom etiketternas precision och hastighet kommer att påverkas av deras möjlighet att välja bland klasserna. I stället för att bokstavera hela släktet och arterna för växter eller djur använder du exempelvis en fältkod eller förkorta släktet.

Ange en etikett per rad. Använd knappen **+** för att lägga till en ny rad. Om du har fler än 3 eller 4 etiketter men färre än 10 kanske du vill lägga till prefixet i namnen med siffror ("1:", "2:") så att etiketterna kan använda sig av siffer tangenterna för att påskynda sitt arbete.

## <a name="describe-the-labeling-task"></a>Beskriv etikettens uppgift

Det är viktigt att tydligt förklara etikettens uppgift. På sidan **etiketting-instruktioner** kan du lägga till en länk till en extern plats för etikett instruktioner. Se till att instruktionerna är orienterade och lämpligt för mål gruppen. Tänk på följande frågor:

* Vilka etiketter ser de ut och hur kommer de att välja mellan dem? Finns det en referens text att referera till?
* Vad ska de göra om ingen etikett verkar vara lämplig?
* Vad ska de göra om flera etiketter förefaller lämpliga?
* Vilket konfidens tröskelvärde bör gälla för en etikett? Vill du ha deras "bästa gissning" om de inte är säkra?
* Vad ska de göra med delvis Occluded eller överlappande objekt av intresse?
* Vad ska de göra om ett objekt av intresse klipps av bildens kant?
* Vad ska de göra när de skickar en etikett om de tror att de gör ett misstag?

För avgränsnings rutor är viktiga frågor:

* Hur definieras avgränsnings rutan för den här uppgiften? Ska det vara helt på insidan av objektet eller ska det vara på utsidan? Bör den beskäras så tätt som möjligt, eller är vissa godkännanden godtagbara?
* Vilken nivå av vård och konsekvens förväntar du dig att etiketterna ska tillämpas i definitions rutor?

>[!NOTE]
> Observera att etiketterna kan välja de första 9 etiketterna genom att använda siffer nycklar 1-9.

## <a name="initialize-the-labeling-project"></a>Initiera ett etikettande projekt

När du har initierat projektet är vissa delar av projektet oföränderliga. Du kan inte ändra aktivitets typen eller data uppsättningen. Du *kan* ändra etiketter och URL: en för uppgifts beskrivningen. Granska inställningarna noggrant innan du skapar projektet. När du har skickat projektet kommer du tillbaka till start sidan för **etiketten** som visar projektet som **initieras**. Den här sidan uppdateras inte automatiskt. Efter en paus uppdaterar du sidan manuellt för att se projektets status som **skapats**.

## <a name="manage-teams-and-people"></a>Hantera team och personer

Som standard får varje etikett projekt som du skapar ett nytt team med dig som medlem. Men team kan också delas mellan projekt. Och projekt kan ha mer än ett team. Om du vill skapa ett team väljer du **Lägg till Team** på sidan **team** .

Du hanterar personer på sidan **personer** . Lägg till och ta bort personer via e-postadress. Varje Labeler måste autentisera dig via din Microsoft-konto eller Azure Active Directory, om du använder den.  

När du har lagt till en person kan du tilldela den personen till ett eller flera team: gå till sidan **Teams** , Välj teamet och välj sedan **tilldela personer** eller **ta bort personer**.

Om du vill skicka ett e-postmeddelande till teamet väljer du teamet för att visa sidan med **team information** . På den här sidan väljer du **e-postteam** för att öppna ett e-postmeddelande med adresserna för alla i teamet.

## <a name="run-and-monitor-the-project"></a>Köra och övervaka projektet

När du har initierat projektet börjar Azure att köra det. Välj projektet på huvud sidan för **Etiketter** för att gå till **projekt information**. Fliken **instrument panel** visar förloppet för etikett uppgiften.

På fliken **data** kan du se din data uppsättning och granska etiketterade data. Om du ser felaktigt märkta data markerar du den och väljer **avvisa**, vilket tar bort etiketterna och sätter tillbaka dem i den omärkta kön.

Använd fliken **team** för att tilldela eller ta bort tilldelningen av team till projektet.

Om du vill pausa eller starta om projektet väljer du knappen **paus**/**Start** . Du kan bara märka data när projektet körs.

Du kan märka data direkt från sidan **projekt information** genom att välja **etikett data**.

## <a name="export-the-labels"></a>Exportera etiketterna

Du kan när som helst exportera etikett data för Machine Learning experimentering. Bild etiketter kan exporteras i [Coco-format](http://cocodataset.org/#format-data) eller som en Azure Machine Learning data uppsättning. Använd knappen **Exportera** på sidan **projekt information** i ditt projekt med etiketter.

COCO-filen skapas i standard-BLOB-arkivet för Azure Machine Learning arbets ytan i en mapp i *export-/Coco*. Du kan komma åt den exporterade Azure Machine Learning data uppsättningen i avsnittet **data uppsättningar** i Machine Learning. På sidan data uppsättnings information finns också exempel kod för att få åtkomst till dina etiketter från python.

![Exporterad data uppsättning](media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="next-steps"></a>Nästa steg

* Etikett bilder för [bild klassificering eller objekt identifiering](how-to-label-images.md)
* Läs mer om [Azure Machine Learning och Machine Learning Studio (klassisk)](../compare-azure-ml-to-studio-classic.md)
