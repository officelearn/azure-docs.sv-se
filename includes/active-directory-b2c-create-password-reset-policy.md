Om du vill kunna aktivera detaljerad lösenordsåterställning i programmet måste du skapa en princip för återställning av lösenord. Observera att alternativet för lösenordsåterställning för hela klientorganisationen beskrivs [här](../articles/active-directory-b2c/active-directory-b2c-reference-sspr.md). Principen beskriver hur lösenordsåterställningen går till för konsumenterna och innehållet i de token som programmet tar emot vid genomförda inloggningar.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

I principavsnittet i inställningar väljer du **Principer för lösenordsåterställning** och klickar på **+ Lägg till**.

![Välj registrerings- eller inloggningsprinciper och klicka på Lägg till](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-policy.png)

Ange ett **namn** på principen för programmet. Ange till exempel `SSPR`.

Välj **Identitetsprovidrar** och markera **Återställ lösenord med e-postadress**. Klicka på **OK**.

![Välj återställning av lösenord med e-postadress som identitetsprovider och klicka på OK](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-identity-providers.png)

Välj **Programanspråk**. Välj anspråk som du vill ska returneras i de auktoriseringstoken som skickas tillbaka till programmet efter en genomförd återställning av lösenord. Välj till exempel **Användarobjekt-id**.

![Välj några programanspråk och klicka på OK](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-application-claims.png)

Klicka på **Skapa** för att lägga till principen. Principen visas i listan som **B2C_1_SSPR**. Prefixet **B2C_1_** läggs till i namnet.

Öppna principen genom att välja **B2C_1_SSPR**. Kontrollera inställningarna i tabellen och klicka på **Kör nu**.

![Välj princip och kör den](media/active-directory-b2c-create-password-reset-policy/run-b2c-password-reset-policy.png)

| Inställning      | Värde  |
| ------------ | ------ |
| **Program** | Contoso B2C-app |
| **Välj svarswebbadress** | `https://localhost:44316/` |

En ny webbläsarflik öppnas och du kan kontrollera hur lösenordsåterställningen fungerar.

> [!NOTE]
> Det kan ta någon minut att skapa en princip och innan uppdateringarna börjar gälla.
>
