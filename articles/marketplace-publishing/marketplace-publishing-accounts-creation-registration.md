---
title: Skapa och registrera publisher konto | Microsoft Docs
description: "Instruktioner för att skapa ett Microsoft Developer-konto så vid godkännande kommer du säljer olika erbjuder typer på Azure Marketplace."
services: Azure Marketplace
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
editor: 
ms.assetid: 5a2fe68d-2967-463f-8af6-42bed07e3eaa
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: mbaldwin
ms.openlocfilehash: da805bead3f6af4ebd0b4b94b30a51ff2abc62dd
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="create-a-microsoft-developer-account"></a>Skapa ett Microsoft Developer-konto
Den här artikeln vägleder dig genom de nödvändiga konto skapas och registreringen ska bli en godkända Microsoft Developer för Azure Marketplace.

## <a name="1-create-a-microsoft-account"></a>1. Skapa ett Microsoft-konto
Om du vill starta publiceringsprocessen, behöver du skapa ett Microsoft-konto. Det här kontot används för att registrera i både den **Microsoft Developer Center** och **Azure Publishing Portal**. Du bör ha endast ett Microsoft-konto för Azure Marketplace-erbjudanden. Det får inte vara specifika för tjänster eller erbjudanden.

Adressen som utgör användarnamnet ska finnas på din domän och styrs av din IT-teamet. Publishing relaterade aktiviteter ska utföras via det här kontot.

> [!WARNING]
> Ord som **”Azure”** och **”Microsoft”** stöds inte för registrering av Microsoft-konto. Undvik att använda dessa ord för att skapa ett konto och registreringsprocessen.
>
>

### <a name="guidelines-for-company-accounts"></a>Riktlinjer för företag
När du skapar ett företagskonto, följer du dessa riktlinjer om mer än en person behöver åtkomst till kontot genom att logga in med Microsoft-konto som har öppnat kontot.

> [!Important]
> Tillåt flera användare att komma åt ditt konto Dev Center viktigt att, bör du använda Azure Active Directory för att tilldela roller till enskilda användare som har åtkomst till kontot genom att logga in med sina individuella autentiseringsuppgifter för Azure AD. Mer information finns i [hantera kontoanvändare](https://msdn.microsoft.com/en-us/windows/uwp/publish/manage-account-users).

* Skapa ditt Microsoft-konto med hjälp av en e-postadress som hör till företagets domän, men inte till enskilda användare, till exempel windowsapps@fabrikam.com.
* Begränsa åtkomsten till den här Microsoft-konto av minsta möjliga antal utvecklare.
* Ställ in en distributionslista för företagets e-post som innehåller alla som behöver åtkomst till kontot för utvecklare och lägga till e-postadressen till din säkerhetsinformation. Detta gör att alla anställda på listan med att ta emot säkerhetskoder vid behov och hantera säkerhetsinformation för ditt Microsoft-konto. Om hur du konfigurerar en distributionslista inte är möjligt behöver ägaren av enskilda e-postkontot ska kunna komma åt och dela säkerhetskod när du uppmanas (t.ex när nya säkerhetsinformation har lagts till i kontot eller när den måste kunna nås från en ny enhet).
* Lägga till ett telefonnummer för företag som inte kräver ett tillägg och går att komma åt viktiga gruppmedlemmar.
* I allmänhet har utvecklare använda betrodda enheter för att logga in på ditt företags utvecklarkonto. Alla nycklar teammedlemmar ska ha åtkomst till dessa betrodda enheter. Detta minskar behovet av säkerhetskoder som ska skickas vid åtkomst till kontot.
* Om du vill tillåta åtkomst till kontot från en icke betrodd dator begränsa att åtkomsten till högst fem utvecklare. Dessa utvecklare bör helst komma åt kontot från datorer som delar samma geografiska och nätverksplats.
* Granska ofta företagets säkerhetsinformation på [ https://account.live.com/proofs/Manage ](https://account.live.com/proofs/Manage) att kontrollera att den är alla aktuella.

Ditt utvecklarkonto ska användas i första hand från betrodda datorer. Detta är viktigt eftersom det finns en gräns för hur många koder som genererats per konto per vecka. Det gör också smidigast inloggning.

Mer information om ytterligare developer konto riktlinjer och säkerhet, klickar du på [här](https://msdn.microsoft.com/en-us/windows/uwp/publish/opening-a-developer-account#additional-guidelines-for-company-accounts).

### <a name="instructions"></a>Instruktioner
1. Öppna en ny Chrome Incognito eller Internet Explorer InPrivate-surfning session för att säkerställa att du inte är inloggad till ett befintligt konto.
2. Registrera e-postmeddelandet (per ovanstående riktlinjer t.ex. windowsapp@fabrikam.com) som ett Microsoft-konto med hjälp av länken [ https://signup.live.com/signup.aspx ](https://signup.live.com/signup.aspx). Följ anvisningarna nedan.

   1. Du måste ange ett giltigt telefonnummer för system om du vill skicka en Verifieringskod för kontot som ett SMS eller ett automatsamtal under registrera ditt konto som ett Microsoft-konto.
   2. Du måste ange en giltig e-id för att ta emot en automatisk e-post för verifiering för kontot under registrera ditt konto som ett Microsoft-konto.
3. Kontrollera den e-postadress som skickas till DL.
4. Du är nu redo att använda det nya Microsoft-kontot i Microsoft Developer Center.

## <a name="2-register-your-account-in-microsoft-developer-center"></a>2. Registrera ditt konto i Microsoft Developer Center
Microsoft Developer Center används för att registrera företagsinformation en gång. Registrant måste vara en giltig representativ för företaget och måste ange deras egen personliga information som ett sätt att verifiera sin identitet. Den person som registrerar måste använda ett Microsoft-konto som är gemensam för företaget, **och samma konto måste användas i Azure Publishing Portal.** Du bör kontrollera att ditt företag inte redan har ett Microsoft Developer Center-konto innan du försöker skapa en. Under processen kommer samla in adressen företagsinformation, bank kontoinformation och skatt information. Denna information kan du vanligtvis få från finans- eller affärsavdelningen.

> [!IMPORTANT]
> Du måste slutföra följande Developer profil komponenter för att kunna gå vidare genom de olika faserna av erbjudandet skapande och distribution.
>
>

| Profil för utvecklare | Starta utkast | Mellanlagring | Publicera kostnadsfritt och lösningsmall | Publicera kommersiella |
| --- | --- | --- | --- | --- |
| Registrering av företagets |Måste ha |Måste ha |Måste ha |Måste ha |
| Skatt profil-ID |Valfri |Valfri |Valfri |Måste ha |
| Konto |Valfri |Valfri |Valfri |Måste ha |

> [!NOTE]
> Ta med din egen licens (BYOL) stöds bara för virtuella datorer och anses vara en **ledigt** erbjudande.
>
>

### <a name="register-your-company-account"></a>Registrera ditt företagskonto
1. Öppna en ny Internet Explorer InPrivate- eller Chrome Incognito-webbläsarsession för att säkerställa att du inte är inloggad till ett personligt konto.
2. Gå till [ http://dev.windows.com/registration?accountprogram=azure ](http://dev.windows.com/registration?accountprogram=azure) att registrera dig som en säljare i Dev Center. Läs följande viktig information innan du fortsätter.

   > [!IMPORTANT]
   > Se till att e-post-id eller en distributionsplats listan (en distributionslista rekommenderas att ta bort beroendet från personer) som du ska använda för att registrera i Dev Center i först registrerad som ett Microsoft-konto. Om det inte finns sedan registrera med den här [länk](https://signup.live.com/signup?uaid=e479342fe2824efeb0c3d92c8f961fd3&lic=1). Dessutom **någon e-post-id under Microsoft företagets domän d.v.s. @microsoft.com går inte att använda** för registrering av Dev Center.
   >
   >

    ![Rita][img-signin]
3. Slutför guiden ”hjälp oss att skydda ditt konto”, som verifierar din identitet via telefonnummer eller e-postadress.

    ![Rita][img-verify]
4. I avsnittet ”registrering kontoinformation” Välj din **konto land/region** från listrutan.

    ![Rita](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_04.png)

   > [!WARNING]
   > **”Säljer från” länder:** för att sälja dina tjänster på Azure Marketplace registrerade entiteten måste komma från en av de godkända ”säljer-från-länderna ovan. Den här begränsningen är beloppet och skatt skäl. Aktivt ser vi att expandera den här listan över länder i den nära framtiden, så Håll ögonen öppna. Mer information finns i [Marketplace deltagande principer](http://go.microsoft.com/fwlink/?LinkID=526833).
   >
   >
5. Välj ”typ av konto” som **företagets** och klicka sedan på den **nästa** knappen.

   > [!IMPORTANT]
   > För att bättre förstå kontotyper och som är bäst att välja, visa sidan [konto typer, platser och avgifter](https://msdn.microsoft.com/library/windows/apps/jj863494.aspx)
   >
   >

    ![Rita](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_05.png)
6. Ange den **Publisher visningsnamn**, vanligtvis namnet på ditt företag.

   > [!TIP]
   > Publisher visningsnamn som angetts i Dev Center visas inte i Azure Marketplace när erbjudandet går listan. Men du måste du fylla det för att slutföra registreringen.
   >
   >
7. Ange den **kontaktuppgifter** för verifiering för kontot.

   > [!IMPORTANT]
   > Du måste ange korrekt kontaktinformation eftersom den används i våra verifieringsprocessen för ditt företag behöver godkännas i Developer Center.
   >
   >
8. Ange kontaktinformation för den **företagets godkännare**. Företagets godkännare är den person som kan verifiera att du har behörighet att skapa ett konto i Dev Center uppdrag av din organisation. Klicka på **nästa** att flytta till den **”betalning avsnittet”** när du är klar.

    ![Rita](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_08.png)
9. Ange din betalningsinformation betala för ditt konto. Om du har en kampanjkod som täcker kostnaden för registrering av anger du som här. Ange annars kreditkort info (eller PayPal stöds marknader). När du är klar klickar du på **nästa** att gå vidare till den **”granska skärmen”**.

    ![Rita](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_09.png)
10. Granska din kontoinformation och bekräfta att allt är korrekt. Läs och acceptera villkoren i den [avtalet för Microsoft Azure Marketplace Publisher](http://go.microsoft.com/fwlink/?LinkID=699560). Markera kryssrutan för att ange att du har läst och accepterat villkoren.
11. Klicka på **Slutför** bekräfta registreringen. Vi skickar ett meddelande till din e-postadress.
12. Om du planerar att publicera endast kostnadsfria erbjudanden, klickar du på **gå till Azure Marketplace Publishing Portal** och du kan hoppa till avsnitt 3 i det här dokumentet [registrera ditt konto i publishing portal](#3-register-your-account-in-the-publishing-portal).

Om du planerar att publicera kommersiell erbjuder (t.ex. virtuella erbjuder med timvis faktureringsmodell som tillämpas), klickar du på **uppdatera din kontoinformation** där du måste fylla i skatt och bankinformation i Developer Center-konto.

Om du vill uppdatera skatt och bank informationen senare, så du kan flytta till nästa avsnitt d.v.s. avsnitt 3 i det här dokumentet [registrera ditt konto i publishing portal](#3-register-your-account-in-the-publishing-portal), och kom tillbaka senare genom att använda länkar i Azure-publicering Portalen.

> [!IMPORTANT]
> Vid kommersiella erbjudanden kommer du inte att skicka dina erbjudanden till produktion utan att slutföra skatt och konto-information.
>
>

Om du vill uppdatera skatt och bank informationen senare går du till avsnittet 3, [registrera ditt konto i publishing portal](#3-register-your-account-in-the-publishing-portal), och kom tillbaka senare genom att använda länkar i Azure Publishing Portal.

### <a name="add-tax-and-banking-information"></a>Lägg till skatt och bankinformation
 Om du vill publicera kommersiella erbjudanden för inköp måste du också att lägga till beloppet skatt information och skicka det för verifiering i Developer Center. Om du vill publicera endast kostnadsfria erbjudanden (eller BYOL erbjuder) behöver du inte lägga till den här informationen. Du kan lägga till den senare, men det tar tid att verifiera skatteinformationen i. Om du vet att du kommer att erbjuda kommersiella erbjudanden för inköp, rekommenderar vi att du lägger till den så snart som möjligt.

**Bankinformation**

1. Logga in på den [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) med ditt Microsoft-konto.
2. Klicka på **beloppet konto** i den vänstra menyn under **Välj betalningsmetod** klickar du på **konto** eller **PayPal**.

   > [!IMPORTANT]
   > Om du har kommersiella erbjudanden som kunder inköp på Marketplace är kontot där du får beloppet för dessa inköp.
   >
   >
3. Ange betalningsinformation och klicka på **spara** när du är nöjd.

   > [!IMPORTANT]
   > Om du behöver uppdatera eller ändra kontot beloppet följer du samma stegen ovan ersätter den aktuella informationen med den nya informationen. Om du ändrar kontot beloppet kan fördröja betalningar av upp till en betalning cykel. Den här fördröjningen beror på att vi behöver verifiera kontot ändras, precis som vi gjorde när du först ställa in beloppet-konto. Du måste fortfarande få betalt hela när ditt konto har verifierats; betalningar på grund av för den aktuella betalningen cykel läggs till nästa.
   >
   >
4. Klicka på **Nästa**.

**Information om skatt**

1. Logga in på den [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) med ditt Microsoft-konto (vid behov).
2. Klicka på **skatt profil** i den vänstra menyn.
3. På den **konfigurera skatt formuläret** sidan, Välj land eller region där du har permanenta land och välj sedan det land eller region där du håller primära medborgarskap. Klicka på **Nästa**.
4. Ange information om skatt och klicka sedan på **nästa**.

> [!WARNING]
> Du kommer inte att skicka till produktion din kommersiell erbjuder utan att slutföra skatt och konto informationen i Microsoft Developer Center-konto.
>
>

Om du har problem med registreringen Developer Center logga ett supportärende som nedan

1. Gå till supportlänk [https://developer.microsoft.com/windows/support](https://developer.microsoft.com/windows/support)
2. Under **Kontakta oss** avsnittet, klicka på knappen **skicka en incident** (som visas i skärmbilden nedan)

    ![Rita](media/marketplace-publishing-accounts-creation-registration/imgAddTax_02.png)
3. Välj ”hjälp med Dev Center” som **problemtyp** och ”publicera och hantera appar” som **kategori**. Klicka på knappen ”Starta e-post” efter.

    ![Rita](media/marketplace-publishing-accounts-creation-registration/imgAddTax_03.png)
4. Du följer med en inloggningssida. Använd alla Microsoft-konto logga in. Om du inte har ett Microsoft-konto skapar en med hjälp av det här [länk](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
5. Fyll i information om problemet och subit biljetten genom att klicka på den **skicka** knappen.

    ![Rita](media/marketplace-publishing-accounts-creation-registration/imgAddTax_05.png)

## <a name="3-register-your-account-in-the-publishing-portal"></a>3. Registrera ditt konto i publishing portal
Den [publicering portal](http://publish.windowsazure.com) används för att publicera och hantera dina erbjudanden.

1. Öppna en ny Chrome Incognito eller Internet Explorer InPrivate-surfning session för att säkerställa att du inte är inloggad till ett personligt konto.
2. Gå till [http://publish.windowsazure.com](http://publish.windowsazure.com).
3. Om du är en ny användare och logga in på publicering Företagsportalen för första gången kan sedan du måste logga in med samma e-post-id för vilken Dev Center-konto har registrerats. På så sätt länkas Dev Center-konto och publicera portalkonto med varandra. Du kan senare lägga till andra medlemmar i företaget, som arbetar på programmet som en medadministratör i publicering portal genom att följa stegen nedan.

Om du har lagts till som medadministratör i publicering portalen sedan du kan logga in med ditt medadministratör.

> [!TIP]
> Principer för deltagande beskrivs på den [Azure-webbplatsen](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
>
>

## <a name="4-steps-to-add-a-co-admin-in-the-publishing-portal"></a>4. Steg för att lägga till en medadministratör i publicering portal
**Förutsatt att du är administratör,** nedan följer stegen för att lägga till en ko-administratör.

> [!NOTE]
> **För de nya användarna** innan du lägger till en medadministratör i publicering portal, se till att du har skapat minst ett program i publicering portal. Detta är den **UTGIVARE** fliken visas endast när du har skapat minst ett program i publicering portal.
>
>

1. Se till att medadministratör e-post-id är ett Microsoft-account(MSA). Om inte, registrera den som en MSA med detta [länk](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
2. Kontrollera att det finns minst ett program under administratörskontot innan du försöker lägga till en ko-administratör.
3. När stegen ovan är klar, logga in på publicering Företagsportalen med medadministratör e-post-id och sedan loggar ut.
4. Nu logga in på publicering Företagsportalen med e-post-ID: t för administratören.
5. Navigera till utgivare -> ditt konto -> Välj administratörer -> Lägg till medadministratör (skärmbilden nedan)

   ![Rita](media/marketplace-publishing-accounts-creation-registration/imgAddAdmin_05.png)

## <a name="5-steps-to-delete-a-co-admin-in-the-publishing-portal"></a>5. Steg för att ta bort en medadministratör i publicering portal
**Förutsatt att du är administratör,** nedan följer stegen för att ta bort en ko-administratör.

1. Logga in på publicering Företagsportalen med e-post-ID: t för administratören.
2. Gå till **utgivare** -> Välj ditt konto -> **administratörer** -> **Medadministratörer**.
3. Klicka på den **X** knappen bredvid medadministratör du vill ta bort totalvärde (skärmbilden nedan).

    ![Rita](media/marketplace-publishing-accounts-creation-registration/imgDeleteAdmin_03.png)

## <a name="next-steps"></a>Nästa steg
Nu när ditt konto skapas och registreras, se till att du uppfyller eller uppfyller alla icke-tekniska krav för att publicera erbjudandet genom att granska [icke-tekniska krav](marketplace-publishing-pre-requisites.md).

## <a name="see-also"></a>Se också
* [Komma igång: hur du publicerar ett erbjudande på Azure Marketplace](marketplace-publishing-getting-started.md)

[img-msalive]:media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-live.jpg
[img-email]:media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-verifyemail.jpg
[img-sd-url]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-incognito.jpg
[img-signin]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-login.jpg
[img-verify]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-verify.jpg
[img-sd-top]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-details.jpg
[img-sd-info]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal.jpg
[img-sd-type]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-type.jpg
[img-sd-mktg1]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det1.jpg
[img-sd-mktg2]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det2.jpg
[img-sd-addr]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-add.jpg
[img-sd-legal]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-cmp.jpg
[img-sd-submit]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-approval.jpg

[link-msdndoc]: https://msdn.microsoft.com/library/jj552460.aspx
[link-sellerdashboard]: http://sellerdashboard.microsoft.com/
[link-pubportal]: https://publish.windowsazure.com
[link-single-vm]:marketplace-publishing-vm-image-creation.md
[link-single-vm-prereq]:marketplace-publishing-vm-image-creation-prerequisites.md
[link-multi-vm]:marketplace-publishing-solution-template-creation.md
[link-multi-vm-prereq]:marketplace-publishing-solution-template-creation-prerequisites.md
[link-datasvc]:marketplace-publishing-data-service-creation.md
[link-datasvc-prereq]:marketplace-publishing-data-service-creation-prerequisites.md
[link-devsvc]:marketplace-publishing-dev-service-creation.md
[link-devsvc-prereq]:marketplace-publishing-dev-service-creation-prerequisites.md
[link-pushstaging]:marketplace-publishing-push-to-staging.md
