Varje klientdator som ansluter till ett virtuellt nätverk med punkt-till-plats måste ha ett klientcertifikat installerat. Klientcertifikatet genereras från rotcertifikatet och installeras på varje klientdator. Autentiseringen misslyckas om ett giltigt klientcertifikat inte är installerat och klienten försöker ansluta till det virtuella nätverket.

Du kan antingen generera ett unikt certifikat för varje klient eller använda samma certifikat för flera klienter. Fördelen med att generera unika klientcertifikat är möjligheten att återkalla ett enskilt certifikat. Om flera klienter i stället använder samma klientcertifikat och du behöver återkalla det, så måste du generera och installera nya certifikat för alla klienter som använder certifikatet för att autentisera.

Du kan generera klientcertifikat på följande sätt:

- **Företagscertifikat:**

  - Om du använder en lösning för företagscertifikat genererar du ett klientcertifikat med det allmänna namnvärdesformatet name@yourdomain.com istället för formatet ”domännamn\användarnamn”.
  - Se till att klientcertifikatet baseras på certifikatmallen ”Användare” där ”Klientautentisering” är den första posten i användningslistan, i stället för Logga in med smartkort osv. Du kan kontrollera certifikatet genom att dubbelklicka på klientcertifikatet och visa *Information > Förbättrad nyckelanvändning*.

- **Självsignerat rotcertifikat:** Om du skapar ett klientcertifikat från ett självsignerat rotcertifikat med hjälp av instruktionerna i artikeln [Create a self-signed root certificate for Point-to-Site connections](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert) (Skapa ett självsignerat rotcertifikat för punkt-till-plats-anslutningar) installeras det automatiskt på den dator som du använde för att skapa det. Om du vill installera klientcertifikatet på en annan klientdator måste du exportera det. Följ anvisningarna i artikeln för att [exportera certifikatet](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport).