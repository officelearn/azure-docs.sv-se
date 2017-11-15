---
title: Hantera dina program i Visual Studio | Microsoft Docs
description: "Du kan använda Visual Studio för att skapa, utveckla, paketera, distribuera och felsöka din Service Fabric-program och tjänster."
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: 
ms.assetid: c317cb7e-7eae-466e-ba41-6aa2518be5cf
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/07/2017
ms.author: mikkelhegn
ms.openlocfilehash: 81814ef07cfab06855b3bfbf0eb6ef51b8dfce4f
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2017
---
# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Använd Visual Studio för att förenkla skriva och hantera dina Service Fabric-program
Du kan hantera dina Azure Service Fabric-program och tjänster via Visual Studio. När du har [ställa in din utvecklingsmiljö](service-fabric-get-started.md), du kan använda Visual Studio skapar Service Fabric-program, lägga till tjänster eller paket, registrera och distribuera program i klustret för lokal utveckling.

## <a name="deploy-your-service-fabric-application"></a>Distribuera Service Fabric-program
Som standard kombinerar följande steg i en enkel åtgärd när du distribuerar ett program:

1. Skapa programpaketet
2. Ladda upp programpaketet image store
3. Registrerar programtyp
4. Att ta bort alla instanser av programmet körs
5. Skapa en instans av programmet

I Visual Studio, trycka på **F5** distribuerar ditt program och koppla felsökaren till alla instanser av programmet. Du kan använda **Ctrl + F5** du distribuerar ett program utan felsökning eller du kan publicera till en lokal eller fjärransluten kluster med hjälp av profilen. 

### <a name="application-debug-mode"></a>Programmet felsökningsläge
Visual Studio tillhandahåller en egenskap som kallas **programmet felsökningsläge**, som styr hur du vill att Visual Studios att hantera programdistribution som en del av felsökning.

#### <a name="to-set-the-application-debug-mode-property"></a>Att ange egenskapen Debug programläge
1. På Service Fabric application projektets (*.sfproj) snabbmenyn väljer **egenskaper** (eller trycker på den **F4** nyckel).
2. I den **egenskaper** fönstret kan du ange den **programmet felsökningsläge** egenskapen.

![Egenskapen programmet Debug-läge][debugmodeproperty]

#### <a name="application-debug-modes"></a>Programmet felsökningslägen

1. **Uppdatera program** detta läge kan du snabbt ändra och felsöka din kod och stöd för redigering av statiska filer vid felsökning. Det här läget fungerar bara om lokal utveckling klustret är i [1 nod läge](/service-fabric-get-started-with-a-local-cluster.md#one-node-and-five-node-cluster-mode).
2. **Ta bort programmet** gör att programmet ska tas bort när debug-sessionen avslutas.
3. **Automatisk uppgradering** programmet fortsätter att köras när debug-sessionen avslutas. Nästa felsökningssessionen behandlar distributionen som en uppgradering. Uppgraderingsprocessen behåller alla data som du angav i föregående felsökningssessionen.
4. **Hålla program** programmet körs i klustret när debug-sessionen avslutas. I början av nästa felsökningssessionen tas programmet bort.

För **automatiskt uppgradera** bevaras data genom att använda programfunktioner för uppgradering av Service Fabric. Mer information om hur du uppgraderar program och hur du kan utföra en uppgradering i en verklig miljö finns [uppgradering av Service Fabric-programmet](service-fabric-application-upgrade.md).

## <a name="add-a-service-to-your-service-fabric-application"></a>Lägga till en tjänst till Service Fabric-program
Du kan lägga till nya tjänster i programmet för att utöka dess funktionalitet.  För att säkerställa att tjänsten ingår i ditt programpaket, lägger du till tjänsten via den **nya Fabric-tjänsten...**  menyalternativ.

![Lägg till en ny Service Fabric-tjänst][newservice]

Välj en typ av Service Fabric-projekt att lägga till i ditt program och ange ett namn för tjänsten.  Se [att välja ett ramverk för din tjänst](service-fabric-choose-framework.md) för att avgöra vilken tjänsttyp som ska använda.

![Välj en projekttyp för Service Fabric-tjänsten att lägga till i ditt program][addserviceproject]

Den nya tjänsten har lagts till i din lösning och befintliga programpaket. Referenser för och en standard tjänstinstans läggs till applikationsmanifestet orsakar tjänsten kan skapas och startas nästa gång du distribuerar programmet.

![Den nya tjänsten har lagts till i programmanifestet][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>Paketera Service Fabric-program
Om du vill distribuera programmet och dess tjänster till ett kluster, måste du skapa ett programpaket.  Paketet organiserar programmanifestet service manifest och andra nödvändiga filer i en viss layout.  Visual Studio konfigurerar och hanterar paket i programprojektet-mappen i katalogen 'pkg'.  Klicka på **paketet** från den **programmet** snabbmenyn skapar eller uppdaterar programpaketet.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Ta bort program och programtyper med Cloud Explorer
Du kan utföra grundläggande klusterhanteringsåtgärder inifrån Visual Studio med Cloud Explorer som du kan starta från den **visa** menyn. Du kan till exempel ta bort program och avetablera programtyper på lokala eller fjärranslutna kluster.

![Ta bort ett program][removeapplication]

> [!TIP]
> Rikare hanteringsfunktioner för klustret, se [visualisera ditt kluster med Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg
* [Service Fabric programmodell](service-fabric-application-model.md)
* [Distribution av Service Fabric](service-fabric-deploy-remove-applications.md)
* [Hantera programparametrar för miljöer med flera](service-fabric-manage-multiple-environment-app-configuration.md)
* [Felsöka ditt Service Fabric-program](service-fabric-debugging-your-application.md)
* [Visualisera ditt kluster med hjälp av Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
[removeapplication]:./media/service-fabric-manage-application-in-visual-studio/removeapplication.png