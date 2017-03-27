---
title: "Kom igång med Eclipse-plugin-programmet för Azure Service Fabric | Microsoft Docs"
description: "Kom igång med Eclipse-plugin-programmet för Azure Service Fabric."
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
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: b612259b97bf238bac28cb77bf26f684128dd882
ms.lasthandoff: 03/21/2017


---

# <a name="getting-started-with-eclipse-plugin-for-service-fabric-java-application-development"></a>Kom igång med Eclipse-plugin-programmet för Service Fabric Java-apputveckling
Eclipse är ett av de mest använda IDE:erna för Java-utvecklare. I den här artikeln diskuterar vi hur du kan ställa in din Eclipse-utvecklingsmiljö för att arbeta med Service Fabric. Den här artikeln hjälper dig att installera plugin-programmet, skapa Service Fabric-program och distribuera Service Fabric-programmet till ett lokalt eller fjärranslutet Service Fabric-kluster.

## <a name="install-or-update-service-fabric-plugin-on-eclipse-neon"></a>Installera eller uppdatera Service Fabric-plugin-programmet för Eclipse Neon
Service Fabric innehåller ett plugin-program för **Eclipse IDE för Java-utvecklare** som förenklar processen att skapa och distribuera Java-tjänster.

1. Kontrollera att du har den senaste Eclipse **Neon**-versionen och den senaste Buildship-versionen (1.0.17 eller senare) installerat. Du kan kontrollera vilka versioner de installerade komponenterna har genom att välja **Help > Installation Details** (Hjälp > Installationsinformation). Du kan uppdatera Buildship genom att följa instruktionerna [här][buildship-update]. Om du vill kontrollera och uppdatera om Eclipse Neon-versionen är den senaste går du till **Help => Check for Updates** (Hjälp => Sök efter uppdateringar).

2. Om du vill installera Service Fabric-plugin-programmet väljer du **Help > Install New Software...** (Hjälp > Installera ny programvara...).
  1. I textrutan "Work with" (Arbeta med) skriver du: ``http://dl.windowsazure.com/eclipse/servicefabric``.
  2. Klicka på Add (Lägg till).

  ![Eclipse Neon-plugin-programmet för Service Fabric][sf-eclipse-plugin-install]

  3. Välj Service Fabric-plugin-programmet och klicka på Next (Nästa).
  4. Fortsätt med installationen och acceptera licensvillkoren.

Om du redan har plugin-programmet för Service Fabric Eclipse installerat kontrollerar du att du har den senaste versionen. Så här kan du kontrollera om det kan uppdateras ännu mer – **Hjälp => Installationsinformation**. Sök sedan efter Service Fabric i listan över installerade plugin-program och klicka på Uppdatera. Om det finns någon aktuell uppdatering kommer den att hämtas och installeras.

> [!NOTE]
> Om installation eller uppdatering av Service Fabric Eclipse-plugin-programmet tar lång tid på din Eclipse beror det på att varje gång Eclipse försöker hämta metadata för alla nya ändringar som har skett registreras uppdateringsplatserna med din Eclipse-instans. Du kan ta till ett litet knep för att det ska gå snabbare – du kan gå till **Available Software Sites** (Tillgängliga programvaruplatser) och avmarkera allt annat än det som pekar på platsen för Service Fabric-plugin-programmet `http://dl.windowsazure.com/eclipse/servicefabric`.
>

## <a name="create-service-fabric-application-using-eclipse"></a>Skapa Service Fabric-programmet med Eclipse

1. Gå till **File => New => Other** (Arkiv=> Nytt => Annat). Välj **Service Fabric Project** (Service Fabric-projekt). Klicka på **Next**.

    ![Service Fabric, ny projektsida 1][create-application/p1]

2. Ge projektet ett namn. Klicka på **Nästa**.

    ![Service Fabric, ny projektsida 2][create-application/p2]

3. Välj tjänstmall från de tillgängliga malluppsättningarna (körbar med Actor, Stateless, Container eller Guest (aktör, tillståndslös, behållare eller gäst)). Klicka på **Next**.

    ![Service Fabric, ny projektsida 3][create-application/p3]

4. Ange tjänstens namn och/eller relevanta uppgifter om tjänsten på den här sidan och klicka på **Finish** (Avsluta).

    ![Service Fabric, ny projektsida 4][create-application/p4]

5. När du skapar ditt första Service Fabric-projekt får du frågan om du vill ställa in Service Fabric-perspektivet. Välj **ja** för att fortsätta.

    ![Service Fabric, ny projektsida 5][create-application/p5]

6. När projektet har skapats ser det ut så här –

    ![Service Fabric, ny projektsida 6][create-application/p6]

## <a name="build-and-deploy-the-service-fabric-application-using-eclipse"></a>Bygg och distribuera Service Fabric-programmet med Eclipse

* Högerklicka på Service Fabric-programmet du just skapade ovan. Välj alternativet **Service Fabric** på snabbmenyn. Nu visas en undermeny med flera alternativ. som ser ut som –

    ![Service Fabric-snabbmeny][publish/RightClick]

  När du klickar på alternativen för att skapa, återskapa och rensa utförs avsedda åtgärder.
  - **Build Application** (Bygg program) skapar programmet utan rensning
  - **Rebuild Application** (Bygg om program) gör en rensad version av programmet
  - **Clean Application** (Rensa program) rensar bort byggda artefakter i programmet


* Du kan välja att distribuera, ta bort eller publicera programmet på den här menyn.
  - **Deploy Application** (Distribuera program) distribuerar till ditt lokala kluster
  - **Publish Application...** (Publicera program...) frågar vilken publiceringsprofil du vill välja av ``Local.json`` och ``Cloud.json``. De här JSON-filerna används för att lagra information (som anslutningsslutpunkter och säkerhetsinformation) som krävs för att ansluta till ett lokalt kluster eller ett molnkluster (Azure).

  ![Service Fabric-snabbmeny][publish/Publish]

* Du kan distribuera Service Fabric-programmet på ett annat sätt med Eclipse Run Configurations.

  1. Välj **Run => Run Configurations** (Kör => Kör konfigurationer). Välj konfigurationen **ServiceFabricDeployer** under **Grade Project** (Gradprojekt).
  2. Under fliken **Arguments** (Argument) i den högra rutan anger du **lokal** eller **moln** som **publishProfile**. Standardinställningen är **lokal**. Om du distribuerar till ett fjärr-/molnkluster väljer du **moln**.
  3. Se till att rätt information visas i publiceringsprofilerna genom att redigera `Local.json` eller `Cloud.json` med information om slutpunkt och säkerhetsuppgifter om det finns några.
  4. Kontrollera att **Working Directory** (Arbetskatalog) i den högra rutan under **Grade Project** (Gradprojekt) pekar på programmet du vill distribuera. Annars klickar du bara på knappen **Workspace...** (Arbetsyta) och väljer önskat program.
  5. Klicka på **Apply** (Använd) och **Kör**.

Ditt program skapas och distribueras efter en liten stund. Du kan övervaka dess status från Service Fabric Explorer.  

## <a name="add-new-service-fabric-service-to-your-service-fabric-application"></a>Lägga till ny Service Fabric-tjänst till ditt Service Fabric-program

Du kan lägga till en ny Service Fabric-tjänst till ett befintligt Service Fabric-program på följande sätt:

1. Högerklicka på projektet där du vill lägga till en tjänst för att öppna snabbmenyn och välja alternativet **Service Fabric**. Nu visas en undermeny med flera alternativ.

    ![Service Fabric, lägg till tjänst, sida 1][add-service/p1]

2. Om du väljer alternativet **Add ServiceFabric Service** (Lägg till ServiceFabric-tjänst) leds du genom nästa uppsättning steg för att lägga till en tjänst i projektet.
3. Välj den tjänstmall du vill lägga till för projektet och klicka på **Nästa**.

    ![Service Fabric, lägg till tjänst, sida 2][add-service/p2]

4. Ange tjänstens namn (och övrig nödvändig information när det behövs) och klicka på knappen **Lägg till tjänst** nedan.  

    ![Service Fabric, lägg till tjänst, sida 3][add-service/p3]

5. När tjänsten har lagts till ser hela projektstrukturen ut ungefär som det du ser nedan –

    ![Service Fabric, lägg till tjänst, sida 4][add-service/p4]

## <a name="upgrade-your-service-fabric-java-application"></a>Uppgradera ditt Service Fabric Java-program

Anta att du har skapat projektet ``App1`` med Service Fabric Eclipse-plugin-programmet och distribuerat det med plugin-programmet för att skapa ett program som heter ``fabric:/App1Application`` av programtyp ``App1AppicationType`` och programversion 1.0. Nu vill du uppgradera programmet utan att stänga av det.

Gör ändringar i programmet och återskapa den ändrade tjänsten.  Uppdatera den ändrade tjänstens manifestfil (``ServiceManifest.xml``) med de uppdaterade versionerna för tjänsten (och kod, konfiguration eller data som är tillämpligt). Ändra också programmets manifest (``ApplicationManifest.xml``) med det uppdaterade versionsnumret och den ändrade tjänsten för programmet.  

Om du vill uppgradera ditt program med Eclipse kan du skapa en dubbel körningskonfiguration och använda den för att uppgradera programmet som och när du vill, med följande steg –
1. Välj **Run => Run Configurations** (Kör => Kör konfigurationer). Klicka på den lilla pilen till vänster om **Grade Project** (Gradprojekt) i den vänstra rutan.
2. Högerklicka på **ServiceFabricDeployer** och välj **Duplicate** (Duplicera). Ge konfigurationen ett namn, till exempel **ServiceFabricUpgrader**.
3. På den högra panelen under fliken **Argument** ändrar du **-Pconfig='deploy'** till **-Pconfig=upgrade** och klickar på **Verkställ**.
4. Nu har du skapat och sparat en körningskonfiguration för att uppgradera programmet, som du kan **köra** när du vill. Då får du den senast uppdaterade programtypversionen från programmanifestfilen.

Nu kan du övervaka programuppdateringen med Service Fabric Explorer. Efter några minuter har programmet uppdaterats.

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

