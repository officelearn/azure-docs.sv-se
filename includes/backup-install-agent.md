## <a name="download-install-and-register-the-azure-backup-agent"></a>Hämta, installera och registrera på Azure Backup-agenten
Installera säkerhetskopieringsagenten på alla dina Windows-datorer (Windows Server, Windows-klient, System Center Data Protection Manager-server eller Azure Backup Server-dator) som används för att säkerhetskopiera data till Azure när du har skapat Recovery Services-valvet.

1. Öppna din prenumeration i den [Azure Portal](https://ms.portal.azure.com/).
2. På den vänstra menyn väljer **alla tjänster** och Skriv i listan över tjänster **återställningstjänster**. Klicka på **Recovery Services-valv**.

   ![Öppna Recovery Services-valvet](../articles/backup/media/tutorial-backup-windows-server-to-azure/full-browser-open-rs-vault_2.png)
3. På sidan Snabbstart klickar du på den **för Windows Server eller System Center Data Protection Manager eller Windows-klienten** alternativ **hämta Agent**. Klicka på **spara** att kopiera den till den lokala datorn.
   
    ![Spara agent](./media/backup-install-agent/agent.png)
4. Dubbelklicka på MARSAgentInstaller.exe om du vill starta installationen av Azure Backup-agenten när agenten har installerats. Välj installationsmappen och tillfälliga mapp som krävs för agenten. Den angivna cacheplatsen bör minst 5% av säkerhetskopierade data som ledigt utrymme.
5. Om du använder en proxyserver för att ansluta till internet, i den **proxykonfiguration** anger information för proxy-server. Om du använder en autentiserad proxyserver måste du ange användarinformation namn och lösenord i den här skärmen.
6. Azure Backup-agenten installerar .NET Framework 4.5 och Windows PowerShell (om det inte är tillgänglig) om du vill slutföra installationen.
7. När agenten är installerad klickar du på den **fortsätta till registreringen** för att fortsätta med arbetsflödet.
   
   ![Registrera dig](./media/backup-install-agent/register.png)
8. På skärmen valvet autentiseringsuppgifter väljer du hämtade valvautentiseringsfilen.
   
    ![Valvautentiseringsuppgifter](./media/backup-install-agent/vc.png)
   
    Valvautentiseringsfilen är endast giltig för 48 timmar (när den har hämtats från portalen). Om du påträffar eventuella fel i den här skärmen (t.ex. ”valvautentiseringsfilen som har upphört att gälla”), inloggning till Azure-portalen och hämta valvautentiseringsfilen igen.
   
    Se till att installationsprogrammet kan komma åt valvautentiseringsfilen. Om du får åtkomst-relaterade fel, kopiera valvautentiseringsfilen till en tillfällig plats på den lokala datorn och försök igen.
   
    Om du stöter på ett ogiltigt valv autentiseringsuppgifter fel som ”ogiltiga autentiseringsuppgifter tillhandahålls”, valvet autentiseringsuppgifter filen är antingen skadad eller så finns den inte har de senaste autentiseringsuppgifterna som associeras med recovery-tjänsten. Försök igen när du har hämtat en ny valvautentiseringsfil från portalen. Det här felet normalt visas när användaren klickar på **Download valvautentiseringen** alternativ i Azure-portalen i snabb följd. När detta inträffar är endast andra valvautentiseringsfilen giltig.
9. I den **krypteringsinställning** skärmen, kan du antingen skapa en lösenfras eller ange en lösenfras (minst 16 tecken). Kom ihåg att spara lösenfrasen på en säker plats.
   
    ![Kryptering](./media/backup-install-agent/encryption.png)
   
   > [!WARNING]
   > Om de tappar bort lösenfrasen eller glömt; Microsoft går inte att återställa säkerhetskopierade data. Användaren äger krypteringslösenfrasen och Microsoft har inte insyn i den lösenfras som används av slutanvändaren. Spara filen på en säker plats när den behövs under en återställning.
   > 
   > 
10. När du klickar på den **Slutför** , datorn har registrerats i valvet och du är nu redo att börja säkerhetskopiera till Microsoft Azure.
11. När du använder fristående Microsoft Azure Backup kan du ändra de inställningar som anges under registreringen arbetsflödet genom att klicka på den **ändra egenskaper för** alternativet i Azure Backup MMC snapin-modulen.
    
    ![Ändra egenskaper för](./media/backup-install-agent/change.png)
    
    Du kan också när du använder Data Protection Manager, du kan ändra de inställningar som anges under registreringen arbetsflödet genom att klicka på den **konfigurera** alternativet genom att välja **Online** under **Management** fliken.
    
    ![Konfigurera Azure Backup](./media/backup-install-agent/configure.png)

