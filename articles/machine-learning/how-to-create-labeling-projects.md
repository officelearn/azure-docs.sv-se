---
title: Skapa ett datamärkningsprojekt
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar och kör märkningsprojekt för att tagga data för maskininlärning.  Verktygen inkluderar ml assisterad märkning, eller människa i slingan märkning för att hjälpa till med uppgiften.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.topic: tutorial
ms.date: 03/01/2020
ms.openlocfilehash: f6723992ac3335e6abdd78f2008130dfe136f7df
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80873896"
---
# <a name="create-a-data-labeling-project-and-export-labels"></a>Skapa ett datamärkningsprojekt och exportetiketter 

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Märkning omfattande data i maskininlärningsprojekt är ofta en huvudvärk. Projekt som har en datorseendekomponent, till exempel bildklassificering eller objektidentifiering, kräver i allmänhet etiketter för tusentals bilder.
 
[Azure Machine Learning](https://ml.azure.com/) ger dig en central plats för att skapa, hantera och övervaka märkningsprojekt (offentlig förhandsversion). Använd den för att samordna data, etiketter och gruppmedlemmar för att effektivt hantera märkningsuppgifter. Machine Learning stöder bildklassificering, antingen multietikett eller multiklass, och objektidentifiering med avgränsade rutor.

Machine Learning spårar förloppet och upprätthåller kön med ofullständiga märkningsuppgifter. Labelers behöver inte ett Azure-konto för att delta. När de har autentiserats med ditt Microsoft-konto eller [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)kan de göra så mycket märkning som deras tid tillåter.

Du startar och stoppar projektet, lägger till och tar bort etiketter och team och övervakar märkningsförloppet. Du kan exportera märkta data i COCO-format eller som en Azure Machine Learning-datauppsättning.

> [!Important]
> Endast bildklassificerings- och objektidentifieringsetikettprojekt stöds för närvarande. Dessutom måste dataavbildningarna vara tillgängliga i ett Azure blob-datalager. (Om du inte har ett befintligt datalager kan du ladda upp bilder när du skapas.)

I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
> * Skapa ett projekt
> * Ange projektets data och struktur
> * Hantera team och personer som arbetar med projektet
> * Köra och övervaka projektet
> * Exportera etiketterna


## <a name="prerequisites"></a>Krav


* De data som du vill märka, antingen i lokala filer eller i Azure blob storage.
* Den uppsättning etiketter som du vill använda.
* Instruktionerna för märkning.
* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://aka.ms/AMLFree) konto innan du börjar.
* En arbetsyta för maskininlärning. Se [Skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

## <a name="create-a-labeling-project"></a>Skapa ett etikettprojekt

Märkningsprojekt administreras från Azure Machine Learning. Du kan använda sidan **Märkningsprojekt** för att hantera dina projekt och personer. Ett projekt har tilldelats en eller flera team och ett team har en eller flera personer som tilldelats det.

Om dina data redan finns i Azure Blob-lagring bör du göra dem tillgängliga som ett datalager innan du skapar etikettprojektet. Ett exempel på hur du använder ett datalager finns i [Självstudiekurs: Skapa ditt första bildklassificeringsmärkningsprojekt](tutorial-labeling.md).

Om du vill skapa ett projekt väljer du **Lägg till projekt**. Ge projektet ett lämpligt namn och välj **Märkningsaktivitetstyp**.

![Guiden För att skapa projekt](./media/how-to-create-labeling-projects/labeling-creation-wizard.png)


* Välj Flera klasser för **bildklassificering** för projekt när du bara vill använda en *enda klass* från en uppsättning klasser på en bild.
* Välj **Multietikett** för bildklassificering för projekt när du vill använda *en eller flera* etiketter från en uppsättning klasser på en bild. Till exempel kan ett foto av en hund vara märkt med både *hund* och *dagtid*.
* Välj **Objektidentifiering (markeringsram)** för projekt när du vill tilldela en klass och en markeringsram till varje objekt i en bild.

Välj **Nästa** när du är redo att fortsätta.

## <a name="specify-the-data-to-label"></a>Ange vilka data som ska etiketteras

Om du redan har skapat en datauppsättning som innehåller dina data markerar du den i listrutan **Välj en befintlig datauppsättning.** Du kan också välja **Skapa en datauppsättning** om du vill använda ett befintligt Azure-datacenter eller överföra lokala filer.


### <a name="create-a-dataset-from-an-azure-datastore"></a>Skapa en datauppsättning från ett Azure-datacenter

I många fall är det bra att bara ladda upp lokala filer. Men [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) är ett snabbare och mer robust sätt att överföra en stor mängd data. Vi rekommenderar Storage Explorer som standardsätt att flytta filer.

Så här skapar du en datauppsättning från data som du redan har lagrat i Azure Blob storage:

1. Välj **Skapa en datauppsättning** > **från datalager**.
1. Tilldela ett **namn** till datauppsättningen.
1. Välj **Arkiv** som **datauppsättningstyp**.  
1. Välj databutiken.
1. Om dina data finns i en undermapp i blob-lagringen väljer du **Bläddra** för att välja sökvägen.
    * Lägg till "/**" i sökvägen om du vill inkludera alla filer i undermappar till den markerade sökvägen.
    * Lägg till*/*"* .*" om du vill inkludera alla data i den aktuella behållaren och dess undermappar.
1. Ge en beskrivning av datauppsättningen.
1. Välj **Nästa**.
1. Bekräfta detaljerna. Välj **Tillbaka** om du vill ändra inställningarna eller **Skapa** om du vill skapa datauppsättningen.

> [!NOTE]
> De data du väljer läses in i projektet.  Om du lägger till mer data i datalagret visas inte i det här projektet när projektet har skapats.  

### <a name="create-a-dataset-from-uploaded-data"></a>Skapa en datauppsättning från överförda data

Så här laddar du upp dina data direkt:

1. Välj **Skapa en datauppsättning** > **från lokala filer**.
1. Tilldela ett **namn** till datauppsättningen.
1. Välj "Arkiv" som **datauppsättningstyp**.
1. *Valfritt:* Välj **Avancerade inställningar** för att anpassa datalager, behållare och sökväg till dina data.
1. Välj **Bläddra** för att välja de lokala filer som ska överföras.
1. Ge en beskrivning av din datauppsättning.
1. Välj **Nästa**.
1. Bekräfta detaljerna. Välj **Tillbaka** om du vill ändra inställningarna eller **Skapa** om du vill skapa datauppsättningen.

Data överförs till standardblob-arkivet ("workspaceblobstore") på arbetsytan Machine Learning.

## <a name="specify-label-classes"></a>Ange etikettklasser

På sidan **Etikettklasser** anger du den uppsättning klasser som ska kategoriseras. Gör detta noggrant, eftersom din märkningsnoggrannhet och hastighet kommer att påverkas av deras förmåga att välja bland klasserna. I stället för att stava ut hela släktet och arten för växter eller djur, använd till exempel en fältkod eller förkorta släktet.

Ange en etikett per rad. Använd **+** knappen för att lägga till en ny rad. Om du har fler än 3 eller 4 etiketter men färre än 10, kanske du vill prefix namnen med siffror ("1: ", "2: ") så att etiketterna kan använda sifferknapparna för att påskynda sitt arbete.

## <a name="describe-the-labeling-task"></a>Beskriv märkningsuppgiften

Det är viktigt att tydligt förklara märkningsuppgiften. På sidan **Etiketteringsinstruktioner** kan du lägga till en länk till en extern webbplats för märkningsinstruktioner eller ge instruktioner i redigeringsrutan på sidan. Håll instruktionerna uppgiftsorienterade och lämpliga för publiken. Tänk på följande frågor:

* Vilka etiketter ser de, och hur väljer de bland dem? Finns det en referenstext att hänvisa till?
* Vad ska de göra om ingen etikett verkar lämplig?
* Vad ska de göra om flera etiketter verkar lämpliga?
* Vilken konfidenströskel bör de gälla för en etikett? Vill du ha deras "bästa gissning" om de inte är säkra?
* Vad ska de göra med delvis ockluderade eller överlappande objekt av intresse?
* Vad ska de göra om ett objekt av intresse klipps av kanten av bilden?
* Vad ska de göra efter att de lämnat in en etikett om de tror att de gjort ett misstag?

För markeringsrutor är viktiga frågor:

* Hur definieras begränsningsramen för den här aktiviteten? Bör det vara helt på insidan av objektet, eller bör det vara på utsidan? Bör det beskäras så nära som möjligt, eller är något utrymme acceptabelt?
* Vilken nivå av omsorg och konsekvens förväntar du dig att märksiketterna ska tillämpa när de definierar markeringsrutor?
* Hur man märker objektet som delvis visas i bilden? 
* Hur man märker objektet som delvis omfattas av andra objekt?

>[!NOTE]
> Var noga med att notera att etiketterna kommer att kunna välja de första 9 etiketterna med hjälp av sifferknapparna 1-9.

## <a name="use-ml-assisted-labeling"></a>Använd ML-assisterad märkning

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Med **ml-sidan för assisterad märkning** kan du utlösa automatiska maskininlärningsmodeller för att påskynda märkningsuppgiften. I början av ditt märkningsprojekt blandas bilderna in i en slumpmässig ordning för att minska potentiella fördomar. Eventuella fördomar som finns i datauppsättningen kommer dock att återspeglas i den tränade modellen. Om till exempel 80 % av dina bilder tillhör en enda klass, kommer ungefär 80 % av de data som används för att träna modellen att tillhöra den klassen. Den här utbildningen omfattar inte aktivt lärande.

Den här funktionen är tillgänglig för bildklassificeringsuppgifter (multiklass- eller multietikett).  

Välj *Aktivera ML-assisterad märkning* och ange en GPU för att aktivera assisterad märkning, som består av två faser:
* Klustring
* Förmärkning

Det exakta antalet märkta bilder som krävs för att starta assisterad märkning är inte ett fast nummer.  Detta kan variera avsevärt från ett märkningsprojekt till ett annat. För vissa projekt, är ibland möjligt att se prelabel eller kluster uppgifter efter 300 bilder har märkts manuellt. ML Assisted Labeling använder en teknik som kallas *Transfer Learning*, som använder en förtränad modell för att få fart på träningsprocessen. Om dina datauppsättningsklasser liknar dem i den förtränade modellen kan företiketter vara tillgängliga efter bara några hundra manuellt märkta bilder. Om din datauppsättning skiljer sig avsevärt från de data som används för att för träna modellen kan det ta mycket längre tid.

Eftersom de slutliga etiketterna fortfarande är beroende av input från labeler, kallas denna teknik ibland *människa i loopetiketten.*

### <a name="clustering"></a>Klustring

När ett visst antal etiketter har skickats börjar maskininlärningsmodellen gruppera liknande bilder.  Dessa liknande bilder presenteras för märksskenarna på samma skärm för att snabba upp manuell taggning. Klustring är särskilt användbart när etikettsetiketten visar ett rutnät med 4, 6 eller 9 bilder. 

När en maskininlärningsmodell har tränats på dina manuellt märkta data trunkeras modellen till det senaste fullständigt anslutna lagret. Omärkta bilder skickas sedan genom den trunkerade modellen i en process som allmänt kallas "inbäddning" eller "featurization". Detta bäddar in varje bild i ett högdimensionellt utrymme som definieras av det här modelllagret. Bilder som är närmaste grannar i utrymmet används för klusteraktiviteter. 

### <a name="prelabeling"></a>Förmärkning

När fler bildetiketter har skickats används en klassificeringsmodell för att förutsäga bildtaggar.  Etikettsättet ser nu sidor som innehåller förväntade etiketter som redan finns på varje bild.  Uppgiften är sedan att granska dessa etiketter och korrigera eventuella felmärkta bilder innan du skickar sidan.  

När en maskininlärningsmodell har tränats på dina manuellt märkta data utvärderas modellen på en testuppsättning med manuellt märkta bilder för att fastställa dess noggrannhet vid en mängd olika konfidenströsklar. Den här utvärderingsprocessen används för att bestämma en konfidenströskel över vilken modellen är tillräckligt exakt för att visa företiketter. Modellen utvärderas sedan mot omärkta data. Bilder med förutsägelser som är mer säkra än det här tröskelvärdet används för förmärkning.

> [!NOTE]
> ML assisterad märkning är **endast** tillgänglig i Arbetsytor för Enterprise Edition.

## <a name="initialize-the-labeling-project"></a>Initiera märkningsprojektet

När märkningsprojektet har initierats är vissa aspekter av projektet oföränderliga. Du kan inte ändra aktivitetstypen eller datauppsättningen. Du *kan* ändra etiketter och URL:en för uppgiftsbeskrivningen. Granska inställningarna noggrant innan du skapar projektet. När du har skickat in projektet returneras du till startsidan för **datamärkning,** som visar projektet som **initierande**. Den här sidan uppdateras inte automatiskt. Så, efter en paus, manuellt uppdatera sidan för att se projektets status som **Skapad**.

## <a name="manage-teams-and-people"></a>Hantera team och personer

Som standard får varje märkningsprojekt som du skapar ett nytt team med dig som medlem. Men team kan också delas mellan projekt. Och projekt kan ha mer än ett team. Om du vill skapa ett team väljer du **Lägg till team** på sidan **Teams.** 

Du hanterar personer på sidan **Labelers.** Lägg till och ta bort personer via e-postadress. Varje etikett måste autentisera via ditt Microsoft-konto eller Azure Active Directory, om du använder den.  

När du har lagt till en person kan du tilldela den personen till ett eller flera team: Gå till **teams-sidan,** välj teamet och välj sedan Tilldela **personer** eller Ta bort **personer**.

Om du vill skicka ett e-postmeddelande till teamet väljer du det team som vill visa sidan **Gruppinformation.** På den här sidan väljer du **E-postteam** för att öppna ett e-postutkast med adresserna till alla i teamet.

## <a name="run-and-monitor-the-project"></a>Köra och övervaka projektet

När du har initierat projektet börjar Azure köra det. Välj projektet på huvudsidan **för dataetikett** om du vill gå till **Projektinformation**. Fliken **Instrumentpanel** visar förloppet för märkningsuppgiften.

På fliken **Data** kan du se din datauppsättning och granska märkta data. Om du ser felaktigt märkta data markerar du dem och väljer **Avvisa**, som tar bort etiketterna och placerar tillbaka data i den omärkta kön.

Använd fliken **Team** om du vill tilldela eller ta bort team till projektet.

Om du vill pausa eller starta om projektet väljer du knappen **Pausa**/**Start.** Du kan bara märka data när projektet körs.

Du kan märka data direkt från sidan **Projektinformation** genom att välja **Etikettdata**.

## <a name="add-new-label-class-to-a-project"></a>Lägga till ny etikettklass i ett projekt

Under märkningsprocessen kan det hända att ytterligare etiketter behövs för att klassificera dina bilder.  Du kanske till exempel vill lägga till en etikett med "Okänd" eller "Annan" för att visa förvirrande bilder.

Så här lägger du till en eller flera etiketter i ett projekt:

1. Markera projektet på huvudsidan **för dataetikett.**
1. Högst upp på sidan väljer du **Paus** för att stoppa etiketter från deras aktivitet.
1. Välj fliken **Information**.
1. Välj **Etikettklasser**i listan till vänster .
1. Högst upp i listan väljer du **+ Lägg till etiketter** ![Lägg till en etikett](media/how-to-create-labeling-projects/add-label.png)
1. Lägg till den nya etiketten i formuläret och välj hur du vill gå vidare.  Eftersom du har ändrat de tillgängliga etiketterna för en bild väljer du hur du vill behandla redan märkta data:
    * Börja om och ta bort alla befintliga etiketter.  Välj det här alternativet om du vill börja märka från början med den nya fullständiga uppsättningen etiketter. 
    * Börja om och behåll alla befintliga etiketter.  Välj det här alternativet om du vill markera alla data som omärkta, men behåll de befintliga etiketterna som standardtagg för bilder som tidigare har märkts.
    * Fortsätt, behålla alla befintliga etiketter. Välj det här alternativet om du vill behålla alla data som redan är märkta som de är och börja använda den nya etiketten för data som ännu inte är märkta.
1. Ändra instruktionerna sidan om det behövs för den nya etiketten (s).
1. När du har lagt till alla nya etiketter väljer du **Start** högst upp på sidan för att starta om projektet.  

## <a name="export-the-labels"></a>Exportera etiketterna

Du kan exportera etikettdata för Machine Learning-experiment när som helst. Bildetiketter kan exporteras i [COCO-format](http://cocodataset.org/#format-data) eller som en Azure Machine Learning-datauppsättning. Använd knappen **Exportera** på sidan **Projektinformation** i ditt etikettprojekt.

COCO-filen skapas i standardblob-arkivet för Arbetsytan Azure Machine Learning i en mapp i *export/coco*. Du kan komma åt den exporterade Azure Machine Learning-datauppsättningen i avsnittet **Datauppsättningar** i Machine Learning. Informationssidan för datauppsättningen innehåller också exempelkod för att komma åt etiketterna från Python.

![Exporterad datauppsättning](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="next-steps"></a>Nästa steg

* [Självstudiekurs: Skapa ditt första bildklassificeringsmärkningsprojekt](tutorial-labeling.md).
* Etikettbilder för [bildklassificering eller objektidentifiering](how-to-label-images.md)
* Läs mer om [Azure Machine Learning och Machine Learning Studio (klassisk)](compare-azure-ml-to-studio-classic.md)
