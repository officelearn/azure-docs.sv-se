### <a name="prepare-for-a-push-installation-on-a-linux-server"></a>Förbereda en push-installation på en Linux-server

1. Kontrollera att det finns en nätverksanslutning mellan Linux-datorn och processervern.
2. Skapa ett konto som processervern kan använda för att komma åt datorn. Kontot måste vara en **rotanvändare** på Linux-källservern. Använd det här kontot bara för push-installation och uppdateringar.
3. Kontrollera att /etc/hosts-filen på Linux-källservern innehåller poster som mappar det lokala värdnamnet till IP-adresser som är associerade med alla nätverkskort.
4. Installera de senaste openssh-, openssh-server- och openssl-paketen på den dator som du vill replikera.
5. Kontrollera att Secure Shell (SSH) är aktiverat och körs på port 22.
6. Aktivera autentisering med SFTP undersystemet och lösenord i filen sshd_config. Följ de här stegen:

    a. Logga in som **rot**.

    b. I den **/etc/ssh/sshd_config** filen, söka efter den rad som börjar med **PasswordAuthentication**.

    c. Ta bort kommentarerna raden och ändra värdet till **Ja**.

    d. Hitta raden som börjar med **undersystemet**, och Avkommentera raden.

      ![Linux](./media/site-recovery-prepare-push-install-mob-svc-lin/mobility2.png)

    e. Starta om **sshd**-tjänsten.

7. Lägg till kontot som du skapat i CSPSConfigtool. Följ de här stegen:

    a. Logga in på konfigurationsservern.

    b. Öppna **cspsconfigtool.exe**. Det är tillgängligt som en genväg på skrivbordet och i mappen %ProgramData%\home\svsystems\bin.

    c. På den **hantera konton** väljer **Lägg till konto**.

    d. Lägg till kontot som du skapade.

    d. Ange autentiseringsuppgifterna som du använder när du aktiverar replikering för en dator.
