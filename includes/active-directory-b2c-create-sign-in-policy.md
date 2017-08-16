Du aktiverar inloggning till ditt program genom att först skapa en inloggningsprincip. Principen beskriver hur inloggningen går till för konsumenterna och innehållet i de token som programmet tar emot vid genomförda inloggningar.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)] Klicka på **Inloggningsprinciper**.

Klicka på **+Lägg till** överst på bladet.

**Namn** är namnet på programmets inloggningsprincip. Ange till exempel **SiIn**.

Klicka på **Identitetsprovidrar** och välj **Inloggning från lokalt konto**. Du kan också välja leverantörer via sociala nätverk om det redan har konfigurerats. Klicka på **OK**.

Klicka på **Programanspråk**. Här kan du välja anspråk som du vill ska returneras i de token som skickas tillbaka till programmet efter en genomförd inloggning. Välj till exempel **Visningsnamn**, **Identitetsprovider**, **Postnummer** och **Användarobjekt-id**. Klicka på **OK**.

Klicka på **Skapa**. Den nya principen visas som **B2C_1_SiIn** (fragmentet **B2C\_1\_** läggs till automatiskt) på bladet **Inloggningsprinciper**.

Öppna principen genom att klicka på **B2C_1_SiIn**.

Välj **Contoso B2C-app** i listrutan **Program** och `https://localhost:44321/` i listrutan **Svarswebbadress/omdirigerings-URI**.

Klicka på **Kör nu**. En ny webbläsarflik öppnas och du kan gå igenom inloggningsupplevelsen för ditt program.

> [!NOTE]
> Det kan ta någon minut att skapa en princip och innan uppdateringarna börjar gälla.
>