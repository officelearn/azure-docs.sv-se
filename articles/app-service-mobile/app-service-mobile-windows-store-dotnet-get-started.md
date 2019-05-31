---
title: Skapa en universell Windowsplattform (UWP) som använder Azure Mobile Apps | Microsoft Docs
description: Följ den här kursen och kom igång med att använda serverdelar för mobilappar i Azure för utveckling av appar med den universella Windowsplattformen (UWP) i C#, Visual Basic eller JavaScript.
services: app-service\mobile
documentationcenter: windows
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 47124296-2908-4d92-85e0-05c4aa6db916
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: crdun
ms.openlocfilehash: be579b631fd910c56f2c360d6aace5b8d35c22e5
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235995"
---
# <a name="create-a-windows-app-with-an-azure-backend"></a>Skapa en Windows-app med en Azure-serverdel

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Översikt

I den här kursen får du lära dig hur du lägger till en molnbaserad serverdelstjänst i en app i den universella Windowsplattformen (UWP). Mer information om Mobile Apps finns [här](app-service-mobile-value-prop.md). Nedan visas skärmdumpar från den färdiga appen:

![Färdig skrivbordsapp](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)

Du måste slutföra den här kursen innan du går någon annan kurs om Mobilappar för UWP-appar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna genomföra den här kursen behöver du följande:

* Ett aktivt Azure-konto. Om du inte har ett konto kan du registrera dig för en utvärderingsversion av Azure och få upp till tio mobilappar utan kostnad som du kan fortsätta att använda även efter utvärderingsperiodens slut. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/).
* Windows 10.
* Visual Studio Community 2017.
* Kunskap om UWP-apputveckling. Gå till [UWP-dokumentation](https://docs.microsoft.com/windows/uwp/) om du vill lära dig hur du [kommer igång](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) med att skapa UWP-appar.

## <a name="create-a-new-azure-mobile-app-backend"></a>Skapa en ny mobilappsserverdel i Azure

Skapa en ny mobilappsserverdel genom att följa instruktionerna nedan.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Skapa en databasanslutning och konfigurera projektet klient och server
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-client-project"></a>Kör klientprojektet

1. Öppna UWP-projektet.

2. Gå till den [Azure-portalen](https://portal.azure.com/) och navigera till den mobila appen som du skapade. På den `Overview` bladet letar du reda på URL: en som är den offentliga slutpunkten för din mobilapp. Exempel – sitename för min app name ”test123” kommer att https://test123.azurewebsites.net.

3. Öppna filen `App.xaml.cs` i den här mappen - windows-uwp-cs/ZUMOAPPNAME /. Programnamnet är `ZUMOAPPNAME`.

4. I `App` klass, Ersätt `ZUMOAPPURL` parameter med offentlig slutpunkt ovan.

    `public static MobileServiceClient MobileService = new MobileServiceClient("ZUMOAPPURL");`

    blir
    
    `public static MobileServiceClient MobileService = new MobileServiceClient("https://test123.azurewebsites.net");`

5. Tryck på F5 för att distribuera och köra appen.

6. I appen anger du en beskrivande text i textrutan **Infoga TodoItem**, till exempel *Slutför kursen* och sedan klickar du på **Spara**.

    ![Färdig Windows-snabbstartsapp på skrivbord](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    En POST-begäran skickas till den nya appserverdelen som finns på Azure.
