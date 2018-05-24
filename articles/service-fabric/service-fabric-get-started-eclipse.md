---
title: Azure Service Fabric-plugin-program för Eclipse | Microsoft Docs
description: Kom igång med Service Fabric-plugin-programmet för Eclipse.
services: service-fabric
documentationcenter: java
author: rapatchi
manager: timlt
editor: ''
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/06/2018
ms.author: rapatchi
ms.openlocfilehash: f0c97fdec07a0b882c1c67d4278a0c3fb7b1a5cd
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207401"
---
# <a name="service-fabric-plug-in-for-eclipse-java-application-development"></a>Service Fabric-plugin-program för utveckling av Java-program i Eclipse
Eclipse är en av de mest använda IDE:erna (Integrated Development Environment) för Java-utvecklare. I den här artikeln beskrivs hur du kan konfigurera din Eclipse-utvecklingsmiljö för att arbeta med Azure Service Fabric. Läs om hur du installerar Service Fabric-plugin-programmet, skapar ett Service Fabric-program och distribuerar Service Fabric-programmet till ett lokalt eller fjärranslutet Service Fabric-kluster i Eclipse. 

> [!NOTE]
> Eclipse-pluginprogrammet stöds för närvarande inte av Windows. 

## <a name="install-or-update-the-service-fabric-plug-in-in-eclipse"></a>Installera eller uppdatera Service Fabric-plugin-programmet i Eclipse
Du kan installera ett Service Fabric-plugin-program i Eclipse. Plugin-programmet gör det enklare att skapa och distribuera Java-tjänster.

> [!IMPORTANT]
> Service Fabric plugin-programmet kräver Eclipse Neon eller en senare version. Efter den här anteckningen följer instruktioner om hur du kontrollerar din Eclipse-version. Om du har en tidigare version av Eclipse installerad kan du hämta nyare versioner från [Eclipse-webbplatsen](https://www.eclipse.org). Vi rekommenderar inte att du installerar en ny version av Eclipse ovanpå en befintlig version (skriver över). Du kan ta bort den gamla versionen innan du kör installationsprogrammet, eller så kan du installera den nya versionen i en annan katalog. 
> 
> I Ubuntu rekommenderar vi att du installerar direkt från Eclipses webbplats i stället för att använda ett installationspaket (`apt` eller `apt-get`). På så sätt kan du vara säker på att du får den senaste versionen av Eclipse. 

Installera Eclipse Neon eller senare från [Eclipse-webbplatsen](https://www.eclipse.org).  Installera även version 2.2.1 eller senare av Buildship (Service Fabric-plugin-programmet är inte kompatibelt med äldre versioner av Buildship):
-   Du kan kontrollera vilka versioner du har av installerade komponenter genom att välja **Hjälp** > **Om Eclipse** > **Installationsinformation** i Eclipse.
-   Om du vill uppdatera Buildship kan du läsa [Eclipse Buildship: Eclipse-plugin-program för Gradle][buildship-update] (på engelska).
-   Om du vill söka efter och installera uppdateringar för Eclipse kan du navigera till **Help** > **Check for Updates** (Hjälp > Sök efter uppdateringar).

Om du vill installera Service Fabric-plugin-programmet öppnar du **Help** > **Install New Software** (Hjälp > Installera ny programvara) i Eclipse.
1. I rutan **Work with** (Arbeta med) skriver du **http://dl.microsoft.com/eclipse**.
2. Klicka på **Lägg till**.
    ![Service Fabric-plugin-program för Eclipse][sf-eclipse-plugin-install]
3. Välj Service Fabric-plugin-programmet och klicka sedan på **Next** (Nästa).
4. Slutför installationsstegen och acceptera licensvillkoren för programvara från Microsoft.
  
Om du redan har Service Fabric-plugin-programmet installerat ska du installera den senaste versionen. 
1. Om du vill söka efter tillgängliga uppdateringar går du till **Hjälp** > **Om Eclipse** > **Installationsinformation**. 
2. Välj Service Fabric i listan över installerade plugin-program och klicka sedan på **Update** (Uppdatera). Tillgängliga uppdateringar installeras.
3. När du uppdaterar Service Fabrik-plugin-programmet ska du även uppdatera Gradle-projektet.  Högerklicka på **build.gradle** och välj **Uppdatera**.

> [!NOTE]
> Om installationen eller uppdateringen av Service Fabric-plugin-programmet är långsam kan det bero på en Eclipse-inställning. Eclipse samlar in metadata om alla ändringar på uppdateringsplatser som är registrerade med din Eclipse-instans. Om du vill påskynda sökningen efter och installationen av uppdateringen av Service Fabric-plugin-programmet kan du gå till **Available Software Sites** (Platser med tillgänglig programvara). Avmarkera kryssrutorna för alla platser utom den som pekar på platsen för Service Fabric-plugin-programmet (http://dl.microsoft.com/eclipse/azure/servicefabric).

> [!NOTE]
>Om Eclipse inte fungerar som förväntat på din Mac eller om du måste köra som en superanvändare) går du till mappen **ECLIPSE_INSTALLATION_PATH** och går till undermappen **Eclipse.app/Contents/MacOS**. Starta Eclipse genom att köra `./eclipse`.


## <a name="create-a-service-fabric-application-in-eclipse"></a>Skapa ett Service Fabric-program i Eclipse

1.  Öppna **File** > **New** > **Other** (Arkiv > Nytt > Annat) i Eclipse. Välj **Service Fabric Project** (Service Fabric Project) och klicka sedan på **Next** (Nästa).

    ![Service Fabric, ny projektsida 1][create-application/p1]

2.  Ange ett namn för ditt projekt och klicka sedan på **Next** (Nästa).

    ![Service Fabric, ny projektsida 2][create-application/p2]

3.  Välj **Service Template** (Tjänstmall) i listan med mallar. Välj typ av tjänstmall (Actor (Aktör), Stateless (Tillståndslös), Container (Behållare) eller Guest Binary (Gäst, binär)) och klicka sedan på **Next** (Nästa).

    ![Service Fabric, ny projektsida 3][create-application/p3]

4.  Ange namnet på tjänsten och information om tjänsten och klicka sedan på **Finish** (Slutför).

    ![Service Fabric, ny projektsida 4][create-application/p4]

5. När du skapar ditt första Service Fabric-projekt klickar du på **Yes** (Ja) i dialogrutan **Open Associated Perspective** (Öppna associerat perspektiv).

    ![Service Fabric, ny projektsida 5][create-application/p5]

6.  Det nya projektet ser ut så här:

    ![Service Fabric, ny projektsida 6][create-application/p6]

## <a name="build-and-deploy-a-service-fabric-application-in-eclipse"></a>Skapa och distribuera ett Service Fabric-program i Eclipse

1.  Högerklicka på det nya Service Fabric-programmet och välj sedan **Service Fabric**.

    ![Service Fabric-snabbmeny][publish/RightClick]

2. Välj önskat alternativ på undermenyn:
    -   Klicka på **Build Application** (Bygg program) om du vill skapa programmet utan rensning.
    -   Klicka på **Rebuild Application** (Bygg om program) om du vill skapa en rensad version av programmet.
    -   Klicka på **Clean Application** (Rensa program) om du vill rensa bort byggda artefakter i programmet.

3.  Du kan också välja att distribuera, ta bort eller publicera programmet på den här menyn:
    -   Klicka på **Deploy Application** (Distribuera program) om du vill distribuera till ditt lokala kluster.
    -   Välj en publiceringsprofil i dialogrutan **Publish Application** (Publicera program):
        -  **Local.json**
        -  **Cloud.json**

     De här JSON-filerna används för att lagra information (som anslutningsslutpunkter och säkerhetsinformation) som krävs för att ansluta till ett lokalt kluster eller ett molnkluster (Azure).

  ![Publicera-menyn för Service Fabric][publish/Publish]

Du kan också distribuera Service Fabric-programmet med Run Configurations (Kör konfigurationer) i Eclipse.

  1.    Välj **Run** > **Run Configurations** (Kör > Kör konfigurationer).
  2.    Välj **ServiceFabricDeployer** under **Gradle Project** (Gradle-projekt).
  3.    Välj **local** (lokalt) eller **cloud** (moln) i den högra rutan på fliken **Arguments** (Argument) för **publishProfile**.  Standardvärdet är **local** (lokalt). Om du distribuerar till ett fjärr- eller molnkluster väljer du **cloud** (moln).
  4.    För att säkerställa att rätt information anges i publiceringsprofilerna redigerar du **Local.json** eller **Cloud.json** efter behov. Du kan lägga till eller uppdatera information om slutpunkt och säkerhetsreferenser.
  5.    Kontrollera att **Working Directory** (Arbetskatalog) pekar på det program som du vill distribuera. Om du vill ändra program klickar du på knappen **Workspace** (Arbetsyta) och väljer önskat program.
  6.    Klicka på **Apply** (Verkställ) och sedan på **Run** (Kör).

Ditt program skapas och distribueras efter en liten stund. Du kan övervaka distributionsstatus i Service Fabric Explorer.  

## <a name="add-a-service-fabric-service-to-your-service-fabric-application"></a>Lägga till en Service Fabric-tjänst till ditt Service Fabric-program

Du kan lägga till en Service Fabric-tjänst till ett befintligt Service Fabric-program på följande sätt:

1.  Högerklicka på det projekt som du vill lägga till en tjänst för och klicka sedan på **Service Fabric**.

    ![Service Fabric, lägg till tjänst, sida 1][add-service/p1]

2.  Klicka på **Add Service Fabric Service** (Lägg till Service Fabric-tjänst) och slutför stegen för att lägga till en tjänst i projektet.
3.  Välj den tjänstmall som du vill lägga till för projektet och klicka sedan på **Next** (Nästa).

    ![Service Fabric, lägg till tjänst, sida 2][add-service/p2]

4.  Ange namnet på tjänsten (och andra uppgifter om det behövs) och klicka på knappen **Add Service** (Lägg till tjänst).  

    ![Service Fabric, lägg till tjänst, sida 3][add-service/p3]

5.  När tjänsten har lagts till ser projektstrukturen ut ungefär så här:

    ![Service Fabric, lägg till tjänst, sida 4][add-service/p4]

## <a name="edit-manifest-versions-of-your-service-fabric-java-application"></a>Redigera manifestversioner för Service Fabric Java-programmet

Om du vill redigera manifestversioner högerklickar du på projektet, går till **Service Fabric** och väljer **Edit Manifest Versions** (Redigera manifestversioner) från listrutan. I guiden kan du uppdatera manifestversioner för programmanifestet, tjänstmanifestet och versioner för paketen **Kod**, **Konfig** och **Data**.

Om du markerar alternativet **Automatically update application and service versions** (Uppdatera versioner av program och tjänster automatiskt) och sedan uppdaterar en version så uppdateras manifestversionerna automatiskt. Säg att du först markerar kryssrutan och sedan uppdaterar versionen **Kod**-versionen från 0.0.0 till 0.0.1 och klickar på **Slutför**. Då uppdateras tjänstemanifestversionen och programmanifestversionen automatiskt till 0.0.1.

## <a name="upgrade-your-service-fabric-java-application"></a>Uppgradera ditt Service Fabric Java-program

Anta att du har ett projekt som heter **App1** som du har skapat med Service Fabric-plugin-programmet i Eclipse. För att distribuera projektet skapade du ett program med namnet **fabric:/App1Application** med hjälp av plugin-programmet. Programtypen är **App1AppicationType** och programversionen är 1.0. Nu vill du uppgradera programmet utan att det påverkar tillgängligheten.

Gör ändringar i programmet och bygg sedan den ändrade tjänsten på nytt. Uppdatera manifestfilen (ServiceManifest.xml) för den ändrade tjänsten med de uppdaterade versionerna för tjänsten (samt kod, konfig eller data, om det behövs). Ändra också programmets manifest (ApplicationManifest.xml) med det uppdaterade versionsnumret för programmet och den ändrade tjänsten.  

Om du vill uppgradera programmet med Eclipse kan du skapa en duplicerad körningskonfigurationsprofil. Sedan kan du använda den för att uppgradera ditt program efter behov.

1.  Välj **Run** > **Run Configurations** (Kör > Kör konfigurationer). Klicka på den lilla pilen till vänster om **Gradle Project** (Gradle-projekt) i den vänstra rutan.
2.  Högerklicka på **ServiceFabricDeployer** och välj sedan **Duplicate** (Duplicera). Ange ett nytt namn för den här konfigurationen, till exempel **ServiceFabricUpgrader**.
3.  På den högra panelen på fliken **Argument** ändrar du **-Pconfig='deploy'** till **-Pconfig='upgrade'** och klickar på **Apply** (Verkställ).

Därmed skapas och sparas en körningskonfigurationsprofil som du när som helst kan använda till att uppgradera programmet. Då får du också den senast uppdaterade programtypversionen från programmanifestfilen.

Det tar ett par minuter att uppgradera programmet. Du kan övervaka programuppgraderingen i Service Fabric Explorer.

## <a name="migrating-old-service-fabric-java-applications-to-be-used-with-maven"></a>Migrera gamla Service Fabric Java-program som ska användas med Maven
Vi har nyligen flyttat Service Fabric Java-bibliotek från Service Fabric Java-SDK till Maven-centrallager. De nya program som du genererar med hjälp av Eclipse genererar senast uppdaterade projekt (som fungerar med Maven), men du kan uppdatera dina befintliga Service Fabric Java tillståndslösa eller aktörsprogram, som tidigare använde Service Fabric Java SDK, för att använda Service Fabric Java-beroenden från Maven. Följ anvisningarna [här](service-fabric-migrate-old-javaapp-to-use-maven.md) för att se till att det äldre programmet fungerar med Maven.

<!-- Images -->

[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-eclipse/service-fabric-eclipse-plugin.png

[create-application/p1]:./media/service-fabric-get-started-eclipse/create-application/p1.png
[create-application/p2]:./media/service-fabric-get-started-eclipse/create-application/p2.png
[create-application/p3]:./media/service-fabric-get-started-eclipse/create-application/p3.png
[create-application/p4]:./media/service-fabric-get-started-eclipse/create-application/p4.png
[create-application/p5]:./media/service-fabric-get-started-eclipse/create-application/p5.png
[create-application/p6]:./media/service-fabric-get-started-eclipse/create-application/p6.png

[publish/Publish]: ./media/service-fabric-get-started-eclipse/publish/Publish.png
[publish/RightClick]: ./media/service-fabric-get-started-eclipse/publish/RightClick.png

[add-service/p1]: ./media/service-fabric-get-started-eclipse/add-service/p1.png
[add-service/p2]: ./media/service-fabric-get-started-eclipse/add-service/p2.png
[add-service/p3]: ./media/service-fabric-get-started-eclipse/add-service/p3.png
[add-service/p4]: ./media/service-fabric-get-started-eclipse/add-service/p4.png

<!-- Links -->
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
