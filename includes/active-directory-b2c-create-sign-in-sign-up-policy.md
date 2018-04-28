[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

I principavsnittet i inställningar väljer du **Registrerings- eller inloggningsprinciper** och klickar på **+ Lägg till**.

![Välj registrerings- eller inloggningsprinciper och klicka på Lägg till](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-policy.png)

Ange ett **namn** på principen för programmet. Ange till exempel `SiUpIn`.

Välj **Identitetsprovidrar** och markera **Registrering via e-post**. Du kan också välja leverantörer via sociala nätverk om det redan har konfigurerats. Klicka på **OK**.

![Välj Registrering via e-post och en identitetsprovider och klicka på OK](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-identity-providers.png)

Välj **Registreringsattribut**. Välj de attribut du vill samla in från konsumenten under registreringen. Välj till exempel **land/region**, **visningsnamn** och **postnummer**. Klicka på **OK**.

![Välj några attribut och klicka på OK](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-sign-up-attributes.png)

Välj **Programanspråk**. Välj anspråk som du vill ska returneras i de auktoriseringstoken som skickas tillbaka till programmet efter en genomförd registrering eller inloggning. Välj till exempel **visningsnamn**, **identitetsprovidrar**, **postnummer**, **ny användare** och **användarobjekt-id**.

![Välj några programanspråk och klicka på OK](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-application-claims.png)

Klicka på **Skapa** för att lägga till principen. Principen visas i listan som **B2C_1_SiUpIn**. Prefixet **B2C_1_** läggs till i namnet.

Öppna principen genom att välja **B2C_1_SiUpIn**. Kontrollera inställningarna i tabellen och klicka på **Kör nu**.

![Välj princip och kör den](media/active-directory-b2c-create-sign-in-sign-up-policy/run-b2c-signup-signin-policy.png)

| Inställning      | Värde  |
| ------------ | ------ |
| **Program** | Contoso B2C-app |
| **Välj svarswebbadress** | `https://localhost:44316/` |

En ny webbläsarflik öppnas och du kan kontrollera hur registreringen eller inloggningen går till.

> [!NOTE]
> Det kan ta någon minut att skapa en princip och innan uppdateringarna börjar gälla.
>