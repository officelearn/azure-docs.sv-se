<properties
    pageTitle="Webb- och arbetsroller för Python med Python Tools 2.2 för Visual Studio | Microsoft Azure"
    description="Översikt över hur du använder Python Tools för Visual Studio för att skapa Azure-molntjänster, inklusive webb- och arbetsroller."
    services="cloud-services"
    documentationCenter="python"
    authors="thraka"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="hero-article"
    ms.date="08/30/2015"
    ms.author="adegeo"/>




# Webb- och arbetsroller för Python med Python Tools 2.2 för Visual Studio

Den här artikeln innehåller en översikt över hur du använder webb- och arbetsroller för Python med hjälp av [Python Tools för Visual Studio][].

## Krav

 - Visual Studio 2013 eller 2015
 - [Python Tools 2.2 för Visual Studio][] (PTVS)
 - [Azure SDK-verktyg för VS 2013][] eller [Azure SDK-verktyg för VS 2015][]
 - [Python 2.7 32-bitars][] eller [Python 3.4 32-bitars][]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Vad är webb- och arbetsroller för Python?

Azure tillhandahåller tre beräkningsmodeller för program som körs: [funktionen Web Apps i Azure App Service][körningsmodell för webbplatser], [Azure Virtual Machines][körningsmodell för virtuella datorer] och [Azure Cloud Services][körningsmodell för molntjänster]. Alla tre modeller stöder Python. Cloud Services, där webb- och arbetsroller ingår, tillhandahåller *plattform som en tjänst (PaaS)*. I en molntjänst tillhandahåller en webbroll en dedikerad IIS-webbserver (Internet Information Services) som fungerar som värd för frontend-webbprogram, medan en arbetsroll kan köra asynkrona, tidskrävande eller beständiga uppgifter oberoende av användarinteraktion eller indata.

Mer information finns i [Vad är en molntjänst?].

> [AZURE.NOTE] *Vill du skapa en enkel webbplats?*
Om ditt scenario bara är en enkel webbplats bör du överväga att använda den förenklade funktionen Web Apps i Azure App Service. Det är lätt att uppgradera till en molntjänst efter hand som webbplatsen växer och dina behov förändras. På <a href="/develop/python/">Python Developer Center</a> hittar du artiklar om utvecklingen av funktionen Web Apps i Azure App Service.
<br />


## Skapa projekt

I Visual Studio kan du välja **Azure Cloud Service** i dialogrutan **Nytt projekt** under **Python**.

![Dialogrutan Nytt projekt](./media/cloud-services-python-ptvs/new-project-cloud-service.png)

Du kan skapa nya webb- och arbetsroller i Azure Cloud Services-guiden.

![Dialogrutan för Azure Cloud Services](./media/cloud-services-python-ptvs/new-service-wizard.png)

Mallen för arbetsroller innehåller formaterad exempelkod för anslutning till ett Azure-lagringskonto eller till Azure Service Bus.

![Molntjänstlösning](./media/cloud-services-python-ptvs/worker.png)

Du kan lägga till webb- eller arbetsroller till en befintlig molntjänst när som helst.  Du kan välja att lägga till befintliga projekt i din lösning eller skapa nya.

![Kommandot Lägg till roll](./media/cloud-services-python-ptvs/add-new-or-existing-role.png)

Din molntjänst kan innehålla roller som implementeras på olika språk.  Exempelvis kan en Python-webbroll implementeras med hjälp av Django, med Python, eller med C#-arbetsroller.  Du kan enkelt kommunicera mellan dina roller med hjälp av Service Bus-köer eller lagringsköer.

## Lokal körning

Om du definierar ditt molntjänstprojekt som startprojektet och trycker på F5 körs molntjänsten i den lokala Azure-emulatorn.

Även om PTVS har stöd för att starta i emulatorn så går det inte att felsöka (till exempel brytpunkter).

Om du vill felsöka dina webb- och arbetsroller kan du konfigurera rollprojektet som startprojektet och felsöka det i stället.  Du kan också ange flera startprojekt.  Högerklicka på lösningen och välj **Ange startprojekt**.

![Egenskaper för lösningens startprojekt](./media/cloud-services-python-ptvs/startup.png)

## Publicera till Azure

När du vill publicera högerklickar du på molntjänstprojektet i lösningen och väljer **Publicera**.

![Inloggning för publicering i Microsoft Azure](./media/cloud-services-python-ptvs/publish-sign-in.png)

Välj den molntjänst som du vill publicera till på inställningssidan.

![Publiceringsinställningar i Microsoft Azure](./media/cloud-services-python-ptvs/publish-settings.png)

Du kan skapa en ny molntjänst om du inte redan har en tillgänglig.

![Dialogrutan Skapa molntjänst](./media/cloud-services-python-ptvs/publish-create-cloud-service.png)

Det är också bra att aktivera fjärrskrivbordsanslutningar till datorn eller datorerna för felsökningsändamål.

![Dialogrutan Konfiguration av fjärrskrivbord](./media/cloud-services-python-ptvs/publish-remote-desktop-configuration.png)

När du har konfigurerat inställningarna klickar du på **Publicera**.

Förloppet visas i utdatafönstret. Därefter visas fönstret med Microsoft Azure-aktivitetsloggen.

![Fönstret med Microsoft Azure-aktivitetsloggen](./media/cloud-services-python-ptvs/publish-activity-log.png)

Distributionen tar flera minuter. När den är klar körs dina webb- och/eller arbetsroller i Azure!

## Nästa steg

Mer detaljerad information om hur du arbetar med webb- och arbetsroller i Python Tools för Visual Studio finns i dokumentationen till PTVS:

- [Molntjänstprojekt][]

Mer information om hur du använder Azure-tjänster från dina webb- och arbetsroller, t.ex. Azure Storage eller Azure Service Bus, finns i följande artiklar.

- [Blob-tjänst][]
- [Tabelltjänst][]
- [Kötjänst][]
- [Service Bus-köer][]
- [Avsnitt om Service Bus][]


<!--Link references-->

[Vad är en molntjänst?]: ./cloud-services/cloud-services-choose-me.md
[körningsmodell för webbplatser]: ./app-service-web/app-service-web-overview.md
[körningsmodell för virtuella datorer]: ./virtual-machines/virtual-machines-windows-about.md
[körningsmodell för molntjänster]: ./cloud-services/cloud-services-choose-me.md
[Python Developer Center]: /develop/python/

[Blob-tjänst]: ./storage/storage-python-how-to-use-blob-storage.md
[Kötjänst]: ./storage/storage-python-how-to-use-queue-storage.md
[Tabelltjänst]: ./storage/storage-python-how-to-use-table-storage.md
[Service Bus-köer]: ./service-bus/service-bus-python-how-to-use-queues.md
[Avsnitt om Service Bus]: ./service-bus/service-bus-python-how-to-use-topics-subscriptions.md


<!--External Link references-->

[Python Tools för Visual Studio]: http://aka.ms/ptvs
[Dokumentation om Python Tools för Visual Studio]: http://aka.ms/ptvsdocs
[Molntjänstprojekt]: http://go.microsoft.com/fwlink/?LinkId=624028
[Python Tools 2.2 för Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Azure SDK-verktyg för VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK-verktyg för VS 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 32-bitars]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python 3.4 32-bitars]: http://go.microsoft.com/fwlink/?LinkId=517191



<!--HONumber=Jun16_HO2-->


