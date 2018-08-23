---
title: Hantera dina Azure Servic Fabric-program i Visual Studio | Microsoft Docs
description: Använd Visual Studio för att skapa, utveckla, paketera, distribuera och felsöka Azure Service Fabric-program och tjänster.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: ''
ms.assetid: c317cb7e-7eae-466e-ba41-6aa2518be5cf
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 03/26/2018
ms.author: mikhegn
ms.openlocfilehash: 2e7b7d9b0a194b13de9bdf759f4f3be645ed7c2e
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2018
ms.locfileid: "42442177"
---
# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Använd Visual Studio för att förenkla skriva och hantera dina Service Fabric-program
Du kan hantera dina Azure Service Fabric-program och tjänster via Visual Studio. När du har [ställa in din utvecklingsmiljö](service-fabric-get-started.md), du kan använda Visual Studio för att skapa Service Fabric-program, lägga till tjänster eller ett paket, registrera och distribuera program i det lokala utvecklingsklustret.

## <a name="deploy-your-service-fabric-application"></a>Distribuera Service Fabric-programmet
Som standard kombinerar följande steg i en enkel åtgärd när du distribuerar ett program:

1. Skapa programpaketet
2. Ladda upp programpaketet till avbildningsarkivet
3. Registrera programtypen
4. Ta bort alla instanser av programmet körs
5. Skapa en programinstans

I Visual Studio att trycka på **F5** distribuerar ditt program och bifoga felsökningsprogrammet till alla instanser av programmet. Du kan använda **Ctrl + F5** att distribuera ett program utan felsökning eller du kan publicera till en lokal eller fjärransluten kluster med hjälp av profilen.

### <a name="application-debug-mode"></a>Programmet felsökningsläge
Visual Studio tillhandahåller en egenskap som kallas **programmets felsökningsläge**, som styr du hur Visual Studios att hantera distribution av program som en del av felsökning.

#### <a name="to-set-the-application-debug-mode-property"></a>Ange egenskapen programmets felsökningsläge
1. På Service Fabric-program projektets (*.sfproj) snabbmenyn och välj **egenskaper** (eller tryck på den **F4** nyckel).
2. I den **egenskaper** ställer den **programmets felsökningsläge** egenskapen.

![Ange program Debug-frågeläge][debugmodeproperty]

#### <a name="application-debug-modes"></a>Lägen för felsökning av program

1. **Uppdatera programmet** det här läget kan du snabbt ändra och felsöka din kod och stöd för redigering av statiska webbfiler när du felsöker. Det här läget fungerar bara om det lokala utvecklingsklustret finns i [1 nod läge]. Detta är standardinställningen programmets felsökningsläge. (/ service-fabric-get-started-with-a-local-cluster.md#one-node-and-five-node-cluster-mode).
2. **Ta bort programmet** gör så att programmet ska tas bort när debug-sessionen avslutas.
3. **Automatisk uppgradering** programmet fortsätter att köras när debug-sessionen avslutas. Nästa felsökningssessionen hanterar distributionen som en uppgradering. Uppgraderingsprocessen bevarar alla data som du angav i föregående felsökningssessionen.
4. **Zachovat Aplikaci** programmet köras i klustret när debug-sessionen avslutas. I början av nästa felsökningssessionen tas programmet bort.

För **automatisk uppgradering** bevaras data genom att använda programfunktioner för uppgradering av Service Fabric. Mer information om hur du uppgraderar program och hur du kan utföra en uppgradering i en verklig miljö finns i [Service Fabric-Programuppgradering](service-fabric-application-upgrade.md).

## <a name="add-a-service-to-your-service-fabric-application"></a>Lägga till en tjänst till ditt Service Fabric-program
Du kan lägga till nya tjänster i ditt program för att utöka dess funktioner. För att säkerställa att tjänsten ingår i programpaketet, lägger du till tjänsten via den **ny Fabric-tjänst...**  menyalternativ.

![Lägg till en ny Service Fabric-tjänst][newservice]

Välj en typ av Service Fabric-projekt som du lägger till i ditt program och ange ett namn för tjänsten.  Se [välja ett ramverk för din tjänst](service-fabric-choose-framework.md) för att avgöra vilken typ av tjänst du använder.

![Välj en Service Fabric-projekt tjänsttyp för att lägga till i ditt program][addserviceproject]

Den nya tjänsten har lagts till din lösning och befintligt programpaket. Servicereferenser och en standard-tjänstinstans läggs till i programmanifestet orsakar tjänsten skapas och startas nästa gång du distribuerar programmet.

![Den nya tjänsten har lagts till i programmanifestet][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>Paketera ditt Service Fabric-program
Om du vill distribuera programmet och dess tjänster till ett kluster, måste du skapa ett programpaket.  Paketet organiserar applikationsmanifestet, tjänstmanifest och andra nödvändiga filer i en viss layout.  Visual Studio konfigurerar och hanterar paketet i programprojektet-mappen i katalogen 'pkg.  Klicka på **paketet** från den **program** snabbmenyn skapar eller uppdaterar programpaketet.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Ta bort program och programtyper med Cloud Explorer
Du kan utföra grundläggande klusterhanteringsåtgärder inifrån Visual Studio med Cloud Explorer, som du kan starta från den **visa** menyn. Du kan till exempel ta bort program och avetablera programtyper i lokala eller fjärranslutna kluster.

![Ta bort ett program][removeapplication]

> [!TIP]
> En mer omfattande kluster-hanteringsfunktioner, se [visualisera klustret med Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg
* [Service Fabric-programmodellen](service-fabric-application-model.md)
* [Distribution av Service Fabric-program](service-fabric-deploy-remove-applications.md)
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