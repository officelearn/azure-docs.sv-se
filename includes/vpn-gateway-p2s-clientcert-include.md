Varje klientdator som ansluter till ett virtuellt nätverk med punkt-till-plats måste ha ett klientcertifikat installerat. Klientcertifikatet genereras från rotcertifikatet och installeras på varje klientdator. Autentiseringen misslyckas om ett giltigt klientcertifikat inte är installerat och klienten försöker ansluta till det virtuella nätverket.

Du kan antingen generera ett unikt certifikat för varje klient eller använda samma certifikat för flera klienter. Fördelen med att generera unika klientcertifikat är möjligheten att återkalla ett enskilt certifikat. Om flera klienter i stället använder samma klientcertifikat och du behöver återkalla det, så måste du generera och installera nya certifikat för alla klienter som använder certifikatet för att autentisera.

Du kan generera klientcertifikat på följande sätt:

- **Företagscertifikat:**

  - Om du använder en lösning för företagscertifikat genererar du ett klientcertifikat med det allmänna namnvärdesformatet name@yourdomain.com istället för formatet ”domännamn\användarnamn”.
  - Se till att klientcertifikatet baseras på certifikatmallen ”Användare” där ”Klientautentisering” är den första posten i användningslistan, i stället för Logga in med smartkort osv. Du kan kontrollera certifikatet genom att dubbelklicka på klientcertifikatet och visa **Information > Förbättrad nyckelanvändning**.

- **Självsignerat rotcertifikat:** Det är viktigt att du följer anvisningarna i någon av P2S-certifikatartiklarna nedan. I annat fall kommer inte det klientcertifikat du skapar att vara kompatibelt med P2S-anslutningar och klienterna får ett felmeddelande när de försöker ansluta. Med anvisningarna i någon av följande artiklar skapas ett kompatibelt klientcertifikat: 

  * [Windows 10 PowerShell-anvisningar](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert): För de här anvisningarna krävs Windows 10 och PowerShell för att skapa certifikat. Certifikaten som skapas kan installeras på valfri P2S-klient som stöds.
  * [MakeCert-anvisningar](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): Använd MakeCert om du inte har åtkomst till en Windows 10-dator för att skapa certifikat. MakeCert har tagits ur bruk men du kan fortfarande använda det för att skapa certifikat. Certifikaten som skapas kan installeras på valfri P2S-klient som stöds.

  När du skapar ett klientcertifikat från ett självsignerat rotcertifikat med hjälp av föregående anvisningar installeras det automatiskt på den dator som du använde för att skapa det. Om du vill installera klientcertifikatet på en annan klientdator måste du exportera det som .pfx tillsammans med hela certifikatkedjan. Då skapas en .pfx-fil som innehåller den rotcertifikatinformation som krävs för att autentisera klienten. Anvisningar för hur du exporterar ett certifikat finns i [Certifikat: exportera ett klientcertifikat](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport).