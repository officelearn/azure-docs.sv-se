---
title: Skapa ett Microsoft Developer-konto | Microsoft Docs
description: Krav och anvisningar för att skapa ett Microsoft Developer-konto.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: bdcd1d0cc9f9979d898410180d364716b344d641
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48811545"
---
<a name="create-a-microsoft-developer-account"></a>Skapa ett Microsoft Developer-konto
====================================

Den här artikeln beskriver hur du blir en godkänd Microsoft Developer för Azure Marketplace-publicering.

## <a name="create-a-microsoft-account"></a>Skapa ett Microsoft-konto

Om du vill starta publiceringsprocessen, måste du slutföra de **Microsoft Developer Center** registrering. Du använder samma konto som är registrerade på den **[Cloud Partner Portal](https://cloudpartner.azure.com/)** att starta publiceringsprocessen.

### <a name="general-account-guidelines"></a>Allmänna kontot riktlinjer

Vi rekommenderar att du bara har ett Microsoft-konto för Azure Marketplace-erbjudanden. Det här kontot får inte vara specifika för tjänster eller erbjudanden.

Adressen som utgör användarnamnet bör finnas i din domän och skötas av ditt IT-team. Publicera relaterade aktiviteter bör göras via det här kontot.

>[!WARNING]
>Ord som ”Azure” och ”Microsoft” stöds inte för registrering av Microsoft. Undvik att använda dessa ord för att slutföra kontoskapandet och registreringsprocessen.

### <a name="company-account-guidelines"></a>Riktlinjer för företag-konto

Följ dessa riktlinjer om mer än en person ska kunna komma åt kontot genom att logga in med det Microsoft-konto som öppnats kontot.

>[!IMPORTANT]
>Om du vill ge flera användare åtkomst till ditt Dev Center-konto, bör du använda Azure Active Directory för att tilldela roller till enskilda användare. De kan komma åt kontot genom att logga in med sina person autentiseringsuppgifter för Azure AD. Mer information finns i [hantera kontoanvändare](https://docs.microsoft.com/windows/uwp/publish/manage-account-users).

-   Skapa ditt Microsoft-konto med hjälp av en e-postadress som tillhör företaget\'s domän, men inte till enskilda användare. Till exempel windowsapps\@fabrikam.com.
-   Begränsa åtkomsten till Microsoft-konto till det minsta möjliga antalet utvecklare.
-   Ställ in en distributionslista för företagets e-post som innehåller alla som behöver åtkomst till developer-konto och Lägg till e-postadressen till din säkerhetsinformation. Detta tillåter alla anställda på listan för att ta emot säkerhetskoder vid behov och hantera säkerhetsinformation för ditt Microsoft-konto. Om det inte är möjligt att ställa in en distributionslista, måste ägaren av enskilda e-postkontot vara tillgängliga för att komma åt och dela säkerhetskod när du uppmanas (till exempel när ny säkerhetsinformation har lagts till i kontot eller när den måste kunna nås från en ny enhet.)
-   Lägg till ett telefonnummer för företag som kräver inte ett tillägg och är tillgänglig för viktiga gruppmedlemmar.
-   Generellt sett ha utvecklare använda betrodda enheter för att logga in på ditt företags developer-konto. Alla viktiga teammedlemmar ska ha åtkomst till dessa betrodda enheter. Detta minskar behovet av säkerhetskoder som ska skickas vid åtkomst till kontot.
-   Om du vill tillåta åtkomst till kontot från en icke betrodd dator kan du begränsa att åtkomsten till högst fem utvecklare. Utvecklarna bör helst åtkomst till kontot från datorer som delar samma geografiska och nätverksplats.
-   Ofta granska din [företags säkerhetsinformation](https://account.live.com/proofs/Manage) att kontrollera att den hålls aktuell.

>[!IMPORTANT]
>Developer-konto ska nås främst från betrodda datorer. Detta är viktigt eftersom det finns en gräns för hur många koder som genererats per konto per vecka. Dessutom kan de flesta sömlös inloggning.
>
>Mer information finns i [ytterligare developer-konto riktlinjer och security](https://msdn.microsoft.com/windows/uwp/publish/opening-a-developer-account#additional-guidelines-for-company-accounts).

### <a name="to-create-a-microsoft-account"></a>Skapa ett Microsoft-konto

1.  Öppna en ny Chrome Incognito eller Internet Explorer InPrivate-webbläsarsession för att säkerställa att du inte är inloggad till ett befintligt konto.
2.  Registrera e-postmeddelandet (med hjälp av föregående riktlinjer) som ett Microsoft-konto med den här [länk](https://signup.live.com/signup.aspx). Slutför följande registrera instruktioner:

    - Du måste ange ett giltigt telefonnummer för systemet för att skicka en Verifieringskod-konto som ett SMS eller ett automatiskt samtal när du registrerar ditt konto som ett Microsoft-konto.
    - När du registrerar ditt konto ett Microsoft-konto, måste du ange en giltig e-id för automatiserade e-post för Kontoverifiering.
    - Kontrollera den e-postadress som skickas till i Distributionslistan.

    Du är nu redo att använda det nya Microsoft-kontot i Microsoft Developer Center.

## <a name="register-your-account-in-microsoft-developer-center"></a>Registrera ditt konto i Microsoft Developer Center

Microsoft Developer Center för att registrera företagsinformation en gång. Av måste vara en giltig representant från företaget och måste ange sin personliga information som ett sätt att verifiera sin identitet. Den person som registrerar måste använda ett Microsoft-konto som delas för företaget, **och samma konto måste användas i partnerportalen i molnet.** Du bör kolla om ditt företag redan har ett Microsoft Developer Center-konto innan du försöker skapa en. Under processen kan vi samla in företagets adressuppgifter, Bankkontouppgifter, och skatteinformation. Denna information kan du vanligtvis få från finans- eller affärsavdelningen.

>[!IMPORTANT]
>Du måste slutföra följande komponenter i Developer-profil för att utföra olika faser i erbjudandet skapande och distribution.

| Utvecklarprofil     | Starta utkast    | Mellanlagring       | Publicera utan kostnad och lösningsmallen   | Publicera kommersiella   |
|---------------------- |----------------   |-----------    |-------------------------------------  |---------------------  |
| Registrering av företagets  | Måste ha         | Måste ha     | Måste ha                             | Måste ha             |
| Skatt profil-ID        | Valfri          | Valfri      | Valfri                              | Måste ha             |
| Konto          | Valfri          | Valfri      | Valfri                              | Måste ha             |

>[!NOTE]
>Bring Your Own License (BYOL) stöds bara för virtuella datorer och betraktas som en gratisversion.

### <a name="register-your-company-account"></a>Registrera ditt företagskonto

1. Öppna en ny Internet Explorer InPrivate- eller Chrome Incognito-webbläsarsession för att säkerställa att du inte är inloggad på ett personligt konto.

2. Gå till den [Windows Dev Center](http://dev.windows.com/registration?accountprogram=azure) registrera dig själv som en säljare. Läs följande viktig information innan du fortsätter.

   ![Verifiering av Microsoft-konto](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-verify.jpg)

    >[!IMPORTANT]
    >Se till att e-post-id eller en distributionsplats listan (en distributionslista rekommenderas att ta bort beroendet från personer) som du kommer att använda för att registrera i Dev Center i först registreras som ett Microsoft-konto. Annars kan du sedan registrera med den här länken. Dessutom alla e-post-id under Microsoft företagets domän kan inte användas för registrering av Dev Center ”.

   ![Dev center inloggning](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-login.jpg)

3. Kör guiden ”Help us protect your account” för att verifiera din identitet med hjälp av ett telefonnummer eller e-postadress.

4. Registrering-kontoinformation, Välj din **konto land/region** från listrutan och välj sedan **nästa**.

   ![Välj land/region](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_04.png)

    >[!WARNING]
    >”” Försäljningsländer: för att kunna sälja dina tjänster på Azure Marketplace, registrerade entiteten måste vara från en av de godkända ”” försäljningsländer visas i listrutan. Den här begränsningen är payout och skatt skäl. Mer information finns i deltagandepolicyerna för Marketplace.

5. Välj **företagets** som din ”kontotyp” och välj sedan **nästa**.

    >[!IMPORTANT]
    >För att bättre förstå kontotyper och bestämma vilken typ som är bäst för dig, visa sidan kontotyper, platser och avgifter som visas i nästa skärmdump.

    ![Typer av konton för säljare](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_05.png)

6. Ange den **Publisher visningsnamn**. Detta är vanligtvis namnet på ditt företag.

    >[!NOTE]
    >Angivna i Dev Center publisher visningsnamnet visas inte i Azure Marketplace när erbjudandet har listats. Men den här informationen behövs för att slutföra registreringen.

7. Ange den **kontaktinformation** för verifiering för kontot.

    >[!IMPORTANT]
    >Du måste ange kontaktinformation eftersom det kommer att användas i vår verifieringsprocessen för ditt företag behöver godkännas i Developer Center ”.

8. Ange kontaktinformation för den **företagets godkännare**. Företagets godkännare är den person som kan verifiera att du har behörighet att skapa ett konto i Dev Center uppdrag av din organisation. När du anger den här informationen kan väljer **nästa** att flytta till den **avsnittet för betalning**.

    ![Identifiera företagets godkännare](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_08.png)

9. Ange betalningsinformation för ditt konto. Om du har en kampanjkod som täcker kostnaden för registrering kan ange du som här. I annat fall ger ditt kreditkort info (eller PayPal på marknader som stöds). Välj **nästa** att gå vidare till sista **granska**.

   ![Betalningsregistrering](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_09.png)

10. Granska din kontoinformation och bekräfta att allt är korrekt. Läs och Godkänn villkoren i den [Publiceringsavtalet för Microsoft Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=699560). Markera rutan som indikerar att du har läst och godkänt villkoren.

11. Välj **Slutför** att bekräfta din registrering. Ett bekräftelsemeddelande skickas till din e-postadress.

12. Om du planerar att bara publicera kostnadsfria erbjudanden, väljer [gå till Cloud Partner Portal](https://cloudpartner.azure.com/) och hoppa över att ”registrera ditt konto i cloud partner portal” i den här artikeln.

### <a name="commercial-offers"></a>Kommersiella erbjudanden

Du måste ange skatte- och bankuppgifter om du planerar att publicera kommersiella erbjudanden, till exempel ett erbjudande för virtuell dator med hjälp av ett timpris för fakturering modellen. Det genom att logga in på ditt Developer Center-konto och välj **uppdatera din kontoinformation**. Följ anvisningarna i nästa avsnitt, ”Lägg till bank och skatt information”.

>[!IMPORTANT]
>Du kan inte skicka ett erbjudande med kommersiella till produktion utan att ange konto- och skatteinformation.

Om du föredrar att uppdatera din bank- och skatteinformation senare kan hoppa du ”registrera ditt konto i cloud partner portal” i den här artikeln.

>[!NOTE]
>Vi rekommenderar att ange konto- och skatteinformation så snart som möjligt eftersom det tar tid att validera skatteinformation.

### <a name="add-banking-and-tax-information"></a>Lägg till bank- och skatteinformation

Om du vill publicera kommersiella erbjudanden för inköp, måste du lägga till betalnings och skatteinformation och skicka in för verifiering i Developer Center.

**Ange bankinformation**

1.  Logga in på den [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) med ditt Microsoft-konto.
2.  Välj **Payout konto** på den vänstra menyn under **Välj betalningssätt**väljer **konto** eller **PayPal**.

    >[!NOTE]
    >Om du har kommersiella erbjudanden som kunderna köper i Marketplace, är konton där du får payout för dessa inköp.
3.  Ange betalningsinformationen och välj sedan **spara**.

    >[!IMPORTANT]
    >Om du behöver uppdatera eller ändra ditt betalnings-konto, följer du föregående steg om du vill ersätta den aktuella informationen med den nya informationen.
    >
    >Ändra ditt betalnings-konto kan fördröja betalningarna av upp till en betalningsmetod cykel. Den här fördröjningen beror på att vi behöver verifiera ändringen av tjänstkontot, precis som vi gjorde när du först ställa in betalnings-konto. Du kommer fortfarande få betalt för för hela beloppet när ditt konto har verifierats; alla betalningar på grund av för den aktuella betalningen cykel kommer att läggas till nästa.

4.  Välj **Nästa**.

**Ange skatteinformation**

1.  Logga in på den [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) med ditt Microsoft-konto (vid behov).
2.  På menyn till vänster väljer **skatt profil**.
3.  På den **ställer in formulärets skatt** sidan:
    - Välj land eller region där du har permanent datahemvist.
    - Välj land eller region där du håller primära medborgarskap.
    - Välj **Nästa**.
4.  Ange dina skatteuppgifter och välj sedan **nästa**.

>[VARNING!] Du kommer inte att kunna skicka till dina kommersiella erbjudanden till produktion utan att ange konto- och skatteinformation i Microsoft Developer Center-konto.

### <a name="developer-center-registration-issues"></a>Developer Center registreringsproblem

Om du har problem med registrering av Developer Center, kan du använda följande steg för att öppna ett supportärende.

1.  Gå till den [supportlänk](https://developer.microsoft.com/windows/support).
2.  Under **Kontakta oss**väljer **ett ärende**.
    ![Skapa ett ärende](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_02.png)
3.  För **problemtyp**, Välj ”Help med Dev Center” och för **kategori**, välj ”Publicera och hantera appar”. Välj **starta e-post**.

    ![identifiera typ av problem](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_03.png)

4.  Du kommer att få ett tecken på sidan. Använda ett Microsoft-konto för att logga in. Om du inte har ett Microsoft-konto du [skapar ett](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1). \

5.  Ger detaljerad information om problemet och välj **skicka** att skicka biljetten.

    ![Skicka ett supportärende](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_05.png)

## <a name="register-your-account-in-the-cloud-partner-portal"></a>Registrera ditt konto i partnerportalen i molnet

Du använder den [Cloud Partner Portal](https://cloudpartner.azure.com/) publicera och hantera dina erbjudanden.

1.  Öppna en ny Chrome Incognito eller Internet Explorer InPrivate-webbläsarsession för att säkerställa att du inte är inloggad på ett personligt konto.
2.  Gå till [Cloud Partner Portal](https://cloudpartner.azure.com/).
3.  Om du är en ny användare och logga in på den [Cloud Partner Portal](https://cloudpartner.azure.com/) för första gången du måste logga sedan in med samma e-post-id som har registrerats med ditt Dev Center-konto. Detta garanterar att ditt Dev Center-konto och cloud partner portal-konto är kopplade till varandra.

Senare kan du lägga till andra medlemmar i företaget som arbetar med programmet. Du kan dem som deltagare eller ägare i partnerportalen i molnet genom att följa stegen i nästa avsnitt.

Om du har lagts till som deltagare/ägare i cloud partner portal-portalen, kan du logga in med ditt eget konto.

>[!TIP]
>Deltagandepolicyer för beskrivs på Azure-webbplatsen.

## <a name="manage-users-as-owners-or-contributors-in-the-cloud-partner-portal"></a>Hantera användare som ägare eller deltagare i partnerportalen i molnet

[Steg för att hantera användare på partnerportalen i molnet](./cloud-partner-portal-manage-users.md)


## <a name="next-steps"></a>Nästa steg

Nu när ditt konto skapas och registrerats, kan du börja på Azure marketplace publiceringsprocessen.
