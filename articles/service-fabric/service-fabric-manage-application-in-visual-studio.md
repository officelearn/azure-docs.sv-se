---
title: Hantera program i Visual Studio
description: Använd Visual Studio för att skapa, utveckla, paketera, distribuera och felsöka dina Azure Service Fabric-program och -tjänster.
author: mikkelhegn
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: mikhegn
ms.openlocfilehash: d6734f5da0fb7e5c9052b26b55b2d90b068bdbbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614340"
---
# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Använda Visual Studio för att förenkla skrivning och hantering av dina Service Fabric-program
Du kan hantera dina Azure Service Fabric-program och tjänster via Visual Studio. När du har [konfigurerat utvecklingsmiljön](service-fabric-get-started.md)kan du använda Visual Studio för att skapa Service Fabric-program, lägga till tjänster eller paketera, registrera och distribuera program i det lokala utvecklingsklustret.

## <a name="deploy-your-service-fabric-application"></a>Distribuera ditt Service Fabric-program
Som standard kombineras följande steg för att distribuera ett program till en enkel åtgärd:

1. Skapa programpaketet
2. Ladda upp programpaketet till bildarkivet
3. Registrera programtypen
4. Ta bort alla programinstanser som körs
5. Skapa en programinstans

I Visual Studio distribuerar du programmet genom att trycka på **F5** och bifoga felsökningsfel till alla programinstanser. Du kan använda **Ctrl+F5** för att distribuera ett program utan felsökning, eller så kan du publicera till ett lokalt kluster eller fjärrkluster med hjälp av publiceringsprofilen.

### <a name="application-debug-mode"></a>Felsökningsläge för program
Visual Studio tillhandahåller en egenskap som heter **Application Debug Mode**, som styr hur du vill att Visual Studios ska hantera programdistribution som en del av felsökning.

#### <a name="to-set-the-application-debug-mode-property"></a>Så här anger du egenskapen Programfelsökningsläge
1. På snabbmenyn för service fabric-programprojektet (*.sfproj) väljer du **Egenskaper** (eller trycker på **F4).**
2. Ange egenskapen **Programfelsökläge** i fönstret **Egenskaper.**

![Ange egenskapen för felsökningsläge för program][debugmodeproperty]

#### <a name="application-debug-modes"></a>Felsökningslägen för program

1. **Uppdatera program** I det här läget kan du snabbt ändra och felsöka koden och stöder redigering av statiska webbfiler medan du felsöker. Det här läget fungerar bara om det lokala utvecklingsklustret är i 1-nodläge. Detta är standardläget för programfelsökning.
2. **Om du tar bort programmet** tas programmet bort när felsökningssessionen avslutas.
3. **Automatisk uppgradering** Programmet fortsätter att köras när felsökningssessionen avslutas. Nästa felsökningssession behandlar distributionen som en uppgradering. Uppgraderingsprocessen bevarar alla data som du har angett i en tidigare felsökningssession.
4. **Behåll programmet** Programmet fortsätter att köras i klustret när felsökningssessionen avslutas. I början av nästa felsökningssession tas programmet bort.

För automatisk **uppgradering** bevaras data genom att använda programuppgraderingsfunktionerna i Service Fabric. Mer information om hur du uppgraderar program och hur du kan utföra en uppgradering i en verklig miljö finns i [Uppgradering av Programuppgraderingen för Service Fabric](service-fabric-application-upgrade.md).

## <a name="add-a-service-to-your-service-fabric-application"></a>Lägga till en tjänst i ditt Service Fabric-program
Du kan lägga till nya tjänster i programmet för att utöka dess funktionalitet. För att säkerställa att tjänsten ingår i ditt programpaket lägger du till tjänsten via menyalternativet **New Fabric Service.**

![Lägga till en ny Service Fabric-tjänst][newservice]

Välj en service fabric-projekttyp som du vill lägga till i programmet och ange ett namn för tjänsten.  Se [Välja ett ramverk för din tjänst som](service-fabric-choose-framework.md) hjälper dig att bestämma vilken tjänsttyp du ska använda.

![Välj en serviceprogramtyp för Service Fabric som du vill lägga till i programmet][addserviceproject]

Den nya tjänsten läggs till i din lösning och befintliga programpaket. Tjänstreferenserna och en standardtjänstinstans läggs till i programmanifestet, vilket gör att tjänsten skapas och startas nästa gång du distribuerar programmet.

![Den nya tjänsten läggs till i ditt programmanifest][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>Paketera ditt Service Fabric-program
Om du vill distribuera programmet och dess tjänster till ett kluster måste du skapa ett programpaket.  Paketet organiserar programmanifestet, tjänstmanifesten och andra nödvändiga filer i en viss layout.  Visual Studio ställer in och hanterar paketet i programprojektets mapp i katalogen "pkg".  Om du klickar på **Paket** på **snabbmenyn Program** skapas eller uppdateras programpaketet.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Ta bort program och programtyper med Cloud Explorer
Du kan utföra grundläggande klusterhanteringsåtgärder inifrån Visual Studio med **View** Cloud Explorer, som du kan starta från Visa-menyn. Du kan till exempel ta bort program och avetablera programtyper på lokala kluster eller fjärrkluster.

![Ta bort ett program][removeapplication]

> [!TIP]
> En rikare klusterhanteringsfunktion finns i [Visualisera klustret med Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg
* [Programmodell för Service Fabric](service-fabric-application-model.md)
* [Distribution av tjänst fabric-program](service-fabric-deploy-remove-applications.md)
* [Hantera programparametrar för flera miljöer](service-fabric-manage-multiple-environment-app-configuration.md)
* [Felsöka ditt Service Fabric-program](service-fabric-debugging-your-application.md)
* [Visualisera klustret med hjälp av Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
[removeapplication]:./media/service-fabric-manage-application-in-visual-studio/removeapplication.png
