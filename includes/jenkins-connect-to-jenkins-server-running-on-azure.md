1. Navigera till den virtuella datorn Jenkins i webbläsaren. Eftersom Jenkins instrumentpanelen är tillgänglig via osäker HTTP, visas ett meddelande som anger att du behöver använda SSH tunnel till den virtuella datorn.

    ![Jenkins innehåller information som förklarar hur du använder SSH för att ansluta till den virtuella datorn Jenkins.](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-ssh-instructions.png)

1. Öppna ett bash eller terminal fönster med åtkomst till SSH.

1. Ange följande `ssh` kommandot, ersätter den  **&lt;användarnamn >** och  **&lt;domän >** platshållarna med värdena anges när du skapar Jenkins virtuell dator:

    ```bash
    ssh -L 127.0.0.1:8080:localhost:8080 <username>@<domain>.eastus.cloudapp.azure.com
    ```

1. Följ den `ssh` anvisningarna för att logga in på den virtuella datorn Jenkins.

1. Ange följande kommando för att hämta det ursprungliga lösenordet för upplåsning Jenkins.

    ```bash
    sudo cat /var/lib/jenkins/secrets/initialAdminPassword
    ```

1. Kopiera till Urklipp lösenord som visas i fönstret bash eller terminal.

1. I din webbläsare, navigerar du till `http://localhost:8080`.

1. Klistra in lösenordet som hämtats tidigare i den **administratörslösenord** och markerar **Fortsätt**.

    ![Jenkins lagrar ett initialt lösenord som du måste använda för att låsa upp den virtuella datorn Jenkins första gången du ansluter till den.](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-unlock.png)

1. Välj **installera plugin-program för föreslagna**.

    ![Jenkins kan du enkelt installera en mängd föreslagna plugin-program på initiala](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-customize.png)

1. På den **skapa första administratörsanvändare** , skapa administrativa användare och lösenord för Jenkins instrumentpanelen och väljer **spara och slutför**.

1. På den **Jenkins är klar!** Välj **börja använda Jenkins**.

    ![När Jenkins har installerats och konfigurerats för åtkomst, kan du starta använder den för att skapa och bygga jobb.](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-ready.png)