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
ms.openlocfilehash: a7c696870e22e1692ca5ed778e47f8e4cc00615a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187244"
---
## <a name="set-up-the-development-environment"></a>Konfigurera utvecklingsmiljön

Det här avsnittet går igenom inrättandet av utvecklingsmiljön. Detta inkluderar att skapa en ASP.NET MVC-app, lägga till en ansluten tjänstanslutning, lägga till en styrenhet och ange de namnområdesdirektiv som krävs.

### <a name="create-an-aspnet-mvc-app-project"></a>Skapa ett ASP.NET MVC-appprojekt

1. Öppna Visual Studio.

1. Välj **Arkiv** > **nytt** > **projekt**på huvudmenyn .

1. Välj **Webb** > **ASP.NET webbprogram (.NET Framework)** i dialogrutan **Nytt projekt** . I fältet **Namn** anger du **StorageAspNet**. Välj **OK**.

    ![Skärmbild av dialogrutan Nytt projekt](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. I dialogrutan **Nytt ASP.NET webbprogram** väljer du **MVC**och väljer sedan **OK**.

    ![Skärmbild av dialogrutan Nytt ASP.NET webbprogram](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Använda anslutna tjänster för att ansluta till ett Azure-lagringskonto

1. Högerklicka på projektet i **Solution Explorer.**

1. Välj **Lägg till** > **ansluten tjänst**på snabbmenyn .

1. I dialogrutan **Anslutna tjänster** väljer du **Molnlagring med Azure Storage**.

    ![Skärmbild av dialogrutan Anslutna tjänster](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. I dialogrutan **Azure Storage** väljer du det Azure-lagringskonto som ska användas för den här självstudien. Om du vill skapa ett nytt Azure-lagringskonto väljer du **Skapa ett nytt lagringskonto**och fyller i formuläret. När du har valt ett befintligt lagringskonto eller skapat ett nytt väljer du **Lägg till**. Visual Studio installerar NuGet-paketet för Azure Storage och en lagringsanslutningssträng till **Web.config**.

1. Högerklicka på **Beroenden**i **Solution Explorer**och **lägg**till en NuGet-paketreferens till den senaste versionen av Microsoft.Azure.ConfigurationManager.

> [!TIP]
> Mer information om hur du skapar ett lagringskonto med [Azure-portalen](https://portal.azure.com)finns i [Skapa ett lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).
>
> Du kan också skapa ett lagringskonto med hjälp av [Azure PowerShell,](../articles/storage/common/storage-powershell-guide-full.md) [Azure CLI](../articles/storage/common/storage-azure-cli.md)eller Azure [Cloud Shell](../articles/cloud-shell/overview.md).
