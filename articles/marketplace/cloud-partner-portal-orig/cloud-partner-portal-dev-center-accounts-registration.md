---
title: Skapa ett Microsoft Developer-konto | Azure Marketplace
description: Krav och steg för att skapa ett Microsoft Developer-konto.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 161abde1ef0dfd86842fb56afe699fa632e0d9ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280379"
---
<a name="create-a-microsoft-developer-account"></a>Skapa ett Microsoft Developer-konto
====================================

I den här artikeln beskrivs hur du blir en godkänd Microsoft-utvecklare för Azure Marketplace-publicering.

## <a name="create-a-microsoft-account"></a>Skapa ett Microsoft-konto

Om du vill starta publiceringsprocessen måste du slutföra registreringen av **Microsoft Developer Center.** Du använder samma registrerade konto på **[Cloud Partner Portal](https://cloudpartner.azure.com/)** för att starta publiceringsprocessen.

### <a name="general-account-guidelines"></a>Riktlinjer för allmänna konton

Vi rekommenderar att du bara har ett Microsoft-konto för dina Azure Marketplace-erbjudanden. Det här kontot ska inte vara specifikt för tjänster eller erbjudanden.

Adressen som utgör användarnamnet ska finnas på din domän och styras av IT-teamet. Alla publiceringsrelaterade aktiviteter bör göras via det här kontot.

>[!WARNING]
>Ord som "Azure" och "Microsoft" stöds inte för registrering av Microsoft-konton. Undvik att använda dessa ord för att slutföra processen för att skapa och registrera kontot.

### <a name="company-account-guidelines"></a>Riktlinjer för företagskonto

Följ dessa riktlinjer om fler än en person behöver komma åt kontot genom att logga in med det Microsoft-konto som öppnade kontot.

>[!IMPORTANT]
>Om du vill tillåta flera användare att komma åt ditt Dev Center-konto rekommenderar vi att du använder Azure Active Directory för att tilldela roller till enskilda användare. De kan komma åt kontot genom att logga in med sina individuella Azure AD-autentiseringsuppgifter. Mer information finns i [Hantera kontoanvändare](https://docs.microsoft.com/windows/uwp/publish/manage-account-users).

-   Skapa ditt Microsoft-konto med en e-postadress som tillhör företagets\'domän, men inte till en enskild person. Till exempel windowsapps\@fabrikam.com.
-   Begränsa åtkomsten till det här Microsoft-kontot till minsta möjliga antal utvecklare.
-   Skapa en företagsdistributionslista för e-post som innehåller alla som behöver komma åt utvecklarkontot och lägg till den här e-postadressen i din säkerhetsinformation. På så sätt kan alla anställda i listan ta emot säkerhetskoder när det behövs och hantera ditt Microsoft-kontos säkerhetsinformation. Om det inte är möjligt att konfigurera en distributionslista måste ägaren till det enskilda e-postkontot vara tillgänglig för åtkomst och delning av säkerhetskoden när du uppmanas att göra det (till exempel när ny säkerhetsinformation läggs till i kontot eller när den måste nås från en ny enhet.)
-   Lägg till ett företagstelefonnummer som inte kräver ett tillägg och som är tillgängligt för viktiga gruppmedlemmar.
-   I allmänhet har utvecklare använder betrodda enheter för att logga in på företagets utvecklarkonto. Alla viktiga gruppmedlemmar bör ha åtkomst till dessa betrodda enheter. Detta minskar behovet av att säkerhetskoder skickas när du öppnar kontot.
-   Om du behöver tillåta åtkomst till kontot från en icke-betrodd dator begränsar du åtkomsten till högst fem utvecklare. Helst bör dessa utvecklare komma åt kontot från datorer som delar samma geografiska plats och nätverksplats.
-   Granska ofta [företagets säkerhetsinformation](https://account.live.com/proofs/Manage) för att se till att den är aktuell.

>[!IMPORTANT]
>Ditt utvecklarkonto bör i första hand nås från betrodda datorer. Detta är viktigt eftersom det finns en gräns för antalet koder som genereras per konto, per vecka. Det möjliggör också den mest sömlösa inloggningsupplevelsen.
>
>Mer information finns i [ytterligare riktlinjer för utvecklarkonto och säkerhet](https://msdn.microsoft.com/windows/uwp/publish/opening-a-developer-account#additional-guidelines-for-company-accounts).

### <a name="to-create-a-microsoft-account"></a>Så här skapar du ett Microsoft-konto

1.  Öppna en ny webbläsarsession för Chrome Incognito eller Internet Explorer för att säkerställa att du inte är inloggad på ett befintligt konto.
2.  Registrera e-postmeddelandet (med de tidigare riktlinjerna) som ett Microsoft-konto med hjälp av den här [länken](https://signup.live.com/signup.aspx). Fyll i följande registreringsinstruktioner:

    - När du registrerar ditt konto som ett Microsoft-konto måste du ange ett giltigt telefonnummer för att systemet ska kunna skicka en kontoverifieringskod som ett sms eller ett automatiserat samtal.
    - När du registrerar ditt konto som ett Microsoft-konto måste du ange ett giltigt e-post-ID för att få ett automatiskt e-postmeddelande för kontoverifiering.
    - Verifiera e-postadressen som skickas till DL.

    Nu är du redo att använda det nya Microsoft-kontot i Microsoft Developer Center.

## <a name="register-your-account-in-microsoft-developer-center"></a>Registrera ditt konto i Microsoft Developer Center

Microsoft Developer Center används för att registrera företagsinformationen en gång. Registranten måste vara en giltig representant för företaget, och måste lämna sina personuppgifter som ett sätt att validera sin identitet. Den person som registrerar sig måste använda ett Microsoft-konto som delas för företaget **och samma konto måste användas i Cloud Partner Portal.** Du bör kontrollera att ditt företag inte redan har ett Microsoft Developer Center-konto innan du försöker skapa ett. Under processen samlar vi in företagets adressinformation, bankkontoinformation och skatteinformation. Dessa kan vanligtvis erhållas från ekonomi eller affärskontakter.

>[!IMPORTANT]
>Du måste slutföra följande utvecklarprofilkomponenter för att kunna gå igenom de olika faserna för att skapa och distribuera erbjudanden.

| Profil för utvecklare     | Så här startar du utkast    | Mellanlagring       | Publicera kostnadsfri och lösningsmall   | Publicera kommersiella   |
|---------------------- |----------------   |-----------    |-------------------------------------  |---------------------  |
| Företagsregistrering  | Måste ha         | Måste ha     | Måste ha                             | Måste ha             |
| Id för skatteprofil        | Valfri          | Valfri      | Valfri                              | Måste ha             |
| Bankkonto          | Valfri          | Valfri      | Valfri                              | Måste ha             |

>[!NOTE]
>Bring Your Own License (BYOL) stöds endast för virtuella datorer och anses vara ett gratis erbjudande.

### <a name="register-your-company-account"></a>Registrera ditt företagskonto

1. Öppna en ny Internet Explorer InPrivate- eller Chrome Incognito-webbläsarsession för att säkerställa att du inte är inloggad på ett personligt konto.

2. Gå till [Windows Dev Center](https://dev.windows.com/registration?accountprogram=azure) för att registrera dig som säljare. Läs följande viktiga anmärkning innan du fortsätter.

   ![Verifiering av Microsoft-konto](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-verify.jpg)

    >[!IMPORTANT]
    >Kontrollera att e-post-ID eller distributionslista (en distributionslista rekommenderas för att ta bort beroende från enskilda personer) som du kommer att använda för att registrera dig i Dev Center registreras först som ett Microsoft-konto. Om inte, vänligen registrera dig via denna länk. Dessutom kan inget e-post-ID under Microsoft-företagsdomänen inte användas för Dev Center-registrering.

   ![Logga in i Dev center](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-login.jpg)

3. Kör guiden "Hjälp oss att skydda ditt konto" för att verifiera din identitet med hjälp av ett telefonnummer eller en e-postadress.

4. I Registreringskontoinformation väljer du ditt **kontoland/din region** i listrutan och väljer sedan **Nästa**.

   ![Välj land/region](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_04.png)

    >[!WARNING]
    >"Sälj från"-länder/-regioner: För att kunna sälja dina tjänster på Azure Marketplace måste din registrerade enhet komma från ett av de godkända "sell-from"-länder/-regionerna som visas i listrutan. Denna begränsning är av utbetalnings- och skatteskäl. Mer information finns i Marketplaces policyer för deltagande på Marknaden.

5. Välj **Företag** som "Kontotyp" och välj sedan **Nästa**.

    >[!IMPORTANT]
    >Om du vill bättre förstå kontotyper och bestämma vilken typ som är bäst för dig kan du visa sidkontotyper, platser och avgifter som visas i nästa skärminspelning.

    ![Kontotyper för säljare](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_05.png)

6. Ange **visningsnamnet för Publisher**. Detta är vanligtvis namnet på ditt företag.

    >[!NOTE]
    >Utgivarens visningsnamn som anges i Dev Center visas inte på Azure Marketplace när erbjudandet har listats. Men denna information behövs för att slutföra registreringsprocessen.

7. Ange **kontaktinformationen** för kontoverifieringen.

    >[!IMPORTANT]
    >Du måste ange korrekt kontaktinformation eftersom den kommer att användas i vår verifieringsprocess för att ditt företag ska godkännas i Developer Center."

8. Ange kontaktinformationen för **företagsgodkännaren**. Företagsgodkänningspersonen är den person som kan verifiera att du har behörighet att skapa ett konto i Dev Center för din organisations räkning. När du har lämnat den här informationen väljer du **Nästa** om du vill gå till **avsnittet Betalning**.

    ![Identifiera företagets godkännare](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_08.png)

9. Ange betalningsinformation för ditt konto. Om du har en kampanjkod som täcker registreringskostnaden kan du ange det här. I annat fall kan du ange din kreditkortsinformation (eller PayPal på marknader som stöds). Välj **Nästa** om du vill gå vidare till den slutliga **granskningen**.

   ![Registrering av betalning](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_09.png)

10. Granska dina kontouppgifter och bekräfta att allt är korrekt. Läs och acceptera villkoren i [Microsoft Azure Marketplace Publisher Agreement](https://go.microsoft.com/fwlink/?LinkID=699560). Markera rutan för att ange att du har läst och accepterat dessa villkor.

11. Välj **Slutför** för att bekräfta din registrering. Ett bekräftelsemeddelande skickas till din e-postadress.

12. Om du planerar att bara publicera kostnadsfria erbjudanden väljer du [Gå till Cloud Partner Portal](https://cloudpartner.azure.com/) och hoppa till "Registrera ditt konto i molnpartnerportalen" i den här artikeln.

### <a name="commercial-offers"></a>Kommersiella erbjudanden

Om du planerar att publicera kommersiella erbjudanden, till exempel ett erbjudande om virtuell dator med hjälp av en faktureringsmodell per timme, måste du ange skatte- och bankinformation. Logga in på utvecklarcentrets konto och välja **Uppdatera kontoinformationen**. Följ instruktionerna i nästa avsnitt, "Lägg till bank- och skatteinformation".

>[!IMPORTANT]
>Du kommer inte att kunna driva ett kommersiellt erbjudande till produktion utan att tillhandahålla bankkonto och skatteinformation.

Om du föredrar att uppdatera din bank- och skatteinformation senare kan du hoppa till "Registrera ditt konto i molnpartnerportalen" i den här artikeln.

>[!NOTE]
>Vi rekommenderar att du tillhandahåller bankkonto- och skatteinformation så snart som möjligt eftersom det tar tid att validera skatteinformation.

### <a name="add-banking-and-tax-information"></a>Lägga till bank- och skatteinformation

Om du vill publicera kommersiella erbjudanden för köp måste du lägga till utbetalnings- och skatteinformation och skicka in den för validering i Developer Center.

**Så här tillhandahåller du bankinformation**

1.  Logga in på [Microsoft Developer Center](https://dev.windows.com/registration?accountprogram=azure) med ditt Microsoft-konto.
2.  Välj **Utbetalningskonto** i den vänstra menyn under **Välj betalningsmetod**väljer du **Bankkonto** eller **PayPal**.

    >[!NOTE]
    >Om du har kommersiella erbjudanden som kunder köper på Marketplace är det här kontot där du får utbetalning för dessa köp.
3.  Ange betalningsinformationen och välj sedan **Spara**.

    >[!IMPORTANT]
    >Om du behöver uppdatera eller ändra ditt utbetalningskonto följer du föregående steg för att ersätta den aktuella informationen med den nya informationen.
    >
    >Om du ändrar ditt utbetalningskonto kan du fördröja dina betalningar med upp till en betalningscykel. Den här förseningen beror på att vi måste verifiera kontoändringen, precis som vi gjorde när du först konfigurerade utbetalningskontot. Du får fortfarande betalt för hela beloppet efter att ditt konto har verifierats. Eventuella betalningar som förfaller till betalning för den aktuella betalningscykeln kommer att läggas till nästa.

4.  Välj **Nästa**.

**Så här tillhandahåller du skatteinformation**

1.  Logga in på [Microsoft Developer Center](https://dev.windows.com/registration?accountprogram=azure) med ditt Microsoft-konto (om det behövs).
2.  Välj **Skatteprofil**på menyn till vänster .
3.  På sidan **Konfigurera skatteformulär:**
    - Välj det land eller den region där du har permanent uppehållstillstånd.
    - Välj det land eller den region där du har primärt medborgarskap.
    - Välj **Nästa**.
4.  Ange dina skatteuppgifter och välj sedan **Nästa**.

>[!WARNING]
>Du kan inte skicka till dina kommersiella erbjudanden till produktion utan att tillhandahålla bankkonto- och skatteinformation i ditt Microsoft Developer Center-konto.

### <a name="developer-center-registration-issues"></a>Registreringsproblem för Developer Center

Om du har problem med Developer Center-registreringen följer du följande steg för att öppna en supportbiljett.

1.  Gå till [supportlänken](https://developer.microsoft.com/windows/support).
2.  Under **Kontakta oss**väljer du Skicka en **incident**.
    ![Öppna en biljett](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_02.png)
3.  För **problemtyp**väljer du "Hjälp med Dev Center" och för **Kategori**väljer du "Publicera och hantera appar". Välj **Starta e-post**.

    ![identifiera typ av problem](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_03.png)

4.  Du får en inloggningssida. Använd valfritt Microsoft-konto för att logga in. Om du inte har ett Microsoft-konto skapar du [ett](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).\

5.  Ge detaljerad information om problemet och välj **Skicka** för att skicka biljetten.

    ![skicka in en biljett](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_05.png)

## <a name="register-your-account-in-the-cloud-partner-portal"></a>Registrera ditt konto i molnpartnerportalen

Du använder [Cloud Partner Portal](https://cloudpartner.azure.com/) för att publicera och hantera dina erbjudanden.

1.  Öppna en ny webbläsarsession för Chrome Incognito eller Internet Explorer för att säkerställa att du inte är inloggad på ett personligt konto.
2.  Gå till [Cloud Partner Portal](https://cloudpartner.azure.com/).
3.  Om du är en ny användare och loggar in på [Cloud Partner Portal](https://cloudpartner.azure.com/) för första gången måste du logga in med samma e-post-ID som är registrerat med ditt Dev Center-konto. Detta säkerställer att ditt Dev Center-konto och molnpartnerportalkontot är länkade till varandra.

Senare kan du lägga till de andra medlemmarna i företaget som arbetar med programmet. Du kan dem som deltagare eller ägare i molnpartnerportalen genom att följa stegen i nästa avsnitt.

Om du läggs till som deltagare/ägare i molnpartnerportalportalen kan du logga in med ditt eget konto.

>[!TIP]
>Principer för deltagande beskrivs på Azure-webbplatsen.

## <a name="manage-users-as-owners-or-contributors-in-the-cloud-partner-portal"></a>Hantera användare som ägare eller deltagare i molnpartnerportalen

[Steg för att hantera användare på molnpartnerportalen](./cloud-partner-portal-manage-users.md)


## <a name="next-steps"></a>Nästa steg

Nu när ditt konto har skapats och registrerats kan du starta publiceringsprocessen för Azure Marketplace.
