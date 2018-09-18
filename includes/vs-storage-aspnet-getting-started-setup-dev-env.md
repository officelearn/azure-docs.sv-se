---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 634143f56bc9134f240ff42b7b5989605c8bffde
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2018
ms.locfileid: "45979388"
---
## <a name="set-up-the-development-environment"></a>Konfigurera utvecklingsmiljön

Det här avsnittet beskriver hur du konfigurerar utvecklingsmiljön. Detta omfattar att skapa en ASP.NET MVC-app, lägger till en anslutning för anslutna tjänster, att lägga till en domänkontrollant och ange nödvändiga namnområde-direktiv.

### <a name="create-an-aspnet-mvc-app-project"></a>Skapa en ASP.NET MVC-app-projekt

1. Öppna Visual Studio.

1. På huvudmenyn väljer **filen** > **New** > **projekt**.

1. I den **nytt projekt** dialogrutan **Web** > **ASP.NET-Webbtillämpningsprogram (.NET Framework)**. I den **namn** fältet, anger du **StorageAspNet**. Välj **OK**.

    ![Dialogrutan Skärmbild av nytt projekt](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. I den **nytt ASP.NET-webbprogram** dialogrutan **MVC**, och välj sedan **OK**.

    ![Skärmbild av nytt ASP.NET-webbprogram dialogrutan](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Använd anslutna tjänster för att ansluta till ett Azure storage-konto

1. I **Solution Explorer**, högerklicka på projektet.

2. På snabbmenyn väljer **Lägg till** > **Connected Service**.

1. I den **Connected Services** dialogrutan **Molnlagring med Azure Storage**.

    ![Skärmbild av Connected Services dialogrutan](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. I den **Azure Storage** dialogrutan Välj Azure storage-kontonamnet som ska användas för den här självstudien. Om du vill skapa ett nytt Azure storage-konto, Välj **skapa ett nytt Lagringskonto**, och Fyll i formuläret. När du väljer ett befintligt lagringskonto eller skapa en ny, väljer **Lägg till**. Visual Studio installerar NuGet-paketet för Azure Storage och en anslutningssträng för lagring till **Web.config**.

> [!TIP]
> Lär dig hur du skapar ett lagringskonto med den [Azure-portalen](https://portal.azure.com), se [skapa ett lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).
>
> Du kan också skapa ett lagringskonto med hjälp av [Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md), [Azure CLI](../articles/storage/common/storage-azure-cli.md), eller [Azure Cloud Shell](../articles/cloud-shell/overview.md).

