---
title: Skapa ett Microsoft Developer-konto | Azure Marketplace
description: Krav och anvisningar för att skapa ett Microsoft Developer-konto.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 161abde1ef0dfd86842fb56afe699fa632e0d9ba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80280379"
---
<a name="create-a-microsoft-developer-account"></a>Skapa ett Microsoft Developer-konto
====================================

Den här artikeln beskriver hur du blir en godkänd Microsoft-utvecklare för Azure Marketplace-publicering.

## <a name="create-a-microsoft-account"></a>Skapa ett Microsoft-konto

För att starta publicerings processen måste du slutföra registreringen av **Microsoft Developer Center** . Du använder samma registrerade konto på **[Cloud Partner Portal](https://cloudpartner.azure.com/)** för att starta publicerings processen.

### <a name="general-account-guidelines"></a>Allmänna rikt linjer för konto

Vi rekommenderar att du bara har en Microsoft-konto för dina Azure Marketplace-erbjudanden. Detta konto bör inte vara speciellt för tjänster eller erbjudanden.

Adressen som utgör användar namnet bör finnas i din domän och kontrol leras av ditt IT-team. Alla publicerings relaterade aktiviteter bör göras via det här kontot.

>[!WARNING]
>Ord som "Azure" och "Microsoft" stöds inte för Microsoft-konto registrering. Undvik att använda dessa ord för att slutföra skapandet och registreringen av kontot.

### <a name="company-account-guidelines"></a>Rikt linjer för företags konto

Följ dessa rikt linjer om mer än en person behöver åtkomst till kontot genom att logga in med Microsoft-konto som öppnade kontot.

>[!IMPORTANT]
>Om du vill ge flera användare åtkomst till ditt dev Center-konto rekommenderar vi att du använder Azure Active Directory för att tilldela roller till enskilda användare. De kan komma åt kontot genom att logga in med sina individuella autentiseringsuppgifter för Azure AD. Mer information finns i [Hantera konto användare](https://docs.microsoft.com/windows/uwp/publish/manage-account-users).

-   Skapa din Microsoft-konto med hjälp av en e-postadress som tillhör\'företagets domän, men inte till en enskild person. Till exempel WindowsApps\@fabrikam.com.
-   Begränsa åtkomsten till den här Microsoft-konto till det minsta möjliga antalet utvecklare.
-   Konfigurera en distributions lista för företagets e-post som innehåller alla som behöver åtkomst till Developer-kontot och Lägg till den här e-postadressen i din säkerhets information. Detta gör att alla anställda i listan kan ta emot säkerhets koder vid behov och hantera din Microsoft-kontos säkerhets information. Om det inte är möjligt att konfigurera en distributions lista måste ägaren till det enskilda e-postkontot vara tillgänglig för åtkomst till och dela säkerhets koden när du uppmanas att göra det (till exempel när ny säkerhets information läggs till i kontot eller när den måste nås från en ny enhet.)
-   Lägg till ett företags telefonnummer som inte kräver ett tillägg och som är tillgängligt för viktiga grupp medlemmar.
-   I allmänhet ska utvecklare använda betrodda enheter för att logga in på ditt företags konto för utvecklare. Alla viktiga grupp medlemmar bör ha åtkomst till dessa betrodda enheter. Detta minskar behovet av att säkerhets koder skickas vid åtkomst till kontot.
-   Om du behöver tillåta åtkomst till kontot från en icke-betrodd dator begränsar du åtkomsten till högst fem utvecklare. Dessa utvecklare bör helst komma åt kontot från datorer som delar samma geografiska plats och nätverks plats.
-   Granska ofta ditt [företags säkerhets information](https://account.live.com/proofs/Manage) och se till att det är aktuellt.

>[!IMPORTANT]
>Ditt utvecklares konto bör främst nås från betrodda datorer. Detta är viktigt eftersom det finns en gräns för antalet koder som genereras per konto, per vecka. Den ger också den mest sömlösa inloggnings upplevelsen.
>
>Mer information finns i [rikt linjer och säkerhet för Developer-konto](https://msdn.microsoft.com/windows/uwp/publish/opening-a-developer-account#additional-guidelines-for-company-accounts).

### <a name="to-create-a-microsoft-account"></a>Skapa en Microsoft-konto

1.  Öppna en ny Chrome Incognito-eller Internet Explorer-webbläsarsession för att se till att du inte är inloggad på ett befintligt konto.
2.  Registrera e-postmeddelandet (med föregående rikt linjer) som en Microsoft-konto med hjälp av den här [länken](https://signup.live.com/signup.aspx). Slutför följande registrerings anvisningar:

    - När du registrerar ditt konto som en Microsoft-konto måste du ange ett giltigt telefonnummer för systemet för att skicka en konto verifierings kod som ett textmeddelande eller ett automatiserat anrop.
    - När du registrerar ditt konto som en Microsoft-konto måste du ange ett giltigt e-post-ID för att få ett automatiskt e-postmeddelande för konto verifiering.
    - Verifiera e-postadressen som skickas till DL.

    Nu är du redo att använda den nya Microsoft-konto i Microsoft Developer Center.

## <a name="register-your-account-in-microsoft-developer-center"></a>Registrera ditt konto i Microsoft Developer Center

Microsoft Developer Center används för att registrera företags informationen en gång. Av måste vara en giltig representant för företaget och måste ange sin personliga information som ett sätt att verifiera sin identitet. Personen som registrerar måste använda ett Microsoft-konto som delas för företaget **och samma konto måste användas i Cloud Partner Portal.** Du bör kontrol lera att företaget inte redan har ett Microsoft Developer Center-konto innan du försöker skapa ett. Under processen samlar vi in företags adress information, bank konto information och skatte information. Dessa kan vanligt vis erhållas från finans-eller affärs kontakter.

>[!IMPORTANT]
>Du måste slutföra följande komponenter för utvecklare av utvecklare för att gå igenom de olika stegen för att skapa och distribuera erbjudanden.

| Utvecklings profil     | För att starta utkast    | Mellanlagring       | Publicera en mall för kostnads fria och lösningar   | Publicera kommersiell   |
|---------------------- |----------------   |-----------    |-------------------------------------  |---------------------  |
| Företags registrering  | Måste ha         | Måste ha     | Måste ha                             | Måste ha             |
| ID för skatte profil        | Valfri          | Valfri      | Valfri                              | Måste ha             |
| Bank konto          | Valfri          | Valfri      | Valfri                              | Måste ha             |

>[!NOTE]
>Det finns bara stöd för att ta med din egen licens (BYOL) för virtuella datorer och det anses vara ett kostnads fritt erbjudande.

### <a name="register-your-company-account"></a>Registrera ditt företags konto

1. Öppna en ny Internet Explorer InPrivate-eller Chrome Incognito-webbläsarsession för att se till att du inte är inloggad på ett personligt konto.

2. Gå till [Windows Dev Center](https://dev.windows.com/registration?accountprogram=azure) för att registrera dig som säljare. Läs följande viktiga kommentar innan du fortsätter.

   ![Microsoft-konto verifiering](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-verify.jpg)

    >[!IMPORTANT]
    >Se till att e-post-ID: t eller distributions listan (en distributions lista rekommenderas för att ta bort beroendet från individer) som du kommer att använda för registrering i dev Center är den första registrerade som en Microsoft-konto. Annars kan du registrera dig med hjälp av den här länken. Dessutom går det inte att använda alla e-post-ID: n under Microsofts företags domän för dev Center-registrering.

   ![Dev Center-inloggning](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-login.jpg)

3. Kör guiden "hjälpa oss skydda ditt konto" för att verifiera din identitet med hjälp av ett telefonnummer eller en e-postadress.

4. I registrerings konto information väljer du ditt **konto land/region** i list rutan och väljer sedan **Nästa**.

   ![Välj land/region](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_04.png)

    >[!WARNING]
    >"Sälja från" länder/regioner: för att sälja dina tjänster på Azure Marketplace måste din registrerade enhet vara från ett av de godkända "sälja-från"-länderna/-regionerna som visas i list rutan. Den här begränsningen gäller för utbetalnings-och beskattnings skäl. Mer information finns i deltagar principerna för Marketplace.

5. Välj **företag** som "konto typ" och välj sedan **Nästa**.

    >[!IMPORTANT]
    >För att bättre förstå konto typer och bestämma vilken typ som passar dig bäst, kan du Visa sid konto typer, platser och avgifter som visas i nästa skärm bild.

    ![Konto typer för säljare](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_05.png)

6. Ange **visnings namnet för utgivaren**. Detta är vanligt vis namnet på ditt företag.

    >[!NOTE]
    >Utgivarens visnings namn som anges i dev Center visas inte i Azure Marketplace när ditt erbjudande visas. Men den här informationen behövs för att slutföra registrerings processen.

7. Ange **kontakt information** för konto verifieringen.

    >[!IMPORTANT]
    >Du måste ange korrekt kontakt information eftersom den kommer att användas i vår verifierings process för att ditt företag ska godkännas i Developer Center.

8. Ange kontakt information för **företagets god kännare**. Företagets god kännare är den person som kan verifiera att du har behörighet att skapa ett konto i dev Center för din organisations räkning. När du har angett den här informationen väljer du **Nästa** för att gå till **avsnittet betalning**.

    ![Identifiera företags god kännare](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_08.png)

9. Ange betalnings information för ditt konto. Om du har en kampanj kod som täcker kostnaden för registrering kan du ange den här. Annars anger du kreditkorts information (eller PayPal på marknader som stöds). Välj **Nästa** för att gå vidare till den slutliga **granskningen**.

   ![Betalnings registrering](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_09.png)

10. Granska konto informationen och kontrol lera att allt är korrekt. Läs och godkänn villkoren i [Microsoft Azure Marketplace utgivar avtalet](https://go.microsoft.com/fwlink/?LinkID=699560). Markera kryss rutan om du vill visa att du har läst och accepterat dessa villkor.

11. Bekräfta registreringen genom att klicka på **Slutför** . Ett bekräftelse meddelande skickas till din e-postadress.

12. Om du planerar att bara publicera kostnads fria erbjudanden väljer du [gå till Cloud Partner Portal](https://cloudpartner.azure.com/) och hoppar till "Registrera ditt konto i Cloud Partner Portal" i den här artikeln.

### <a name="commercial-offers"></a>Kommersiella erbjudanden

Om du planerar att publicera kommersiella erbjudanden, till exempel ett erbjudande om virtuell dator med en fakturerings modell per timme, måste du ange information om skatt och bank. Logga in på ditt Developer Center-konto och välj **Uppdatera din konto information**. Följ anvisningarna i nästa avsnitt, "Lägg till bank och skatte information".

>[!IMPORTANT]
>Du kan inte skicka ett kommersiellt erbjudande till produktion utan att tillhandahålla bank konto och skatte information.

Om du vill uppdatera bank-och skatte informationen senare kan du gå vidare till "Registrera ditt konto i Cloud Partner Portal" i den här artikeln.

>[!NOTE]
>Vi rekommenderar att du ger bank konto-och skatte information så snart som möjligt eftersom det tar tid att validera skatte information.

### <a name="add-banking-and-tax-information"></a>Lägg till bank och skatte information

Om du vill publicera kommersiella erbjudanden för köp måste du lägga till utbetalnings-och skatte information och skicka den för verifiering i Developer Center.

**Ange bank information**

1.  Logga in på [Microsoft Developer Center](https://dev.windows.com/registration?accountprogram=azure) med din Microsoft-konto.
2.  Välj **utbetalnings konto** i den vänstra menyn, under **Välj betalnings sätt**, Välj **bank konto** eller **PayPal**.

    >[!NOTE]
    >Om du har kommersiella erbjudanden som kunderna köper i Marketplace, är det här det konto där du får utbetalningar för dessa inköp.
3.  Ange betalnings information och välj sedan **Spara**.

    >[!IMPORTANT]
    >Om du behöver uppdatera eller ändra ditt utbetalnings konto följer du stegen ovan för att ersätta den aktuella informationen med den nya informationen.
    >
    >Att ändra ditt utbetalnings konto kan försena dina betalningar med upp till en betalnings cykel. Den här fördröjningen inträffar eftersom vi behöver verifiera konto ändringen, precis som vi gjorde när du först konfigurerade kontot för utbetalning. Du betalar fortfarande för hela beloppet när ditt konto har verifierats. alla betalningar som förfaller för den aktuella betalnings cykeln läggs till nästa.

4.  Välj **Nästa**.

**Ange skatte information**

1.  Logga in på [Microsoft Developer Center](https://dev.windows.com/registration?accountprogram=azure) med din Microsoft-konto (om det behövs).
2.  Välj **skatte profil**på den vänstra menyn.
3.  På sidan **Konfigurera ditt skatte formulär** :
    - Välj land eller region där du har permanent placering.
    - Välj land eller region där du innehar ett primärt medborgarskap.
    - Välj **Nästa**.
4.  Ange din skatte information och välj sedan **Nästa**.

>[!WARNING]
>Du kan inte skicka till dina kommersiella erbjudanden till produktion utan att tillhandahålla bank konto och skatte information i ditt Microsoft Developer Center-konto.

### <a name="developer-center-registration-issues"></a>Registrerings problem för Developer Center

Om du har problem med registreringen av utvecklar Center kan du använda följande steg för att öppna ett support ärende.

1.  Gå till [support länken](https://developer.microsoft.com/windows/support).
2.  Under **kontakta oss**väljer du **skicka en incident**.
    ![Öppna en biljett](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_02.png)
3.  För **problem typ**väljer du "hjälp med dev Center" och för **kategorin**väljer du publicera och hantera appar. Välj **Start-e-post**.

    ![identifiera typ av problem](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_03.png)

4.  Du kommer att få en inloggnings sida. Använd alla Microsoft-konto för att logga in. Om du inte har någon Microsoft-konto [skapar du en](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1). \

5.  Ange detaljerad information om problemet och välj **Skicka** för att skicka biljetten.

    ![Skicka in en biljett](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_05.png)

## <a name="register-your-account-in-the-cloud-partner-portal"></a>Registrera ditt konto i Cloud Partner Portal

Du kan använda [Cloud Partner Portal](https://cloudpartner.azure.com/) för att publicera och hantera dina erbjudanden.

1.  Öppna en ny Chrome Incognito-eller Internet Explorer-webbläsarsession för att se till att du inte är inloggad på ett personligt konto.
2.  Gå till [Cloud Partner Portal](https://cloudpartner.azure.com/).
3.  Om du är en ny användare och loggar in på [Cloud Partner Portal](https://cloudpartner.azure.com/) för första gången måste du logga in med samma e-post-ID som är registrerat hos ditt dev Center-konto. Detta säkerställer att ditt dev Center-konto och moln Partner Portal kontot är kopplade till varandra.

Senare kan du lägga till andra medlemmar i företaget som arbetar med programmet. Du kan välja mellan deltagare eller ägare i Cloud Partner Portal genom att följa stegen i nästa avsnitt.

Om du har lagts till som deltagare/ägare i moln Partner Portal portalen kan du logga in med ditt eget konto.

>[!TIP]
>Deltagar principerna beskrivs på Azure-webbplatsen.

## <a name="manage-users-as-owners-or-contributors-in-the-cloud-partner-portal"></a>Hantera användare som ägare eller deltagare i Cloud Partner Portal

[Steg för att hantera användare på Cloud Partner Portal](./cloud-partner-portal-manage-users.md)


## <a name="next-steps"></a>Nästa steg

Nu när ditt konto har skapats och registrerats kan du starta publicerings processen för Azure Marketplace.
