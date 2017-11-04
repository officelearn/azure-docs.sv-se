Organisationer som använder flera [programvara som en tjänst (SaaS)](https://azure.microsoft.com/overview/what-is-saas/) program för produktivitet eftersom molnteknik och verktyg blir mer tillgänglig. När antalet SaaS-appar växer, blir det svårt för administratörer att hantera konton och behörigheter och för användare att komma ihåg olika lösenord. Hantera programmen individuellt skapar extra arbete och är mindre säkert.

* Anställda som har att hålla reda på många lösenord tenderar att använda mindre säkra metoder för att spara dem, antingen skriva ned lösenord eller med samma lösenord för många konton.
* När en nyanställd anländer eller en lämnar måste sina konton vara individuellt etablerats eller tas bort.
* Dessutom anställda kan börja använda SaaS-appar för arbetet utan att gå via IT-avdelningen, vilket innebär att de skapar sina egna konton på system som IT-administratörer inte har godkänts och är inte övervakning.  

En lösning för alla dessa problem är enkel inloggning (SSO). Det är det enklaste sättet att hantera flera appar och ge användarna en konsekvent upplevelse inloggning. Azure Active Directory (AD Azure) är en robust lösning för enkel inloggning och har många tillgängliga redan integrerade program med självstudier för administratörer att snabbt konfigurera en ny app och börja etablera användare.

## <a name="how-does-azure-active-directory-integrate-apps"></a>Hur integrera appar i Azure Active Directory?
Azure AD kan du integrera dina appar och etablerade konton. Detta kan göras på något av två sätt.

* Om appen är redan integrerade i appen galleriet kan gå du igenom som portalen för att installera appar och konfigurera inställningar för att tillåta enkel inloggning. För alla Gallery-appar kan du sätta igång genom att följa enkla steg för steg-instruktionerna i appgalleriet och i Azure-portalen att aktivera enkel inloggning.
* Om appen inte är i galleriet, kan du ändå ställa in de flesta appar i Azure AD som en anpassad app. Detta kräver lite mer tekniska kunskaper för att konfigurera. Du kan lägga till alla program som stöder SAML 2.0 som en federerad app eller alla program som har en HTML-baserad-inloggningssida som lösenord SSO app.

I de fall där användarna har skapat sina egna konton för SaaS-appar som inte hanteras av IT-avdelningen, den [Cloud App Discovery](../articles/active-directory/active-directory-cloudappdiscovery-whatis.md) verktyget tillhandahåller en lösning. Det här verktyget övervakar Internet-trafik för att identifiera vilka appar som används i hela organisationen och antalet personer som använder dem. IT kan använda denna information för att lära sig vilka appar som användare föredrar och bestämma vilka att integrera i Azure AD för enkel inloggning.  

När du integrerar en app till Azure AD kan du mappa användarnas etablerade programmet identiteter till deras respektive identiteter med Azure AD.  

