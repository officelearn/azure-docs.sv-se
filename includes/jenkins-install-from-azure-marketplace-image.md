1. I webbläsaren och öppna den [Azure Marketplace-avbildning för Jenkins](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview).

1. Välj **Hämta nu IT**.

    ![Välj GIT IT nu för att starta installationsprocessen för Jenkins Marketplace-avbildning.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-get-it-now.png)

1. När du har granskat prisinformation information och villkoren, Välj **Fortsätt**.

    ![Jenkins Marketplace priser och villkor avbildningsinformationen.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-pricing-and-terms.png)

1. Välj **skapa** att konfigurera servern Jenkins i Azure-portalen. 

    ![Installera Jenkins Marketplace-avbildningen.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-create.png)

1. I den **grunderna** anger du följande värden:

    - **Namnet** -ange `Jenkins`.
    - **Användaren** -ange ett användarnamn som ska användas när du loggar in på den virtuella datorn som kör Jenkins.
    - **Autentiseringstypen** – Välj **lösenord**.
    - **Lösenord** -ange lösenordet som ska användas när du loggar in på den virtuella datorn som kör Jenkins.
    - **Bekräfta lösenord** -ange lösenordet som ska användas när du loggar in på den virtuella datorn som Jenkins körs igen.
    - **Jenkins versionstyp** – Välj **LTS**.
    - **Prenumerationen** – Välj Azure-prenumeration som du vill installera Jenkins.
    - **Resursgruppen** – Välj **Skapa nytt**, och ange ett namn för resursgruppen som fungerar som en logisk behållare för insamling av resurser som utgör Jenkins installationen.
    - **Plats** – Välj **östra USA**.

    ![Ange autentiserings- och gruppinformation för Jenkins på fliken Grundläggande.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-basic.png)

1. Välj **OK** för att komma till den **inställningar** fliken. 

1. I den **inställningar** anger du följande värden:

    - **Storlek** -Välj lämplig storleksalternativ för den virtuella datorn Jenkins.
    - **VM disktyp** – ange antingen Hårddisk (hårddiskenheten) eller SSD (SSD) som anger vilken lagringstyp som disk är tillåten för den virtuella datorn Jenkins.
    - **Offentliga IP-adressen** -IP-adressnamn som standard Jenkins namnet du angav i föregående sida med suffixet - IP. Du kan välja alternativet för att ändra som standard.
    - **Domännamnet** -ange värdet för den fullständiga URL-Adressen till den virtuella datorn Jenkins.

    ![Ange inställningar för virtuell dator för Jenkins på fliken Inställningar.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-settings.png)

1. Välj **OK** för att komma till den **sammanfattning** fliken.

1. När den **sammanfattning** visar, informationen har verifierats. När du ser den **validering godkänd** meddelandet markerar **OK**. 

    ![Fliken Sammanfattning visar och verifierar dina valda alternativ.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-summary.png)

1. När den **skapa** visar, Välj **skapa** att skapa den virtuella datorn Jenkins. När servern är klar, visas ett meddelande i Azure-portalen.

    ![Jenkins är klar meddelande.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-notification.png)