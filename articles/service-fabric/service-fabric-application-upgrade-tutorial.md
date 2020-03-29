---
title: Självstudiekurs för uppgradering av Service Fabric-appen
description: Den här artikeln går igenom upplevelsen av att distribuera ett Service Fabric-program, ändra koden och distribuera en uppgradering med hjälp av Visual Studio.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: db814b972db1aee56be0858c9ff5d1c382640642
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464819"
---
# <a name="service-fabric-application-upgrade-tutorial-using-visual-studio"></a>Självstudiekurs för uppgradering av Programuppgradering av Service Fabric med Visual Studio
> [!div class="op_single_selector"]
> * [Powershell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Azure Service Fabric förenklar processen för att uppgradera molnprogram genom att se till att endast ändrade tjänster uppgraderas och att programhälsa övervakas under hela uppgraderingsprocessen. Det återställer också automatiskt tillbaka programmet till den tidigare versionen när du stöter på problem. Service Fabric-programuppgraderingar är *Zero Downtime*, eftersom programmet kan uppgraderas utan driftstopp. Den här självstudien beskriver hur du slutför en rullande uppgradering från Visual Studio.

## <a name="step-1-build-and-publish-the-visual-objects-sample"></a>Steg 1: Skapa och publicera exemplet visuella objekt
Hämta först visual [objects-programmet](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Actors/VisualObjects) från GitHub. Skapa och publicera sedan programmet genom att högerklicka på programprojektet **VisualObjects**och välja kommandot **Publicera** i menyalternativet Service Fabric.

![Snabbmeny för ett Service Fabric-program][image1]

Om du väljer **Publicera** visas en popup och du kan ange **målprofilen** till **PublishProfiles\Local.xml**. Fönstret ska se ut så här innan du klickar på **Publicera**.

![Publicera ett service fabric-program][image2]

Nu kan du klicka på **Publicera** i dialogrutan. Du kan använda [Service Fabric Explorer för att visa klustret och programmet](service-fabric-visualizing-your-cluster.md). Programmet Visuella objekt har en webbtjänst som [http://localhost:8081/visualobjects/](http://localhost:8081/visualobjects/) du kan gå till genom att skriva i webbläsarens adressfält.  Du bör se 10 flytande visuella objekt som rör sig på skärmen.

**OBS:** Om distribuera `Cloud.xml` till profil (Azure Service Fabric), programmet bör sedan vara tillgänglig på **http://{ServiceFabricName}.{ Region}.cloudapp.azure.com:8081/visualobjects/**. Kontrollera att du `8081/TCP` har konfigurerat i belastningsutjämnaren (hitta belastningsutjämnaren i samma resursgrupp som tjänst fabric-instansen).

## <a name="step-2-update-the-visual-objects-sample"></a>Steg 2: Uppdatera exemplet med visuella objekt
Du kanske märker att med den version som distribuerades i steg 1 roterar inte de visuella objekten. Låt oss uppgradera det här programmet till ett där de visuella objekten också roterar.

Välj Projektet VisualObjects.ActorService i VisualObjects-lösningen **VisualObjectActor.cs** och öppna VisualObjectActor.cs-filen. I filen går du `MoveObject`till metoden `visualObject.Move(false)`, kommenterar `visualObject.Move(true)`ut och avkommentar . Den här kodändringen roterar objekten när tjänsten har uppgraderats.  **Nu kan du bygga (inte bygga om) lösningen**, som bygger de ändrade projekten. Om du väljer *Återskapa alla*måste du uppdatera versionerna för alla projekt.

Vi måste också version vår ansökan. Om du vill göra versionsändringarna när du har högerklickat på **VisualObjects-projektet** kan du använda alternativet **Redigera manifestversioner** i Visual Studio. Om du väljer det här alternativet visas dialogrutan för versionsversioner på följande sätt:

![Dialogrutan Versionshantering][image3]

Uppdatera versionerna för de ändrade projekten och deras kodpaket, tillsammans med programmet till version 2.0.0. När ändringarna har gjorts ska manifestet se ut så här (fetstil visar ändringarna):

![Uppdatera versioner][image4]

Visual Studio-verktygen kan göra automatiska sammanslagningar av versioner när du väljer **Uppdatera program- och tjänstversioner automatiskt**. Om du använder [SemVer](http://www.semver.org)måste du uppdatera kod- och/eller konfigurationspaketversionen ensam om det alternativet är markerat.

Spara ändringarna och markera nu rutan **Uppgradera programmet.**

## <a name="step-3--upgrade-your-application"></a>Steg 3: Uppgradera ditt program
Bekanta dig med [programuppgraderingsparametrarna](service-fabric-application-upgrade-parameters.md) och [uppgraderingsprocessen](service-fabric-application-upgrade.md) för att få en god förståelse för de olika uppgraderingsparametrar, time-outs och hälsokriterium som kan tillämpas. För den här genomgången anges utvärderingsvillkoret för tjänsthälsan till standardläget (oövervakat läge). Du kan konfigurera dessa inställningar genom att välja **Konfigurera uppgraderingsinställningar** och sedan ändra parametrarna efter behov.

Nu är vi alla inställda på att starta programmet uppgraderingen genom att välja **Publicera**. Det här alternativet uppgraderar programmet till version 2.0.0, där objekten roterar. Service Fabric uppgraderar en uppdateringsdomän i taget (vissa objekt uppdateras först, följt av andra) och tjänsten förblir tillgänglig under uppgraderingen. Tillgång till tjänsten kan kontrolleras via din klient (webbläsare).  

Nu, när programuppgraderingen fortsätter, kan du övervaka den med Service Fabric Explorer, med hjälp av fliken **Pågående uppgraderingar** under programmen.

Om några minuter bör alla uppdateringsdomäner uppgraderas (slutförda) och utdatafönstret i Visual Studio bör också ange att uppgraderingen är slutförd. Och du bör upptäcka att *alla* visuella objekt i ditt webbläsarfönster nu roterar!

Du kanske vill prova att ändra versionerna och gå från version 2.0.0 till version 3.0.0 som en övning, eller till och med från version 2.0.0 tillbaka till version 1.0.0. Spela med time-outs och hälsopolicyer för att bekanta dig med dem. När du distribuerar till ett Azure-kluster i motsats till ett lokalt kluster kan de parametrar som används behöva skilja sig åt. Vi rekommenderar att du ställer in time-outs försiktigt.

## <a name="next-steps"></a>Nästa steg
[Om du uppgraderar programmet med PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) går du igenom en programuppgradering med PowerShell.

Styr hur ditt program uppgraderas med hjälp av [uppgraderingsparametrar](service-fabric-application-upgrade-parameters.md).

Gör dina programuppgraderingar kompatibla genom att lära dig hur du använder [data serialisering](service-fabric-application-upgrade-data-serialization.md).

Lär dig hur du använder avancerade funktioner när du uppgraderar programmet genom att referera till [avancerade ämnen](service-fabric-application-upgrade-advanced.md).

Åtgärda vanliga problem i programuppgraderingar genom att referera till stegen i [Felsökning av programuppgraderingar](service-fabric-application-upgrade-troubleshooting.md).

[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png
