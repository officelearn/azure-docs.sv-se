1. Kör det enhetliga installationsprogrammet.
2. I **innan du börjar**väljer **installera konfigurationsservern och processervern**.

    ![Innan du börjar](./media/site-recovery-add-configuration-server/combined-wiz1.png)

3. I **Third Party Software License** (Licens för programvara från tredje part) klickar du på **I Accept** (Jag accepterar) för att ladda ned och installera MySQL.

    ![Tredjepartsprogram](./media/site-recovery-add-configuration-server/combined-wiz2.png)
4. I **Registration** (Registrering) väljer du den registreringsnyckel som du hämtade från valvet.

    ![Registrering](./media/site-recovery-add-configuration-server/combined-wiz3.png)
5. I **Internet Settings** (Internetinställningar) anger du hur providern som körs på konfigurationsservern ska ansluta till Azure Site Recovery via internet. Kontrollera att du har tillåtit URL: er som krävs.

    - Om du vill ansluta till proxyservern som för närvarande har ställts in på datorn väljer **Anslut till Azure Site Recovery med proxyserver**.
    - Om du vill att providern ska ansluta direkt väljer **Anslut direkt till Azure Site Recovery utan proxyserver**.
    - Om den befintliga proxyservern kräver autentisering, eller om du vill använda en anpassad proxyserver för provideranslutningen, väljer **Anslut med anpassade proxyinställningar**, och ange adressen, porten och autentiseringsuppgifterna.
     ![Brandvägg](./media/site-recovery-add-configuration-server/combined-wiz4.png)
6. I **Kravkontroll** körs en kontroll för att se till att installationen kan köras. Om det visas en varning om **synkroniseringskontrollen för global tid** kontrollerar du att systemklockans tid (inställningarna för **datum och tid**) är samma som tidszonen.

    ![Förutsättningar](./media/site-recovery-add-configuration-server/combined-wiz5.png)
7. I **MySQL Configuration** (MySQL-konfiguration) skapar du autentiseringsuppgifter för att logga in på den MySQL-serverinstans som är installerad.

    ![MySQL](./media/site-recovery-add-configuration-server/combined-wiz6.png)
8. I **Miljöinformation** väljer du om du ska replikera virtuella VMwares-datorer. Om du kan sedan kontrollerar installationen att PowerCLI 6.0 är installerat.

    ![MySQL](./media/site-recovery-add-configuration-server/combined-wiz7.png)

9. I **Installationsplats** väljer du om du vill installera binärfilerna och lagra cachen. Enheten du väljer måste ha minst 5 GB tillgängligt utrymme, men vi rekommenderar en cacheenhet med 600 GB eller mer ledigt utrymme.

    ![Installationsplats](./media/site-recovery-add-configuration-server/combined-wiz8.png)
10. I **Val av nätverk** anger du lyssnare (nätverkskort och SSL-port) där konfigurationsservern skickar och tar emot replikeringsdata. Port 9443 är standardporten som används för att skicka och ta emot replikeringstrafik, men du kan ändra portnumret så att det passar din miljö. Förutom port 9443 öppnar vi också port 443, som används av en webbserver för att dirigera replikeringsåtgärder. Använd inte port 443 för att skicka eller ta emot replikeringstrafik.

    ![Val av nätverk](./media/site-recovery-add-configuration-server/combined-wiz9.png)


11. I **Sammanfattning** granskar du informationen och klickar på **Installera**. När installationen är klar skapas en lösenfras. Du behöver den när du aktiverar replikering. Kopiera lösenfrasen och förvara den på en säker plats.

    ![Sammanfattning](./media/site-recovery-add-configuration-server/combined-wiz10.png)

När registreringen är klar visas servern på bladet **Inställningar** > **Servrar** i valvet.
