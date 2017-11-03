### <a name="open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine"></a>Öppna TCP-portar i Windows-brandväggen för standardinstansen av databasmotorn
1. Anslut till den virtuella datorn med Fjärrskrivbord. Detaljerade instruktioner om hur du ansluter till den virtuella datorn finns i [Öppna den virtuella SQL-datorn med Fjärrskrivbord](../articles/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md#remotedesktop).
2. När du är inloggad skriver du **WF.msc** på Startskärmen. Tryck sedan på RETUR.
   
    ![Starta brandväggsprogrammet](./media/virtual-machines-sql-server-connection-steps/12Open-WF.png)
3. I **Windows-brandväggen med avancerad säkerhet** högerklickar du på **Regler för inkommande trafik** i den vänstra rutan. Klicka sedan på **Ny regel** i åtgärdsfönstret.
   
    ![Ny regel](./media/virtual-machines-sql-server-connection-steps/13New-FW-Rule.png)
4. I dialogrutan **Guiden Ny inkommande regel** väljer du **Port** under **Regeltyp**. Klicka sedan på **Nästa**.
5. Använd **TCP-standardporten** i dialogrutan **Protokoll och portar**. I rutan **Specifika lokala portar** anger du sedan portnumret för instansen av databasmotorn (**1433** för standardinstansen eller valet du gjorde för den privata porten i slutpunktssteget).
   
    ![TCP-port 1433](./media/virtual-machines-sql-server-connection-steps/14Port-1433.png)
6. Klicka på **Nästa**.
7. I dialogrutan **Åtgärd** markerar du **Tillåt anslutningen** och klickar på **Nästa**.
   
    **Säkerhetstips:** Du får ytterligare skydd om du markerar **Tillåt anslutningen om den är säker**. Välj det här alternativet om du vill konfigurera ytterligare säkerhetsalternativ i din miljö.
   
    ![Tillåta anslutningar](./media/virtual-machines-sql-server-connection-steps/15Allow-Connection.png)
8. Markera **Offentlig**, **Privat** och **Domän** i dialogrutan **Profil**. Klicka sedan på **Nästa**.
   
    **Säkerhetstips:**  Om du väljer **Offentlig** tillåts åtkomst via Internet. Välj alltid en mer restriktiv profil om det är möjligt.
   
    ![Offentlig profil](./media/virtual-machines-sql-server-connection-steps/16Public-Private-Domain-Profile.png)
9. Skriv ett namn och en beskrivning för regeln i dialogrutan **Namn** och klicka sedan på **Slutför**.
   
    ![Regelnamn](./media/virtual-machines-sql-server-connection-steps/17Rule-Name.png)

Öppna ytterligare portar för andra komponenter efter behov. Mer information finns i [Configuring the Windows Firewall to Allow SQL Server Access](http://msdn.microsoft.com/library/cc646023.aspx) (Konfigurera Windows-brandväggen för att tillåta SQL Server-åtkomst).

### <a name="configure-sql-server-to-listen-on-the-tcp-protocol"></a>Konfigurera SQL Server för att lyssna på TCP-protokollet

[!INCLUDE [Enable TCP](virtual-machines-sql-server-connection-tcp-protocol.md)]

### <a name="configure-sql-server-for-mixed-mode-authentication"></a>Konfigurera SQL Server för blandat läge-autentisering
SQL Server-databasmotorn kan inte använda Windows-autentisering utan domänmiljö. Om du vill ansluta till databasmotorn från en annan dator konfigurerar du SQL Server för blandat läge-autentisering. Med blandat läge-autentisering tillåts både SQL Server-autentisering och Windows-autentisering.

> [!NOTE]
> Det kanske inte är nödvändigt att konfigurera blandat läge-autentisering om du har konfigurerat ett virtuellt Azure-nätverk med en konfigurerad domänmiljö.
> 
> 

1. När du är ansluten till den virtuella datorn skriver du **SQL Server Management Studio** på startsidan och klickar sedan på den valda ikonen.
   
    Första gången du öppnar Management Studio måste programmet skapa en Management Studio-miljö för användarna. Det kan ta en stund.
2. Management Studio presenterar dialogrutan **Anslut till Server**. I rutan **Servernamn** skriver du namnet på den virtuella datorn som ska anslutas till databasmotorn med Object Explorer (i stället för namnet på den virtuella datorn kan du också använda **(lokal)** eller en punkt som **Servernamn**). Välj **Windows-autentisering** och lämna ***ditt_VM_namn*\din_lokala_administratör** i rutan **Användarnamn**. Klicka på **Anslut**.
   
    ![Anslut till server](./media/virtual-machines-sql-server-connection-steps/19Connect-to-Server.png)
3. Högerklicka på instansen av SQL Server (namnet på den virtuella datorn) i Object Explorer i SQL Server Management Studio. Klicka sedan på **Egenskaper**.
   
    ![Serveregenskaper](./media/virtual-machines-sql-server-connection-steps/20Server-Properties.png)
4. Markera **Läge för SQL Server- och Windows-autentisering** under **Serverautentisering** på sidan **Säkerhet**. Klicka sedan på **OK**.
   
    ![Välja autentiseringsläge](./media/virtual-machines-sql-server-connection-steps/21Mixed-Mode.png)
5. I dialogrutan SQL Server Management Studio klickar du på **OK** för att bekräfta kravet på att starta om SQL Server.
6. Högerklicka på din server i Object Explorer och klicka sedan på **Starta om**. (Om SQL Server Agent körs måste den också startas om.)
   
    ![Starta om](./media/virtual-machines-sql-server-connection-steps/22Restart2.png)
7. I dialogrutan SQL Server Management Studio klickar du på **Ja** för att bekräfta att du vill starta om SQL Server.

### <a name="create-sql-server-authentication-logins"></a>Skapa SQL Server-autentiseringsinloggningar
För att kunna ansluta till databasmotorn från en annan dator måste du skapa minst en SQL Server-autentiseringsinloggning.

1. I Object Explorer i SQL Server Management Studio expanderar du mappen för den serverinstans som du vill skapa den nya inloggningen i.
2. Högerklicka på mappen **Säkerhet**, peka på **Nytt** och välj **Inloggning...**.
   
    ![Ny inloggning](./media/virtual-machines-sql-server-connection-steps/23New-Login.png)
3. På sidan **Allmänt** i dialogrutan **Inloggning – ny** anger du namnet på den nya användaren i rutan **Inloggningsnamn**.
4. Välj **SQL Server-autentisering**.
5. Ange ett lösenord för den nya användaren i rutan **Lösenord**. Ange lösenordet på nytt i rutan **Bekräfta lösenord**.
6. Välj de tvingande alternativ som ska tillämpas för lösenord (**Tillämpa lösenordsprincip**, **Tillämpa giltighetstid för lösenord** och **Användaren måste ändra lösenordet vid nästa inloggning**). Om det är du själv som ska använda den här inloggningen behöver du inte kräva ändring av lösenordet vid nästa inloggning.
7. Välj en standarddatabas för inloggningen i listan **Standarddatabas**. **Master** är standard för det här alternativet. Om du inte har skapat någon användardatabas ännu använder du standardvärdet **Master**.
   
    ![Inloggningsegenskaper](./media/virtual-machines-sql-server-connection-steps/24Test-Login.png)
8. Om det här är den första inloggningen som du skapar kanske du vill utse inloggningen till SQL Server-administratör. I så fall markerar du **sysadmin** på sidan **Serverroller**.
   
   > [!NOTE]
   > Medlemmar i den fasta serverrollen sysadmin har fullständig kontroll över databasmotorn. Du bör noggrant begränsa medlemskapet i den här rollen.
   > 
   > 
   
   ![sysadmin](./media/virtual-machines-sql-server-connection-steps/25sysadmin.png)
9. Klicka på OK.

Mer information om SQL Server-inloggningar finns i [Skapa en inloggning](http://msdn.microsoft.com/library/aa337562.aspx).

