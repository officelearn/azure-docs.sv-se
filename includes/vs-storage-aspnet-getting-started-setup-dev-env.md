## <a name="set-up-the-development-environment"></a>Konfigurera utvecklingsmiljön

Det här avsnittet vägleder dig ställa in din utvecklingsmiljö, inklusive att skapa en ASP.NET MVC-app, lägger till en ansluten Services-anslutning, lägger till en domänkontrollant och att ange de nödvändiga namnområde direktiv.

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

1. På den **Lägg till ansluten tjänst** markerar **Azure Storage**, och välj sedan **konfigurera**.

    ![Dialogrutan för anslutna Service](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. På den **Azure Storage** dialogrutan Välj önskad Azure storage-kontot som du vill arbeta och välj **Lägg till**.
