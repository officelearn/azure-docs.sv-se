## <a name="download-install-and-register-the-azure-backup-agent"></a>Hämta, installera och registrera på Azure Backup-agenten
När du har skapat Azure Backup-valvet, ska en agent installeras på var och en av dina Windows-datorer (Windows Server, Windows-klient, System Center Data Protection Manager-server eller Azure Backup Server-dator) som möjliggör säkerhetskopiering av data och program till Azure .

1. Logga in på den [hanteringsportalen](https://manage.windowsazure.com/)
2. Klicka på **återställningstjänster**, välj sedan det säkerhetskopieringsvalv som du vill registrera en server. Sidan Snabbstart för att säkerhetskopieringsvalvet visas.
   
    ![Snabbstart](./media/backup-install-agent/quickstart.png)
3. På sidan Snabbstart klickar du på den **för Windows Server eller System Center Data Protection Manager eller Windows-klienten** alternativ **hämta Agent**. Klicka på **spara** att kopiera den till den lokala datorn.
   
    ![Spara agent](./media/backup-install-agent/agent.png)
4. När agenten har installerats, dubbelklicka på MARSAgentInstaller.exe om du vill starta installationen av Azure Backup-agenten. Välj installationsmappen och tillfälliga mapp som krävs för agenten. Cacheplats som anges måste ha ledigt utrymme som är minst 5% av säkerhetskopierade data.
5. Om du använder en proxyserver för att ansluta till internet, i den **proxykonfiguration** anger information för proxy-server. Om du använder en autentiserad proxyserver måste du ange användarinformation namn och lösenord i den här skärmen.
6. Azure Backup-agenten installerar .NET Framework 4.5 och Windows PowerShell (om det inte är tillgänglig) om du vill slutföra installationen.
7. När agenten är installerad klickar du på den **fortsätta till registreringen** för att fortsätta med arbetsflödet.
   
   ![Registrera dig](./media/backup-install-agent/register.png)
8. Bläddra till och markera valvautentiseringsfilen som tidigare har hämtat på skärmen valvet autentiseringsuppgifter.
   
    ![Valvautentiseringsuppgifter](./media/backup-install-agent/vc.png)
   
    Valvautentiseringsfilen är endast giltig för 48 timmar (när den har hämtats från portalen). Om du påträffar eventuella fel i den här skärmen (t.ex. ”valvautentiseringsfilen som har upphört att gälla”), inloggning till Azure-portalen och hämta valvautentiseringsfilen igen.
   
    Se till att valvautentiseringsfilen finns på en plats som kan användas av installationsprogrammet. Om du får åtkomst till relaterade fel, kopiera valvautentiseringsfilen till en tillfällig plats på den här datorn och försök igen.
   
    Om det uppstår ett fel för ogiltiga autentiseringsuppgifter (t.ex. ”ogiltiga autentiseringsuppgifterna”) i filen är antingen skadad eller har inte har de senaste autentiseringsuppgifterna som är associerade med recovery-tjänsten. Försök igen när du har hämtat en ny valvautentiseringsfil från portalen. Det här felet visas vanligen om användaren klickar på den **Download valvautentiseringen** alternativ i Azure-portalen i snabb följd. I det här fallet gäller endast andra valvautentiseringsfilen.
9. I den **krypteringsinställning** skärmen, kan du antingen skapa en lösenfras eller ange en lösenfras (minst 16 tecken). Kom ihåg att spara lösenfrasen på en säker plats.
   
    ![Kryptering](./media/backup-install-agent/encryption.png)
   
   > [!WARNING]
   > Om de tappar bort lösenfrasen eller glömt; Microsoft går inte att återställa säkerhetskopierade data. Användaren äger krypteringslösenfrasen och Microsoft har inte insyn i den lösenfras som används av slutanvändaren. Spara filen på en säker plats när den behövs under en återställning.
   > 
   > 
10. När du klickar på den **Slutför** , datorn har registrerats i valvet och du är nu redo att börja säkerhetskopiera till Microsoft Azure.
11. När du använder fristående Microsoft Azure Backup kan du ändra de inställningar som anges under registreringen arbetsflödet genom att klicka på den **ändra egenskaper för** alternativ i Azure Backup mmc snapin-modulen.
    
    ![Ändra egenskaper för](./media/backup-install-agent/change.png)
    
    Du kan också när du använder Data Protection Manager, du kan ändra de inställningar som anges under registreringen arbetsflödet genom att klicka på den **konfigurera** alternativet genom att välja **Online** under **Management** fliken.
    
    ![Konfigurera Azure Backup](./media/backup-install-agent/configure.png)

