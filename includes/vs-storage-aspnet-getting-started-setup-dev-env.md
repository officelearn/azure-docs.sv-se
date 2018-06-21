## <a name="set-up-the-development-environment"></a>Konfigurera utvecklingsmiljön

Det här avsnittet går igenom hur du konfigurerar utvecklingsmiljön. Detta omfattar att skapa en ASP.NET MVC-app, lägger till en anslutning för anslutna tjänster, att lägga till ett och ange de obligatoriska namnområde-direktiv.

### <a name="create-an-aspnet-mvc-app-project"></a>Skapa en ASP.NET MVC-app-projekt

1. Öppna Visual Studio.

1. Välj på huvudmenyn **filen** > **ny** > **projekt**.

1. I den **nytt projekt** dialogrutan **Web** > **ASP.NET-webbprogram (.NET Framework)**. I den **namn** anger **StorageAspNet**. Välj **OK**.

    ![Dialogrutan Skärmbild av nytt projekt](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. I den **nytt ASP.NET-webbprogram** dialogrutan **MVC**, och välj sedan **OK**.

    ![Skärmbild av nytt ASP.NET-webbprogram dialogrutan](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Använd anslutna tjänster för att ansluta till ett Azure storage-konto

1. I **Solution Explorer**, högerklicka på projektet.

2. På snabbmenyn Välj **Lägg till** > **ansluten Service**.

1. I den **anslutna tjänster** dialogrutan **lagringsutrymmet i molnet med Azure Storage**.

    ![Skärmbild av anslutna Services dialogrutan](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. I den **Azure Storage** dialogrutan Välj Azure storage-konto som ska användas för den här kursen. Om du vill skapa ett nytt Azure storage-konto, Välj **skapa ett nytt Lagringskonto**, och Fyll i formuläret. Efter att välja ett befintligt lagringskonto eller skapa en ny, Välj **Lägg till**. Visual Studio installerar NuGet-paketet för Azure Storage och en lagringsanslutningssträng till **Web.config**.

> [!TIP]
> Information om hur du skapar ett lagringskonto med den [Azure-portalen](https://portal.azure.com), se [skapa ett lagringskonto](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account).
>
> Du kan också skapa ett lagringskonto med hjälp av [Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md), [Azure CLI](../articles/storage/common/storage-azure-cli.md), eller [Azure Cloud Shell](../articles/cloud-shell/overview.md).

