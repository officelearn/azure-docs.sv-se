---
title: Skapa en UWP-app
description: Följ den här kursen och kom igång med att använda serverdelar för mobilappar i Azure för utveckling av appar med den universella Windowsplattformen (UWP) i C#, Visual Basic eller JavaScript.
ms.assetid: 47124296-2908-4d92-85e0-05c4aa6db916
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 9188db19adab9bd46d65fc97f1c62b39141cee90
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461393"
---
# <a name="create-a-windows-app-with-an-azure-backend"></a>Skapa en Windows-app med en Azure-serverdel

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Översikt

I den här kursen får du lära dig hur du lägger till en molnbaserad serverdelstjänst i en app i den universella Windowsplattformen (UWP). Mer information om Mobile Apps finns [här](app-service-mobile-value-prop.md). Nedan visas skärmdumpar från den färdiga appen:

![Färdig skrivbordsapp](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)

Du måste slutföra den här kursen innan du går någon annan kurs om Mobilappar för UWP-appar.

## <a name="prerequisites"></a>Förutsättningar

Följande krävs för att kunna genomföra kursen:

* Ett aktivt Azure-konto. Om du inte har ett konto kan du registrera dig för en utvärderingsversion av Azure och få upp till tio mobilappar utan kostnad som du kan fortsätta att använda även efter utvärderingsperiodens slut. Mer information om den [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* Windows 10.
* Visual Studio Community 2017.
* Kunskap om UWP-apputveckling. Gå till [UWP-dokumentation](https://docs.microsoft.com/windows/uwp/) om du vill lära dig hur du [kommer igång](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) med att skapa UWP-appar.

## <a name="create-a-new-azure-mobile-app-backend"></a>Skapa en ny mobilappsserverdel i Azure

Skapa en ny mobilappsserverdel genom att följa instruktionerna nedan.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Skapa en databas anslutning och konfigurera klient-och Server projektet
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-client-project"></a>Kör klient projektet

1. Öppna UWP-projektet.

2. Gå till [Azure Portal](https://portal.azure.com/) och navigera till den mobilapp som du har skapat. På bladet `Overview` letar du reda på webb adressen som är den offentliga slut punkten för mobilappen. Exempel – webbplats namnet för mitt program namn "test123" kommer att https://test123.azurewebsites.net.

3. Öppna filen `App.xaml.cs` i den här mappen – Windows-UWP-CS/ZUMOAPPNAME/. Program namnet är `ZUMOAPPNAME`.

4. Ersätt `ZUMOAPPURL` parameter med den offentliga slut punkten ovan i `App`s klass.

    `public static MobileServiceClient MobileService = new MobileServiceClient("ZUMOAPPURL");`

    bli
    
    `public static MobileServiceClient MobileService = new MobileServiceClient("https://test123.azurewebsites.net");`

5. Tryck på F5 för att distribuera och köra appen.

6. I appen anger du en beskrivande text i textrutan *Infoga TodoItem*, till exempel **Slutför kursen** och sedan klickar du på **Spara**.

    ![Färdig Windows-snabbstartsapp på skrivbord](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    En POST-begäran skickas till den nya appserverdelen som finns på Azure.
