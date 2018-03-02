När du har skapat ett självsignerat rotcertifikat, exportera rot certifikatets offentliga nyckel .cer-fil (inte den privata nyckeln). Du kommer senare att överföra filen till Azure. Följande steg hjälper dig att exportera .cer-filen för ditt självsignerade rotcertifikat:

1. Om du vill hämta en .cer-fil från certifikatet öppnar du **Hantera användarcertifikat**. Leta upp det självsignerade rotcertifikatet, som vanligtvis finns under ”Certifikat – aktuell användare\Personligt\Certifikat” och högerklicka. Klicka på **Alla aktiviteter** och klicka sedan på **Exportera**. **Guiden Exportera certifikat** öppnas.

  ![Exportera](./media/vpn-gateway-certificates-export-public-key-include/export.png)
2. Klicka på **Nästa** i guiden.

  ![Exportera certifikat](./media/vpn-gateway-certificates-export-public-key-include/exportwizard.png)
3. Välj **Nej, exportera inte den privata nyckeln** och klicka sedan på **Nästa**.

  ![Exportera inte den privata nyckeln](./media/vpn-gateway-certificates-export-public-key-include/notprivatekey.png)
4. På sidan **Filformat för export** väljer du **Base 64-kodad X.509 (. CER).** och klickar sedan på **Nästa**.

  ![Base64-kodad](./media/vpn-gateway-certificates-export-public-key-include/base64.png)
5. För **fil som ska exporteras**, **Bläddra** till den plats som du vill exportera certifikatet. För **Filnamn** anger du ett namn för certifikatfilen. Klicka sedan på **Nästa**.

  ![Bläddra](./media/vpn-gateway-certificates-export-public-key-include/browse.png)
6. Klicka på **Slutför** för att exportera certifikatet.

  ![Slutför](./media/vpn-gateway-certificates-export-public-key-include/finish.png)
7. Certifikatet har exporterats.

  ![Lyckades](./media/vpn-gateway-certificates-export-public-key-include/success.png)
8. Det exporterade certifikatet ser ut ungefär så här:

  ![Exporterat](./media/vpn-gateway-certificates-export-public-key-include/exported.png)
9. Om du öppnar det exporterade certifikatet med hjälp av anteckningar ser du något som liknar det här exemplet. Avsnittet i blått innehåller den information som överförs till Azure. Om ditt certifikat öppnas i anteckningar och ser inte ut ungefär så här, vanligtvis det innebär du exporterade inte den med hjälp av Base64-kodad X.509 (. CER)-format. Om du vill använda ett annat textredigeringsprogram förstå du dessutom att vissa redigerare kan leda till oönskade formatering i bakgrunden. Detta kan skapa problem när upp text från det här certifikatet till Azure.

  ![Öppna med anteckningar](./media/vpn-gateway-certificates-export-public-key-include/notepad.png)