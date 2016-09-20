### Konfigurera en DNS-etikett för den offentliga IP-adressen

För att ansluta till SQL Server Database Engine från Internet, behöver du först konfigurera en DNS-etikett för din offentliga IP-adress.

> [AZURE.NOTE] DNS-etiketter krävs inte om du planerar att endast ansluta till SQL Server-instansen inom samma virtuella nätverk eller bara lokalt.

För att skapa en DNS-etikett, väljer du först **Virtuella datorer** i portalen. Välj din SQL Server-VM för att få fram egenskaperna.

1. I bladet för den virtuella datorn, väljer du din **offentliga IP-adress.**

    ![offentlig IP-adress](./media/virtual-machines-sql-server-connection-steps/rm-public-ip-address.png)

2. I egenskaperna för din offentliga IP-adress, expanderar du **Konfiguration**.

3. Ange ett namn för DNS-etiketten. Det här namnet är en A-post som kan användas för att ansluta till din SQL Server-VM via namn istället för direkt via IP-adress.

4. Klicka på knappen **Spara**.

    ![dns-etikett](./media/virtual-machines-sql-server-connection-steps/rm-dns-label.png)

### Anslut till databasmotorn från en annan dator

1. Öppna SQL Server Management Studio (SSMS) från en dator som är ansluten till Internet.

2. I dialogrutan **Anslut till server** eller **Anslut till databasmotor**, redigerar du värdet för **Servernamn**. Ange det fullständiga DNS-namnet för den virtuella datorn (bestäms i föregående åtgärd).

3. I rutan **Autentisering**, markerar du **SQL Server-autentisering**.

5. I rutan **Inloggning**, skriver du namnet på en giltig SQL-inloggning.

6. I rutan **Lösenord**, skriver du lösenordet för inloggningen.

7. Klicka på **Anslut**.

    ![ssms anslut](./media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png)



<!--HONumber=sep16_HO1-->


