---
title: "Azure Service Fabric-plugin-program för Eclipse | Microsoft Docs"
description: "Kom igång med Service Fabric-plugin-programmet för Eclipse."
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/27/2016
ms.author: saysa
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: 0407eab7e70649999ba07730425366b7b62e4e7a
ms.lasthandoff: 03/22/2017


---

# <a name="service-fabric-plug-in-for-eclipse-java-application-development"></a>Service Fabric-plugin-program för utveckling av Java-program i Eclipse
Eclipse är en av de mest använda IDE:erna (Integrated Development Environment) för Java-utvecklare. I den här artikeln beskrivs hur du kan konfigurera din Eclipse-utvecklingsmiljö för att arbeta med Azure Service Fabric. Läs om hur du installerar Service Fabric-plugin-programmet, skapar ett Service Fabric-program och distribuerar Service Fabric-programmet till ett lokalt eller fjärranslutet Service Fabric-kluster i Eclipse Neon.

## <a name="install-or-update-the-service-fabric-plug-in-in-eclipse-neon"></a>Installera eller uppdatera Service Fabric-plugin-programmet i Eclipse Neon
Du kan installera ett Service Fabric-plugin-program i Eclipse. Plugin-programmet gör det enklare att skapa och distribuera Java-tjänster.

1.  Kontrollera att du har installerat den senaste versionen av Eclipse Neon och den senaste versionen av Buildship (version 1.0.17 eller senare):
    -   Du kan kontrollera vilka versioner de installerade komponenterna har genom att välja **Help** > **Installation Details** (Hjälp > Installationsinformation) i Eclipse Neon.
    -   Om du vill uppdatera Buildship kan du läsa [Eclipse Buildship: Eclipse-plugin-program för Gradle][buildship-update] (på engelska).
    -   Om du vill söka efter och installera uppdateringar för Eclipse Neon kan du gå till **Help** > **Check for Updates** (Hjälp > Sök efter uppdateringar).

2.  Om du vill installera Service Fabric-plugin-programmet går du till **Help** > **Install New Software** (Hjälp > Installera ny programvara) i Eclipse Neon.
  1.    I rutan **Work with** (Arbeta med) anger du **http://dl.windowsazure.com/eclipse/servicefabric**.
  2.    Klicka på **Lägg till**.
    ![Service Fabric-plugin-program för Eclipse Neon][sf-eclipse-plugin-install]
  3.    Välj Service Fabric-plugin-programmet och klicka sedan på **Next** (Nästa).
  4.    Slutför installationsstegen och acceptera licensvillkoren för programvara från Microsoft.

Om du redan har Service Fabric-plugin-programmet installerat kontrollerar du att du har den senaste versionen. Om du vill söka efter uppdateringar går du till **Help** > **Installation Details** (Hjälp > Installationsinformation). Välj Service Fabric i listan över installerade plugin-program och klicka sedan på **Update** (Uppdatera). Tillgängliga uppdateringar installeras.

> [!NOTE]
> Om installationen eller uppdateringen av Service Fabric-plugin-programmet är långsam kan det bero på en Eclipse-inställning. Eclipse samlar in metadata om alla ändringar på uppdateringsplatser som är registrerade med din Eclipse-instans. Om du vill påskynda sökningen efter och installationen av uppdateringen av Service Fabric-plugin-programmet kan du gå till **Available Software Sites** (Platser med tillgänglig programvara). Avmarkera kryssrutorna för alla platser utom den som pekar på platsen för Service Fabric-plugin-programmet (http://dl.windowsazure.com/eclipse/servicefabric).

## <a name="create-a-service-fabric-application-in-eclipse"></a>Skapa ett Service Fabric-program i Eclipse

1.  Gå till **File** > **New** > **Other** (Arkiv > Nytt > Annat) i Eclipse Neon. Välj **Service Fabric Project** (Service Fabric Project) och klicka sedan på **Next** (Nästa).

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

## <a name="upgrade-your-service-fabric-java-application"></a>Uppgradera ditt Service Fabric Java-program

Anta att du har ett projekt som heter **App1** som du har skapat med Service Fabric-plugin-programmet i Eclipse. För att distribuera projektet skapade du ett program med namnet **fabric:/App1Application** med hjälp av plugin-programmet. Programtypen är **App1AppicationType** och programversionen är 1.0. Nu vill du uppgradera programmet utan att det påverkar tillgängligheten.

Gör ändringar i programmet och bygg sedan den ändrade tjänsten på nytt. Uppdatera manifestfilen (ServiceManifest.xml) för den ändrade tjänsten med de uppdaterade versionerna för tjänsten (samt kod, konfig eller data, om det behövs). Ändra också programmets manifest (ApplicationManifest.xml) med det uppdaterade versionsnumret för programmet och den ändrade tjänsten.  

Om du vill uppgradera programmet med Eclipse Neon kan du skapa en duplicerad körningskonfigurationsprofil. Sedan kan du använda den för att uppgradera ditt program efter behov.

1.  Välj **Run** > **Run Configurations** (Kör > Kör konfigurationer). Klicka på den lilla pilen till vänster om **Gradle Project** (Gradle-projekt) i den vänstra rutan.
2.  Högerklicka på **ServiceFabricDeployer** och välj sedan **Duplicate** (Duplicera). Ange ett nytt namn för den här konfigurationen, till exempel **ServiceFabricUpgrader**.
3.  På den högra panelen på fliken **Argument** ändrar du **-Pconfig='deploy'** till **-Pconfig='upgrade'** och klickar på **Apply** (Verkställ).

Därmed skapas och sparas en körningskonfigurationsprofil som du när som helst kan använda till att uppgradera programmet. Då får du också den senast uppdaterade programtypversionen från programmanifestfilen.

Det tar ett par minuter att uppgradera programmet. Du kan övervaka programuppgraderingen i Service Fabric Explorer.

<!-- Images -->

[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png

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

