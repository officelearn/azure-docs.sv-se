### <a name="prepare-for-a-push-installation-on-a-linux-server"></a>Förbereda en push-installation på en Linux-server

1. Kontrollera att det finns en nätverksanslutning mellan Linux-datorn och processervern.
2. Skapa ett konto som processervern kan använda för att komma åt datorn. Kontot måste vara en **rotanvändare** på Linux-källservern. (Använd bara det här kontot för push-installationen och för uppdateringar.)
3. Kontrollera att /etc/hosts-filen på Linux-källservern innehåller poster som mappar det lokala värdnamnet till IP-adresser som är associerade med alla nätverkskort.
4. Installera de senaste openssh-, openssh-server- och openssl-paketen på den dator som du vill replikera.
5. Kontrollera att Secure Shell (SSH) är aktiverat och körs på port 22.
6. Aktivera SFTP-undersystemet och lösenordsautentisering i sshd_config-filen:
  1.  Logga in som **rot**.
  2.  Hitta raden som börjar med **PasswordAuthentication** i filen /etc/ssh/sshd_config.
  3.  Ta bort kommentarerna på raden och ändra värdet till **yes**.
  4.  Hitta raden som börjar med **Subsystem** och ta bort den.

     ![Linux](./media/site-recovery-prepare-push-install-mob-svc-lin/mobility2.png)
  5. Starta om **sshd**-tjänsten.

7. Lägg till kontot som du skapat i CSPSConfigtool.
    1.  Logga in på konfigurationsservern.
    2.  Öppna **cspsconfigtool.exe**. (Det är tillgängligt som en genväg på skrivbordet och i mappen %ProgramData%\home\svsystems\bin.)
    3.  Klicka på **Lägg till konto** på fliken **Hantera konton**.
    4.  Lägg till kontot som du skapade. 
    5.  Ange autentiseringsuppgifterna som du använder när du aktiverar replikering för en dator.
