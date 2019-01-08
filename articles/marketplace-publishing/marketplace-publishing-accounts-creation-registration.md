---
title: Skapa och registrera utgivarkonto | Microsoft Docs
description: Anvisningar för att skapa ett Microsoft Developer-konto så vid godkännande, kan du sälja olika erbjuder typer på Azure Marketplace.
services: Azure Marketplace
documentationcenter: ''
author: v-miclar
manager: hascipio
editor: ''
ms.assetid: 5a2fe68d-2967-463f-8af6-42bed07e3eaa
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: hascipio
ROBOTS: NOINDEX
ms.openlocfilehash: f32a3a992445bc3aeccc035e54653555bfd64d6c
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54078430"
---
# <a name="create-a-microsoft-developer-account"></a>Skapa ett Microsoft Developer-konto
Den här artikeln vägleder dig genom de nödvändiga kontoskapandet och registreringen ska bli en godkänd Microsoft Developer för Azure Marketplace.

## <a name="1-create-a-microsoft-account"></a>1. Skapa ett Microsoft-konto
Om du vill starta publiceringsprocessen behöver du skapa ett Microsoft-konto. Det här kontot används för att registrera i både den **Microsoft Developer Center** och **Azure-Publiceringsportalen**. Du bör ha endast en Microsoft-konto för alla dina Azure Marketplace-erbjudanden. Det får inte vara specifika för tjänster eller erbjudanden.

Adressen som utgör användarnamnet bör finnas i din domän och skötas av ditt IT-team. Alla publicering-relaterade aktiviteter bör göras via det här kontot.

> [!WARNING]
> Ord som **”Azure”** och **”Microsoft”** stöds inte för registrering av Microsoft. Undvik att använda dessa ord för att slutföra kontoskapandet och registreringsprocessen.
>
>

### <a name="guidelines-for-company-accounts"></a>Riktlinjer för företag
När du skapar ett företag kan du följa dessa riktlinjer om mer än en person ska kunna komma åt kontot genom att logga in med det Microsoft-konto som öppnats kontot.

> [!Important]
> Viktigt att du tillåter flera användare åtkomst till ditt Dev Center-konto, vi rekommenderar att du använder Azure Active Directory för att tilldela roller till enskilda användare som kan komma åt kontot genom att logga in med sina person autentiseringsuppgifter för Azure AD. Mer information finns i [hantera kontoanvändare](https://msdn.microsoft.com/windows/uwp/publish/manage-account-users).

* Skapa ditt Microsoft-konto med hjälp av en e-postadress som hör till ditt företags domän, men inte till enskilda användare – till exempel windowsapps@fabrikam.com.
* Begränsa åtkomsten till Microsoft-konto till det minsta möjliga antalet utvecklare.
* Ställ in en distributionslista för företagets e-post som innehåller alla som behöver åtkomst till developer-konto och Lägg till e-postadressen till din säkerhetsinformation. Den här konfigurationen tillåter alla anställda på listan för att ta emot säkerhetskoder vid behov och hantera säkerhetsinformation för ditt Microsoft-konto. Om hur du konfigurerar en distributionslista inte är möjligt, behöver ägaren av enskilda e-postkontot ska kunna komma åt och dela säkerhetskod när du uppmanas (till exempel när ny säkerhetsinformation har lagts till i kontot, eller när den måste kunna nås från en ny enhet).
* Lägg till ett telefonnummer för företag som kräver inte ett tillägg och är tillgänglig för viktiga gruppmedlemmar.
* Generellt sett ha utvecklare använda betrodda enheter för att logga in på ditt företags developer-konto. Alla viktiga teammedlemmar ska ha åtkomst till dessa betrodda enheter. Den här metoden minskar behovet av säkerhetskoder som ska skickas vid åtkomst till kontot.
* Om du vill tillåta åtkomst till kontot från en icke betrodd dator kan du begränsa att åtkomsten till högst fem utvecklare. Utvecklarna bör helst åtkomst till kontot från datorer som delar samma geografiska och nätverksplats.
* Granska ditt företags säkerhetsinformation på ofta [ https://account.live.com/proofs/Manage ](https://account.live.com/proofs/Manage) att kontrollera att det är alla aktuella.

Developer-konto ska nås främst från betrodda datorer. Den här begränsningen är viktigt eftersom det finns en gräns för hur många koder som genererats per konto per vecka. Dessutom kan de flesta sömlös inloggning.

Mer information om ytterligare developer-konto riktlinjer och säkerhet finns i [här](https://msdn.microsoft.com/windows/uwp/publish/opening-a-developer-account#additional-guidelines-for-company-accounts).

### <a name="instructions"></a>Instruktioner
1. Öppna en ny Chrome Incognito eller Internet Explorer InPrivate-webbläsarsession för att säkerställa att du inte är inloggad till ett befintligt konto.
2. Registrera e-postmeddelandet (per riktlinjer ovan, till exempel windowsapp@fabrikam.com) som ett Microsoft-konto med hjälp av länken [ https://signup.live.com/signup.aspx ](https://signup.live.com/signup.aspx). Följ anvisningarna nedan.

   1. Under registrera ditt konto som ett Microsoft-konto, måste du ange ett giltigt telefonnummer för systemet för att skicka en Verifieringskod-konto som ett SMS eller ett automatiskt samtal.
   2. Under registrera ditt konto som ett Microsoft-konto, måste du ange en giltig e-post-ID för automatiserade e-post för Kontoverifiering.
3. Kontrollera den e-postadress som skickas till i Distributionslistan.
4. Du är nu redo att använda det nya Microsoft-kontot i Microsoft Developer Center.

## <a name="2-register-your-account-in-microsoft-developer-center"></a>2. Registrera ditt konto i Microsoft Developer Center
Microsoft Developer Center för att registrera företagsinformation en gång. Av måste vara en giltig representant från företaget och måste ange sin personliga information som ett sätt att verifiera sin identitet. Den person som registrerar måste använda ett Microsoft-konto som delas för företaget, *och samma konto måste användas i Azure-Publiceringsportalen.* Kontrollera att ditt företag redan har ett Microsoft Developer Center-konto innan du försöker skapa en. Under processen kan vi samla in företagets adressuppgifter, Bankkontouppgifter, och skatteinformation. Dessa data är vanligtvis få från finans- eller affärsavdelningen.

> [!IMPORTANT]
> Du måste slutföra följande komponenter i Developer-profil för att utföra olika faser i erbjudandet skapande och distribution.
>
>

| Utvecklarprofil | Starta utkast | Mellanlagring | Publicera utan kostnad och lösningsmallen | Publicera kommersiella |
| --- | --- | --- | --- | --- |
| Registrering av företagets |Måste ha |Måste ha |Måste ha |Måste ha |
| Skatt profil-ID |Valfri |Valfri |Valfri |Måste ha |
| Konto |Valfri |Valfri |Valfri |Måste ha |

> [!NOTE]
> Bring Your Own License (BYOL) stöds bara för virtuella datorer och betraktas som en **kostnadsfria** erbjuder.
>
>

### <a name="register-your-company-account"></a>Registrera ditt företagskonto
1. Öppna en ny Internet Explorer InPrivate- eller Chrome Incognito-webbläsarsession för att säkerställa att du inte är inloggad på ett personligt konto.
2. Gå till [ http://dev.windows.com/registration?accountprogram=azure ](http://dev.windows.com/registration?accountprogram=azure) registrera dig själv som en försäljning i Dev Center. Observera följande:

   > [!IMPORTANT]
   > Se till att e-post-id eller en distributionsplats listan (en distributionslista rekommenderas att ta bort beroendet från personer) som du kommer att använda för att registrera i Dev Center i först registreras som ett Microsoft-konto. Om inte, sedan registrera använder det här [länk](https://signup.live.com/signup?uaid=e479342fe2824efeb0c3d92c8f961fd3&lic=1). Dessutom *någon e-ID under Microsoft företagets domän, till exempel @microsoft.com, kan inte användas* för registrering av Dev Center.
   >

    ![Rita][img-signin]
3. Slutför guiden ”Help us protect your account”, som verifierar din identitet via telefonnummer eller e-postadress.

    ![Rita][img-verify]
4. I avsnittet ”registrering-kontoinformation” Välj din **konto land/region** från den nedrullningsbara menyn.

    ![Rita](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_04.png)

   > [!WARNING]
   > **”” Försäljningsländer:** Registrerade entiteten måste vara från en av de godkända ”” försäljningsländer ovan för att kunna sälja dina tjänster på Azure Marketplace. Den här begränsningen är payout och skatt skäl. Vi letar aktivt så Håll ögonen öppna om du vill expandera den här listan över länder inom en snar framtid. Mer information finns i den [deltagandepolicyer för Marketplace](https://go.microsoft.com/fwlink/?LinkID=526833).
   >
   >
5. Välj din ”kontotyp” som **företagets** och klicka sedan på den **nästa** knappen.

   > [!IMPORTANT]
   > För att bättre förstå typer av konton och som är bäst för dig att välja, visa sidan [konto typer, platser och avgifter](https://msdn.microsoft.com/library/windows/apps/jj863494.aspx)
   >
   >

    ![Rita](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_05.png)
6. Ange den **Publisher visningsnamn**, vanligtvis namnet på ditt företag.

   > [!TIP]
   > Angivna i Dev Center publisher visningsnamnet visas inte i Azure Marketplace när ditt erbjudande blir angivna. Men det måste anges för att slutföra registreringen.
   >
   >
7. Ange den **kontaktinformation** för verifiering för kontot.

   > [!IMPORTANT]
   > Du måste ange kontaktinformation, eftersom det kommer att användas i vår verifieringsprocessen för ditt företag behöver godkännas i Developer Center.
   >
   >
8. Ange kontaktinformation för den **företagets godkännare**. Företagets godkännare är den person som kan verifiera att du har behörighet att skapa ett konto i Dev Center uppdrag av din organisation. Klicka på **nästa** att flytta till den **”betalning avsnitts”** när du är klar.

    ![Rita](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_08.png)
9. Ange din betalningsinformation för att betala för ditt konto. Om du har en kampanjkod som täcker kostnaden för registrering kan ange du som här. I annat fall ger ditt kreditkort info (eller PayPal på marknader som stöds). När du är klar klickar du på **nästa** att gå vidare till den **”granska skärmen”**.

    ![Rita](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_09.png)
10. Granska din kontoinformation och bekräfta att allt är korrekt. Sedan läser och godkänner villkoren i den [Publiceringsavtalet för Microsoft Azure Marketplace](https://go.microsoft.com/fwlink/?LinkID=699560). Markera rutan som indikerar att du har läst och godkänt villkoren.
11. Klicka på **Slutför** att bekräfta din registrering. Vi skickar ett bekräftelsemeddelande till din e-postadress.
12. Om du planerar att publicera endast kostnadsfria erbjudanden, klickar du på **går du till Azure Marketplace Publisher-portalen** och du kan gå vidare till avsnitt 3 i det här dokumentet [registrera ditt konto i publiceringsportalen](#3-register-your-account-in-the-publishing-portal).

Om du planerar att publicera kommersiell erbjuder (till exempel VM-erbjudanden med timvis faktureringsmodellen), klickar du på **uppdatera din kontoinformation** där du måste fylla i skatte- och bankinformation i Developer Center konto.

Om du vill uppdatera skatte- och bank informationen senare så kan du flytta till nästa avsnitt (avsnitt 3) i det här dokumentet [registrera ditt konto i publiceringsportalen](#3-register-your-account-in-the-publishing-portal), och kom tillbaka senare genom att använda länkar i Azure-publicering Portalen.

> [!IMPORTANT]
> Vid betalning, kommersiella, kommer du inte att kunna skicka dina erbjudanden till produktion utan att slutföra skatte- och konto-information.
>
>

Om du vill uppdatera skatte- och bank informationen senare går du till avsnitt 3, [registrera ditt konto i publiceringsportalen](#3-register-your-account-in-the-publishing-portal), och kom tillbaka senare genom att använda länkar i Azure-Publiceringsportalen.

### <a name="add-tax-and-banking-information"></a>Lägg till skatte- och bankuppgifter
 Om du vill publicera kommersiella erbjudanden för inköp, måste du även att lägga till betalnings och skatteinformation och skicka in för verifiering i Developer Center. Om du vill publicera endast kostnadsfria erbjudanden (eller BYOL-erbjudanden), behöver du inte lägga till den här informationen. Du kan lägga till den senare, men det tar lite tid att verifiera skatteinformationen. Om du vet att du kommer att erbjuda kommersiella erbjudanden för inköp, rekommenderar vi att du lägger till det så snart som möjligt.

**Bankinformation**

1. Logga in på den [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) med ditt Microsoft-konto.
2. Klicka på **Payout konto** på den vänstra menyn under **Välj betalningssätt** klickar du på **konto** eller **PayPal**.

   > [!IMPORTANT]
   > Om du har kommersiella erbjudanden som kunderna köper i Marketplace, är konton där du får payout för dessa inköp.
   >
   >
3. Ange betalningsinformation och klicka på **spara** när du är nöjd.

   > [!IMPORTANT]
   > Om du vill uppdatera eller ändra ditt betalnings-konto, följer du samma stegen ovan, byta ut den aktuella säkerhetsinformation med den nya informationen. Ändra ditt betalnings-konto kan fördröja betalningarna av upp till en betalningsmetod cykel. Den här fördröjningen beror på att vi behöver verifiera ändringen av tjänstkontot, precis som vi gjorde när du först ställa in betalnings-konto. Du kommer fortfarande få betalt för för hela beloppet när ditt konto har verifierats; alla betalningar på grund av för den aktuella betalningen cykel kommer att läggas till nästa.
   >
   >
4. Klicka på **Nästa**.

**Skatteinformation**

1. Logga in på den [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) med ditt Microsoft-konto (vid behov).
2. Klicka på **skatt profil** på den vänstra menyn.
3. På den **ställer in formulärets skatt** sidan, väljer du land eller region där du har permanent dataplacering och sedan väljer du land eller region där du håller primära medborgarskap. Klicka på **Nästa**.
4. Ange information om din skatt och klicka sedan på **nästa**.

> [!WARNING]
> Du kommer inte att kunna skicka till produktion ditt kommersiella erbjuder utan att slutföra skatte- och konto informationen i Microsoft Developer Center-konto.
>
>

Om du har problem med registrering av Developer Center, logga ett supportärende enligt nedan

1. Gå till supportlänken [https://developer.microsoft.com/windows/support](https://developer.microsoft.com/windows/support)
2. Under **Kontakta oss** klickar du på knappen **ett ärende** (som visas på skärmbilden nedan)

    ![Rita](media/marketplace-publishing-accounts-creation-registration/imgAddTax_02.png)
3. Välj ”Help med Dev Center” som **problemtyp** och ”publicera och hantera appar” som **kategori**. Efter det klickar du på knappen ”Start e-post”.

    ![Rita](media/marketplace-publishing-accounts-creation-registration/imgAddTax_03.png)
4. Du kommer att få med en inloggningssida. Använda alla Microsoft-konto logga in. Om du inte har ett Microsoft-konto, skapar du en med hjälp av detta [länk](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
5. Fyll i information om problemet och subit biljetten genom att klicka på den **skicka** knappen.

    ![Rita](media/marketplace-publishing-accounts-creation-registration/imgAddTax_05.png)

## <a name="3-register-your-account-in-the-publishing-portal"></a>3. Registrera ditt konto i publiceringsportalen
Den [Publiceringsportal](http://publish.windowsazure.com) används för att publicera och hantera dina erbjudanden.

1. Öppna en ny Chrome Incognito eller Internet Explorer InPrivate-webbläsarsession för att säkerställa att du inte är inloggad på ett personligt konto.
2. Gå till [http://publish.windowsazure.com](http://publish.windowsazure.com).
3. Om du är en ny användare och logga in på publiceringen portal för första gången, sedan måste du logga in med samma e-post-ID som din Dev Center-kontot är registrerat. I det här sättet, Dev Center-konto och publicering länkas portal-konto med varandra. Du kan senare lägga till andra medlemmar i företaget som arbetar på programmet som en medadministratör i publiceringen portalen genom att följa stegen nedan.

Om du har lagts till som medadministratör i publiceringen portal, sedan du kan logga in med ditt medadministratör.

> [!TIP]
> Beskrivs i deltagandepolicyer för på den [Azure-webbplatsen](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
>
>

## <a name="4-steps-to-add-a-co-admin-in-the-publishing-portal"></a>4. Hur du lägger till en medadministratör i publiceringen portal
**Om vi antar att du är administratör,** nedan följer stegen för att lägga till en medadministratör.

> [!NOTE]
> **För de nya användarna** innan du lägger till en medadministratör i publiceringen portal, se till att du har skapat minst ett program i publiceringen portal. Detta är obligatoriskt som den **UTGIVARE** fliken visas bara när du har skapat minst ett program i publiceringen portal.
>
>

1. Kontrollera att medadministratör e-post-ID: T är en Microsoft-account(MSA). Om den inte registrera den som en MSA som använder det här [länk](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
2. Kontrollera att det finns minst ett program under administratörskontot innan du försöker lägga till en medadministratör.
3. När stegen ovan är klar, logga in på publiceringen portal med medadministratör e-ID och sedan logga ut.
4. Logga nu in på publiceringen med admin e-post-ID.
5. Gå till utgivare -> Välj ditt konto -> administratörer -> Lägg till medadministratör (skärmbilden nedan)

   ![Rita](media/marketplace-publishing-accounts-creation-registration/imgAddAdmin_05.png)

## <a name="5-steps-to-delete-a-co-admin-in-the-publishing-portal"></a>5. Steg för att ta bort en medadministratör i publiceringen portal
**Om vi antar att du är administratör,** nedan följer stegen för att ta bort medadministratör.

1. Logga in på publiceringen med admin e-post-ID.
2. Gå till **utgivare** -> Välj ditt konto -> **administratörer** -> **Medadministratörer**.
3. Klicka på den **X** knappen bredvid medadministratör du vill ta bort summa (skärmbilden nedan).

    ![Rita](media/marketplace-publishing-accounts-creation-registration/imgDeleteAdmin_03.png)

## <a name="next-steps"></a>Nästa steg
Nu när ditt konto skapas och registrerats, se till att du uppfyller eller uppfyller alla icke-tekniska krav för att publicera ditt erbjudande genom att granska [icke-tekniska krav](marketplace-publishing-pre-requisites.md).

## <a name="see-also"></a>Se också
* [Komma igång: Hur du publicerar ett erbjudande på Azure Marketplace](marketplace-publishing-getting-started.md)

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
