### <a name="prepare-for-a-push-installation-on-a-windows-computer"></a>Förbereda för en push-installation på en Windows-dator

1. Kontrollera att det finns en nätverksanslutning mellan Windows-datorn och processervern.
2. Skapa ett konto som processervern kan använda för att komma åt datorn. Kontot måste ha administratörsbehörighet (lokalt eller via domänadministratör). (Använd det här kontot bara för push-installation och agentuppdateringar.)

   > [!NOTE]
   > Om du inte använder ett domänkonto, inaktivera kontroll av fjärråtkomst till användare på den lokala datorn. Inaktivera fjärråtkomst till användaren kontrollen under registernyckeln HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System lägga till ett nytt DWORD-värde: **LocalAccountTokenFilterPolicy**. Ange värdet till **1**. Om du vill göra detta i en kommandotolk, kör du följande kommando:  
   `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
   >
   >
2. Välj i Windows-brandväggen på den dator som du vill skydda **tillåta en app eller funktion via brandväggen**. Aktivera **File and Printer Sharing** och **Windows Management Instrumentation (WMI)**. Du kan konfigurera brandväggsinställningar för datorer som tillhör en domän med hjälp av ett grupprincipobjekt (GPO).

   ![Brandväggsinställningar](./media/site-recovery-prepare-push-install-mob-svc-win/mobility1.png)

3. Lägg till kontot som du skapat i CSPSConfigtool.
    1.  Logga in på konfigurationsservern.
    2.  Öppna **cspsconfigtool.exe**. (Det är tillgängligt som en genväg på skrivbordet och i mappen %ProgramData%\home\svsystems\bin.)
    3.  På den **hantera konton** väljer **Lägg till konto**.
    4.  Lägg till kontot som du skapade.
    5.  Ange autentiseringsuppgifterna som du använder när du aktiverar replikering för en dator.
