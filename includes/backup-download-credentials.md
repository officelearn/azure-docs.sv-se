## <a name="using-vault-credentials-to-authenticate-with-the-azure-backup-service"></a>Med hjälp av autentiseringsuppgifter för valv för att autentisera med Azure Backup-tjänsten
Lokal server (Windows-klient eller server för Windows Server eller Data Protection Manager) måste autentiseras med ett säkerhetskopieringsvalv innan den kan säkerhetskopiera data till Azure. Autentisering uppnås med hjälp av ”valvet autentiseringsuppgifter”. Begreppet valvautentiseringsuppgifter liknar begreppet ”Publiceringsinställningar” fil som används i Azure PowerShell.

### <a name="what-is-the-vault-credential-file"></a>Vad är valvautentiseringsfilen?
Valvautentiseringsfilen är ett certifikat som genereras av portalen för varje säkerhetskopieringsvalv. Portalen överför sedan den offentliga nyckeln till Access Control Service (ACS). Den privata nyckeln för certifikatet görs tillgänglig för användaren som en del av arbetsflödet som angetts som indata i arbetsflöde för registrering av datorn. Detta autentiserar datorn för att skicka säkerhetskopierade data till ett identifierade valv i Azure Backup-tjänsten.

Valvautentiseringen används endast under registreringsarbetsflödet. Det är användarens ansvar att se till att valvautentiseringsfilen inte äventyras. Om den hamnar i händerna på en obehörig användare kan valvautentiseringsfilen användas för att registrera andra datorer mot samma valv. Men eftersom den säkerhetskopiera informationen krypteras med en lösenfras som hör till kunden, kan inte befintlig säkerhetskopierad data vara hotad. Valvautentiseringsuppgifter är inställda att gälla inom 48hrs för att minska risken för detta. Du kan ladda ned valvautentiseringsuppgifter av ett säkerhetskopieringsvalv valfritt antal gånger – men endast det senaste valvautentiseringsfilen gäller under registreringen av arbetsflödet.

### <a name="download-the-vault-credential-file"></a>Hämta valvautentiseringsfilen
Valvautentiseringsfilen hämtas via en säker kanal från Azure-portalen. Azure Backup-tjänsten inte känner till den privata nyckeln för certifikatet och den privata nyckeln beständig inte i portalen eller tjänsten. Använd följande steg för att hämta valvautentiseringsfilen till en lokal dator.

1. Logga in på den [hanteringsportalen](https://manage.windowsazure.com/)
2. Klicka på **återställningstjänster** i det vänstra navigeringsfönstret och välj det säkerhetskopieringsvalv som du har skapat. Klicka på ikonen molnet till vyn Snabbstart i säkerhetskopieringsvalvet.
   
   ![Snabb överblick](./media/backup-download-credentials/quickview.png)
3. På sidan Snabbstart **ladda ned valvautentiseringsuppgifter**. Portalen genererar valvautentiseringsfilen som görs tillgänglig för hämtning.
   
   ![Ladda ned](./media/backup-download-credentials/downloadvc.png)
4. Portalen kommer att generera en valvautentiseringen med hjälp av en kombination av valvnamnet och det aktuella datumet. Klicka på **spara** att hämta autentiseringsuppgifter för valv till det lokala kontot hämtningsmapp eller välj Spara som Spara-menyn för att ange en plats för autentiseringsuppgifter för valv.

### <a name="note"></a>Obs!
* Se till att valvautentiseringsuppgifter som sparas på en plats som kan nås från datorn. Om den är lagrad i en fil resursen/SMB Kontrollera åtkomstbehörigheten.
* Valvautentiseringsfilen används endast under registreringen av arbetsflödet.
* Valvautentiseringsfilen upphör att gälla efter 48hrs och kan hämtas från portalen.
* Referera till Azure Backup [vanliga frågor om](../articles/backup/backup-azure-backup-faq.md) för eventuella frågor om arbetsflödet.

