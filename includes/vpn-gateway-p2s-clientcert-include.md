Varje klientdator som ansluter till ett virtuellt nätverk med punkt-till-plats måste ha ett klientcertifikat installerat. Klientcertifikatet genereras från rotcertifikatet och installeras på varje klientdator. Autentiseringen misslyckas om ett giltigt klientcertifikat inte är installerat och klienten försöker ansluta till det virtuella nätverket.

Du kan antingen generera ett unikt certifikat för varje klient eller använda samma certifikat för flera klienter. Fördelen med att generera unika klientcertifikat är möjligheten att återkalla ett enskilt certifikat. Om flera klienter i stället använder samma klientcertifikat och du behöver återkalla det, så måste du generera och installera nya certifikat för alla klienter som använder certifikatet för att autentisera.

Du kan generera klientcertifikat på följande sätt:

- **Företagscertifikat:**

  - Om du använder en lösning för företagscertifikat genererar du ett klientcertifikat med det allmänna namnvärdesformatet name@yourdomain.com istället för formatet ”domännamn\användarnamn”.
  - Se till att klientcertifikatet baseras på certifikatmallen ”Användare” där ”Klientautentisering” är den första posten i användningslistan, i stället för Logga in med smartkort osv. Du kan kontrollera certifikatet genom att dubbelklicka på klientcertifikatet och visa *Information > Förbättrad nyckelanvändning*.

- **Självsignerade rotcertifikat:** Följ dessa instruktioner för att generera ett klientcertifikat: [Skapa ett klientcertifikat med PowerShell och Windows 10](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert). Anvisningarna omfattar de parametrar som krävs för att skapa ett P2S-kompatibelt certifikat. När du skapar klientcertifikatet installeras den automatiskt på den dator som du använde för att skapa den. Om du vill installera klientcertifikatet på en annan klientdator måste du exportera det som .pfx tillsammans med hela certifikatkedjan. Detta skapar en .pfx-fil som du kan installera på din klientdator som innehåller den nödvändiga rotcertifikatinformationen. Följ anvisningarna för att [exportera certifikatet](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport).