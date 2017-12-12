## <a name="set-up-the-development-environment"></a>Konfigurera utvecklingsmiljön

Det här avsnittet går igenom Konfigurera utvecklingsmiljön, inklusive att skapa en ASP.NET MVC-app, lägger till en ansluten Services-anslutning, lägger till en domänkontrollant och att ange de nödvändiga namnområde direktiv.

### <a name="create-an-aspnet-mvc-app-project"></a>Skapa en ASP.NET MVC-app-projekt

1. Öppna Visual Studio.

1. Välj **Arkiv -> Ny -> projekt** från huvudmenyn

1. På den **nytt projekt** dialogrutan, ange alternativ som är markerade i följande bild:

    ![Skapa ASP.NET-projekt](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. Välj **OK**.

1. På den **nytt ASP.NET-projekt** dialogrutan, ange alternativ som är markerade i följande bild:

    ![Ange MVC](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

1. Välj **OK**.

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Använda anslutna Services för att ansluta till ett Azure storage-konto

1. I den **Solution Explorer**, högerklicka på projektet och på snabbmenyn väljer **Lägg till -> ansluten Service**.

1. På den **Lägg till ansluten tjänst** markerar **lagringsutrymmet i molnet med Azure Storage**, och välj sedan **konfigurera**.

    ![Dialogrutan för anslutna Service](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. På den **Azure Storage** dialogrutan Välj Azure Storage-konto som ska användas för den här kursen.  Om du vill skapa ett nytt Azure Storage-konto, klickar du på **skapa ett nytt Lagringskonto** och Fyll i formuläret.  När du väljer ett befintligt lagringskonto eller skapa en ny, klickar du på **Lägg till**.  Visual Studio ska installera NuGet-paketet för Azure Storage och en lagringsanslutningssträng till **Web.config**.

> [!TIP]
> Information om hur du skapar ett lagringskonto med den [Azure-portalen](https://portal.azure.com), se [skapa ett lagringskonto](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account).
>
> Ett Azure storage-konto kan även skapas med hjälp av [Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md), [Azure CLI](../articles/storage/common/storage-azure-cli.md), eller [Azure Cloud Shell](../articles/cloud-shell/overview.md).

