---
title: Lead management för Dynamics 365 för Customer Engagement | Azure Marketplace
description: Konfigurera leadhantering för Dynamics 365 för Kundengagemang.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 5b3e35b6d19905e3c5262dfea3e52511510c9ffe
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81252970"
---
# <a name="configure-lead-management-for-dynamics-365-for-customer-engagement"></a>Konfigurera leadhantering för Dynamics 365 för kundengagemang

I den här artikeln beskrivs hur du konfigurerar Dynamics 365 for Customer Engagement (tidigare Dynamics CRM Online), läs mer om ändringen [här](https://docs.microsoft.com/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online) för att bearbeta försäljningsleads från ditt marketplace-erbjudande. 

>[!Note]
>Dessa instruktioner är specifika för Microsofts värdmoln Dynamics 365 for Customer Engagement-miljö. Det finns för närvarande inte något annat alternativ för att ta emot leads som att konfigurera en [https-slutpunkt](./commercial-marketplace-lead-management-instructions-https.md) eller en [Azure-tabell](./commercial-marketplace-lead-management-instructions-azure-table.md) för att ta emot leads.

## <a name="prerequisites"></a>Krav

Följande användarbehörigheter behövs för att slutföra stegen i den här artikeln:

* Du måste vara administratör i din Dynamics 365 för Customer Engagement-instans för att kunna installera en lösning och följa dessa instruktioner.
* Du måste vara klientadministratör för att skapa ett nytt tjänstkonto för leadtjänsten som används för att skicka leads från marketplace-erbjudanden.
* Du måste ha åtkomst till administrationsportalen för Office 365.
* Du måste ha åtkomst till Azure-portalen.

## <a name="install-the-solution"></a>Installera lösningen

1.  Ladda ned [Microsoft Marketplace Lead Writer-lösningen](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) och spara den lokalt på datorn.

2.  Öppna Dynamics 365 för Kundengagemang genom att navigera `https://tenant.crm.dynamics.com`till URL:en för din Dynamics-förekomst (till exempel ).

3.  Åtkomstinställningar genom att välja kugghjulsikon och **avancerade inställningar** i det övre navigeringsfältet.
 
    ![Dynamics - Avancerade inställningar](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

4.  En gång på sidan Inställningar öppnar du menyn Inställning i det övre navigeringsfältet och väljer **Lösningar**.

    >[!Note]
    >Om du inte ser alternativen i nästa skärminspelning har du inte de behörigheter du behöver för att fortsätta. Kontakta en administratör på din Dynamics 365 för Customer Engagement-instans.

    ![Dynamics 365 - Lösningar](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

5. En gång på sidan Lösningar väljer du **Importera** och navigerar till den plats där du sparade *Microsoft Marketplace Lead Writer-lösningen* som du hämtade i steg 1.

    ![Dynamics 365 för kundengagemang - Import](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

6. Slutför importen av lösningen genom att följa guiden Importera lösning.

## <a name="configure-user-permissions"></a>Konfigurera användarbehörigheter

Om du vill skriva leads till din Dynamics 365 för Customer Engagement-instans måste du dela ett tjänstkonto med oss och konfigurera behörigheter för kontot.

Följ följande steg för att skapa tjänstkontot och tilldela behörigheter. Du kan använda **Azure Active Directory** eller Office **365**.

>[!Note]
>Baserat på det autentiseringsalternativ du väljer kan du gå vidare till motsvarande instruktioner baserat på ditt val. Se [Azure Active Directory](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#azure-active-directory) eller Office [365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#office-365).

### <a name="azure-active-directory"></a>Azure Active Directory

Vi rekommenderar det här alternativet eftersom du får den extra fördelen att aldrig behöva uppdatera ditt användarnamn / lösenord för att fortsätta få leads. Om du vill använda Alternativet Azure Active Directory anger du app-ID,programnyckel och katalog-ID från Active Directory-programmet.

Använd följande steg för att konfigurera Azure Active Directory för Dynamics 365 för Customer Engagement.

1. Logga in på [Azure-portalen](https://portal.azure.com/)och välj sedan Azure Active Directory-tjänsten från den vänstra navigeringen.

2. Välj **Egenskaper** i azure Active Directory vänster navigering och kopiera **värdet för katalog-ID** på den sidan. Spara det här värdet, eftersom det är *katalog-ID-värdet* som du måste ange i publiceringsportalen för att ta emot leads för ditt marketplace-erbjudande.

    ![Azure Active Directory - Egenskaper](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

3. Välj **Appregistreringar** från vänster navigering i Azure Active Directory och välj sedan **Ny registrering** på den sidan.
4. Ange ett namn på programnamnet. Ange ett meningsfullt programnamn.
5. Under Kontotyper som stöds väljer du **Konton i en organisationskatalog**.
6. Under Omdirigera URI väljer du **Webb** och `https://contosoapp1/auth`tillhandahåller en URI (till exempel ). 
7. Välj **Registrera**.

    ![Registrera ett program](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

8. Nu när ditt program är registrerat öppnar du programmets översiktssida och kopierar värdet **program (klient)** på den sidan. Spara det här värdet, eftersom det är *det program-ID-värde som* du måste ange i publiceringsportalen och i Dynamics för att ta emot leads för ditt marketplace-erbjudande.

    ![Program-ID (klient)](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

9. Välj **Certifikat och hemligheter** från appens vänstra navigering och välj Ny **klienthemlighet** på den sidan. Ange en meningsfull beskrivning av klienthemligheten och välj alternativet **Aldrig** under Upphör att gälla. Välj **Lägg till** för att skapa klienthemligheten.

    ![Ansökan - Certifiering och hemligheter](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

10. Så snart klienthemligheten har skapats **kopierar du klientens hemliga värde**. Du kan inte hämta värdet när du har navigerat bort från sidan. Spara det här värdet, eftersom det är det hemliga värdet *för klienten* som du behöver ange i publiceringsportalen för att ta emot leads för ditt marketplace-erbjudande. 
11. Välj **API-behörigheter** från apparnas vänstra navigering och välj sedan **Lägg till en behörighet**.
12. Välj Microsoft API:er och välj sedan **Dynamics CRM** som API.
13. *Under Vilken typ av behörigheter kräver ditt program kontrollerar*du att **delegerade behörigheter** är markerade. Kontrollera behörigheten för **user_impersonation** *Åtkomst till common data service som organisationsanvändare*. Välj **Lägg till behörigheter**.

    ![Lägga till behörigheter](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

14. När du har slutfört steg 1-13 på Azure-portalen navigerar du till dynamics 365 for Customer Engagement-instansen genom att navigera till URL:en (till exempel `https://tenant.crm.dynamics.com`).
15. Åtkomstinställningar genom att välja kugghjulsikonen och **Avancerade inställningar** i det övre navigeringsfältet.
16. En gång på sidan Inställningar öppnar du menyn Inställning i det övre navigeringsfältet och väljer **Säkerhet**.
17. En gång på sidan Säkerhet väljer du **Användare**.  På sidan Användare väljer du listrutan "Aktiverade användare" för att växla över till **Programanvändare**.
18. Välj **Ny** om du vill skapa en ny användare. 

    ![Skapa en ny användare](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

19. I **Ny användare**kontrollerar du att USER: APPLICATION USER är markerat. Ange ett användarnamn, fullständigt namn och e-postadress för den användare som du vill använda med den här anslutningen. Klistra också in **program-ID:et** för appen som du skapade i Azure-portalen från steg 8. Välj **Spara och stäng** om du vill lägga till användaren.

    ![Ny användare](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

20. Gå till "Säkerhetsinställningar" i den här artikeln för att slutföra konfigurationen av anslutningen för den här användaren.

### <a name="office-365"></a>Office 365

Om du inte vill använda Azure Active Directory kan du registrera en ny användare i *Microsoft 365 admincenter*. Du måste uppdatera ditt användarnamn/lösenord var 90:e dag för att fortsätta få leads.

Använd följande steg för att konfigurera Office 365 för Dynamics 365 för Kundengagemang.

1. Logga in på [Administrationscenter för Microsoft 365](https://admin.microsoft.com).

2. Välj **Lägg till en användare**.

    ![Administrationscenter för Microsoft 365 – lägg till en användare](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

4. Skapa en ny användare för lead writer-tjänsten. Konfigurera följande inställningar:

    * Ange ett användarnamn
    * Ange ett lösenord och avmarkera alternativet "Gör den här användaren ändra sitt lösenord när de loggar in för första gången".
    * Välj "Användare (ingen administratörsåtkomst)" som roll för användaren.
    * Välj "Dynamics 365 Customer Engagement-abonnemang" som produktlicens som visas i nästa skärminspelning. Du debiteras för den licens du väljer. 

Spara dessa värden eftersom de är de *värden för användarnamn och lösenord* som du behöver ange i publiceringsportalen för att ta emot leads för ditt marketplace-erbjudande.

![Microsoft 365 admin center - ny användare](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>Säkerhetsinställningar

Det sista steget är att aktivera användaren som du skapade för att skriva leads.

1. Öppna Dynamics 365 för Kundengagemang genom att navigera `https://tenant.crm.dynamics.com`till URL:en för din Dynamics-förekomst (till exempel ).
2. Åtkomstinställningar genom att välja kugghjulsikon och **avancerade inställningar** i det övre navigeringsfältet.
3. En gång på sidan Inställningar öppnar du menyn Inställning i det övre navigeringsfältet och väljer **Säkerhet**.
4. En gång på sidan Säkerhet väljer du **Användare** och väljer den användare som du skapade i avsnittet Konfigurera användarbehörigheter i det här dokumentet och väljer sedan **Hantera roller**. 

    ![Hantera roller](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

5. Sök efter rollnamnet "Microsoft Marketplace Lead Writer" och välj det för att tilldela användaren rollen.

    ![Hantera användarroller](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!Note]
    >Den här rollen skapas av lösningen som du importerade och har bara behörighet att skriva leads och spåra lösningsversionen för att säkerställa kompatibilitet.

6. Navigera tillbaka till sidan Säkerhet och välj **Säkerhetsroller**. Sök efter rollen "Microsoft Marketplace Lead Writer" och välj den.

    ![Säkerhetsroller](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

7. En gång i säkerhetsrollen väljer du fliken **Kärnposter.** Sök efter entiteten Användarentitetsgränssnittsinställningar och aktivera behörigheterna Skapa, Läsa och Skriva till Användare (1/4 gul cirkel) för den entiteten genom att klicka en gång i var och en av motsvarande cirklar.

    ![Microsoft Marketplace Lead Writer - Kärnposter](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

8. Navigera nu till **fliken Anpassning.** Sök tor entiteten "Systemjobb" och aktiverar read,write och appendTo-behörigheterna till Organisation (fast grönt) för den entiteten genom att klicka fyra gånger i var och en av motsvarande cirklar.

    ![Microsoft Marketplace Lead Writer – anpassning](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

9. **Spara och stäng**.

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-for-customer-engagement"></a>Konfigurera erbjudandet för att skicka leads till Dynamics 365 For Customer Engagement

När du är redo att konfigurera leadhanteringsinformationen för ditt erbjudande i publiceringsportalen följer du stegen nedan:

1. Navigera till **inställningssidan** för Erbjudandet för ditt erbjudande.
2. Välj **Anslut** under avsnittet LeadHantering.

    ![Ansluta till leadhantering](./media/commercial-marketplace-lead-management-instructions-dynamics/connect-lead-management.png)

3. I popup-fönstret Anslutningsinformation väljer du **Dynamics 365 för Customer Engagement** för leadmålet

    ![Anslutningsinformation - leadmål](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

4. Ange **URL:en för Dynamics 365-instans,** till exempel `https://contoso.crm4.dynamics.com`.

5. Välj metoden **för autentisering,** Azure Active Directory eller Office 365. 
6. Om du har valt Azure Active Directory anger du `23456052-aaaa-bbbb-8662-1234df56788f` **program-ID:t (klient)-ID** `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`(exempel: ), **katalog-ID** (exempel: `12345678-8af1-4asf-1234-12234d01db47`) och **klienthemlighet** (exempel: ).

    ![Anslutningsinformation - Azure Active Directory](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

7. Om du har valt Office 365 anger `contoso@contoso.onmicrosoft.com`du **användarnamnet** `P@ssw0rd`(exempel: ) och Lösenord (exempel: ).

    ![Anslutningsinformation - Användarnamn](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

8. **Kontakta e-post** - Ge e-post till personer i ditt företag som ska få e-postmeddelanden när ett nytt lead tas emot. Du kan tillhandahålla flera e-postmeddelanden genom att separera dem med semikolon.
9. Välj **OK**.

Om du vill vara säker på att du har anslutit till ett leadmål klickar du på knappen validera. Om det lyckas kommer du att ha en testledning i leaddestinationen.

![Leadhantering - lagringskonto för anslutningsinformation](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-connection-details.png)

>[!Note]
>Du måste slutföra konfigurationen av resten av erbjudandet och publicera det innan du kan ta emot leads för erbjudandet.
