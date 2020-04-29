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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "67187244"
---
## <a name="set-up-the-development-environment"></a>Konfigurera utvecklingsmiljön

Det här avsnittet beskriver hur du konfigurerar utvecklings miljön. Detta innefattar att skapa en ASP.NET MVC-app, lägga till en anslutning för anslutna tjänster, lägga till en kontroll enhet och ange nödvändiga direktiv för namn områden.

### <a name="create-an-aspnet-mvc-app-project"></a>Skapa ett ASP.NET MVC-appaket

1. Öppna Visual Studio.

1. Från huvud menyn väljer du **fil** > **nytt** > **projekt**.

1. I dialog rutan **nytt projekt** väljer du webb program för **webb** > **ASP.net (.NET Framework)**. I fältet **namn** anger du **StorageAspNet**. Välj **OK**.

    ![Skärmbild av dialogrutan Nytt projekt](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. I dialog rutan **nytt ASP.NET-webbprogram** väljer du **MVC**och väljer sedan **OK**.

    ![Skärm bild av dialog rutan nytt ASP.NET-webbprogram](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Använda anslutna tjänster för att ansluta till ett Azure Storage-konto

1. Högerklicka på projektet i **Solution Explorer**.

1. I snabb menyn väljer du **Lägg till** > **ansluten tjänst**.

1. I dialog rutan **anslutna tjänster** väljer du **moln lagring med Azure Storage**.

    ![Skärm bild av dialog rutan anslutna tjänster](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. I dialog rutan **Azure Storage** väljer du det Azure Storage-konto som ska användas för den här självstudien. Om du vill skapa ett nytt Azure Storage-konto väljer du **skapa ett nytt lagrings konto**och fyller i formuläret. När du har valt ett befintligt lagrings konto eller skapat ett nytt, väljer du **Lägg till**. Visual Studio installerar NuGet-paketet för Azure Storage och en lagrings anslutnings sträng till **Web. config**.

1. I **Solution Explorer**högerklickar du på **beroenden**, väljer **Hantera NuGet-paket**och lägger till en NuGet-paket referens till den senaste versionen av Microsoft. Azure. ConfigurationManager.

> [!TIP]
> Information om hur du skapar ett lagrings konto med [Azure Portal](https://portal.azure.com)finns i [skapa ett lagrings konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).
>
> Du kan också skapa ett lagrings konto med hjälp av [Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md), [Azure CLI](../articles/storage/common/storage-azure-cli.md)eller [Azure Cloud Shell](../articles/cloud-shell/overview.md).
