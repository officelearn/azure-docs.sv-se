Du aktiverar registrering till ditt program genom att först skapa en registreringsprincip. Principen beskriver hur registreringen går till för konsumenterna samt innehållet i de token som programmet tar emot vid genomförda registreringar.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Klicka på **Registreringsprinciper**.

Klicka på **+Lägg till** överst på bladet.

**Namn** är namnet på programmets registreringsprincip. Ange till exempel **SiUp**.

Klicka på **Identitetsprovidrar** och välj **Registrering via e-post**. Du kan också välja leverantörer via sociala nätverk om det redan har konfigurerats. Klicka på **OK**.

Klicka på **Registreringsattribut**. Här väljer du de attribut du vill samla in från konsumenten under registreringen. Välj till exempel **Land/region**, **Visningsnamn** och **Postnummer**. Klicka på **OK**.

Klicka på **Programanspråk**. Här kan du välja anspråk som du vill ska returneras i de token som skickas tillbaka till programmet efter en genomförd registrering. Välj till exempel **Visningsnamn**, **Identitetsprovidrar**, **Postnummer**, **Ny användare** och **Användarobjekt-id**.

Klicka på **Skapa**. Den nya principen visas som **B2C_1_SiUp** (fragmentet **B2C\_1\_** läggs till automatiskt) på bladet **Registreringsprinciper**.

Öppna principen genom att klicka på **B2C_1_SiUp**.

Välj **Contoso B2C-app** i listrutan **Program** och `https://localhost:44321/` i listrutan **Svarswebbadress/omdirigerings-URI**.

Klicka på **Kör nu**. En ny webbläsarflik öppnas och du kan gå igenom användarupplevelsen av registrering för ditt program.

> [!NOTE]
> Det kan ta någon minut att skapa en princip och innan uppdateringarna börjar gälla.
>