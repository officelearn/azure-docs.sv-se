---
title: Kom igång med Azure Apptjänst Mobile Apps för Xamarin.iOS-appar | Microsoft Docs
description: I den här kursen får du hjälp att komma igång med Mobile Apps för Xamarin.iOS-utveckling.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 14428794-52ad-4b51-956c-deb296cafa34
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: crdun
ms.openlocfilehash: 559050cbc575fce5bdb5b32ec266e1cc3d09b2d5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66242711"
---
# <a name="create-a-xamarinios-app"></a>Skapa en Xamarin.iOS-app
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Översikt
I den här kursen får du lära dig hur du lägger till en molnbaserad serverdelstjänst i en Xamarin.iOS-mobilapp med hjälp av en mobilappsserverdel i Azure.  Du skapar både en ny mobilapp-serverdel och en enkel *Att göra-lista* Xamarin.iOS-app som lagrar app-data i Azure.

Du måste slutföra den här kursen innan du påbörjar någon annan Xamarin.iOS-kurs om att använda funktionen Mobile Apps i Azure Apptjänst.

## <a name="prerequisites"></a>Nödvändiga komponenter
För att slutföra den här självstudien, finns följande förhandskrav:

* Ett aktivt Azure-konto. Om du inte har ett konto kan du registrera dig för en utvärderingsversion av Azure och få upp till tio mobilappar utan kostnad som du kan fortsätta att använda även efter utvärderingsperiodens slut. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio för Mac. Se [konfiguration och installation av Visual Studio för Mac](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)
* En Mac med Xcode 9.0 eller senare.
  
## <a name="create-an-azure-mobile-app-backend"></a>Skapa en mobilapp-serverdel i Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Skapa en databasanslutning och konfigurera projektet klient och server
[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinios-app"></a>Kör Xamarin.iOS-app
1. Öppna Xamarin.iOS-projektet.

2. Gå till den [Azure-portalen](https://portal.azure.com/) och navigera till den mobila appen som du skapade. På den `Overview` bladet letar du reda på URL: en som är den offentliga slutpunkten för din mobilapp. Exempel – sitename för min app name ”test123” kommer att https://test123.azurewebsites.net.

3. Öppna filen `QSTodoService.cs` i den här mappen - xamarin.iOS/ZUMOAPPNAME. Programnamnet är `ZUMOAPPNAME`.

4. I `QSTodoService` klass, Ersätt `ZUMOAPPURL` variabeln med offentlig slutpunkt ovan.

    `const string applicationURL = @"ZUMOAPPURL";`

    blir
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. Tryck på F5 för att distribuera och köra appen i iPhone-emulatorn.

6. I appen, ange en beskrivande text som *i självstudiekursen* och klicka sedan på den knappen.

    ![][10]

    Data från begäran infogas i tabellen TodoItem. Objekt som lagras i tabellen returneras av mobilappsserverdelen och data visas i listan.

   > [!NOTE]
   > Du kan se koden som ansluter till mobilappsserverdelen för att fråga efter och infoga data i C#-filen ToDoActivity.cs.
   
<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png