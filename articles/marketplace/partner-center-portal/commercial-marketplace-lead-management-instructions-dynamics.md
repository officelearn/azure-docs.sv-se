---
title: Lead-hantering för Dynamics 365 för kund engagemang | Azure Marketplace
description: Konfigurera ledar hantering för Dynamics 365 för kund engagemang.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: evansma
ms.openlocfilehash: 03d67249ef1d74844a7e9019e90f6331ecfdf0c3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102980"
---
# <a name="configure-lead-management-for-dynamics-365-for-customer-engagement"></a>Konfigurera ledar hantering för Dynamics 365 för kund engagemang

Den här artikeln beskriver hur du konfigurerar Dynamics 365 för kund engagemang (tidigare Dynamics CRM Online). Läs mer om ändringen [här](https://docs.microsoft.com/dynamics365/customer-engagement/admin/switch-dynamics-crm-online-dynamics-365) för att bearbeta försäljnings leads från Marketplace-erbjudandet. 

>[!Note]
>Dessa instruktioner är specifika för Microsoft Hosted Cloud Dynamics 365 för kund engagemang miljö. Det finns för närvarande inte stöd för att ansluta direkt till en Dynamics lokal-miljö, men det finns andra alternativ för att ta emot leads, till exempel konfigurera en [https-slutpunkt](./commercial-marketplace-lead-management-instructions-https.md) eller en [Azure-tabell](./commercial-marketplace-lead-management-instructions-azure-table.md) för att ta emot leads.

## <a name="prerequisites"></a>Förutsättningar

Följande användar behörigheter krävs för att slutföra stegen i den här artikeln:

* Du måste vara administratör för din Dynamics 365 for kund Engagement-instans för att kunna installera en lösning och följa de här anvisningarna.
* Du måste vara klient administratör för att skapa ett nytt tjänst konto för den lead-tjänst som används för att skicka leads från Marketplace-erbjudanden.
* Du måste ha åtkomst till administrations portalen för Office 365.
* Du måste ha åtkomst till Azure Portal.

## <a name="install-the-solution"></a>Installera lösningen

1.  Ladda ned [lösningen för Microsoft Marketplace lead Writer](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) och spara den lokalt på datorn.

2.  Öppna Dynamics 365 för kund engagemang genom att gå till URL: en för din Dynamics-instans ( `https://tenant.crm.dynamics.com`till exempel).

3.  Åtkomst inställningar genom att välja kugg hjuls ikonen och **Avancerade inställningar** i det övre navigerings fältet.
 
    ![Dynamics – avancerade inställningar](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

4.  På sidan inställningar går du till inställnings menyn från det övre navigerings fältet och väljer **lösningar**.

    >[!Note]
    >Om du inte ser alternativen i nästa skärm bild, har du inte de behörigheter du behöver för att fortsätta. Kontakta en administratör på din Dynamics 365 för kund engagemang-instans.

    ![Dynamics 365 – lösningar](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

5. På sidan lösningar väljer du **Importera** och navigera till den plats där du sparade den *Microsoft Marketplace lösningen för bly skrivare* som du laddade ned i steg 1.

    ![Dynamics 365 för kund engagemang – importera](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

6. Slutför importen av lösningen genom att följa guiden Importera lösning.

## <a name="configure-user-permissions"></a>Konfigurera användar behörigheter

Om du vill skriva leads till din Dynamics 365 for kund Engagement-instans måste du dela ett tjänst konto med oss och konfigurera behörigheter för kontot.

Använd följande steg för att skapa tjänst kontot och tilldela behörigheter. Du kan använda **Azure Active Directory** eller **Office 365**.

>[!Note]
>Baserat på det autentiseringsalternativ du väljer kan du gå vidare till motsvarande instruktioner baserat på ditt val. Se [Azure Active Directory](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#azure-active-directory) eller [Office 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#office-365).

### <a name="azure-active-directory"></a>Azure Active Directory

Vi rekommenderar det här alternativet eftersom du får den extra fördelen att du aldrig behöver uppdatera ditt användar namn/lösen ord för att få leads. Om du vill använda alternativet Azure Active Directory anger du app-ID, program nyckel och katalog-ID från ditt Active Directory-program.

Använd följande steg för att konfigurera Azure Active Directory för Dynamics 365 för kund engagemang.

1. Logga in på [Azure Portal](https://portal.azure.com/)och välj sedan tjänsten Azure Active Directory i det vänstra navigerings fältet.

2. Välj **Egenskaper** från Azure Active Directory vänstra navigerings fältet och kopiera värdet för **katalog-ID** på sidan. Spara det här värdet eftersom det är det *katalog-ID-* värde som du måste ange i publicerings portalen för att ta emot leads för ditt Marketplace-erbjudande.

    ![Azure Active Directory-egenskaper](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

3. Välj **Appregistreringar** i det Azure Active Directory vänstra navigerings fältet och välj sedan **ny registrering** på den sidan.
4. Ange ett namn på program namnet. Ange ett meningsfullt program namn.
5. Under konto typer som stöds väljer du **konton i valfri organisations katalog**.
6. Under omdirigerings-URI väljer du **webb** och anger en URI `https://contosoapp1/auth`(till exempel). 
7. Välj **Registrera**.

    ![Registrera ett program](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

8. Nu när ditt program är registrerat går du till programmets översikts sida och kopierar **ID-värdet för appen (klienten)** på sidan. Spara det här värdet eftersom det är det *program-ID (klient)* som du måste ange i publicerings portalen och i Dynamics för att ta emot leads för ditt Marketplace-erbjudande.

    ![Program-ID (klient)](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

9. Välj **certifikat och hemligheter** från appens vänstra navigering och välj **ny klient hemlighet** på den sidan. Ange en meningsfull beskrivning för klient hemligheten och välj alternativet **aldrig** under upphör att gälla. Välj **Lägg till** för att skapa klient hemligheten.

    ![Program – certifiering och hemligheter](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

10. **Kopiera klientens hemliga värde**så fort klient hemligheten har skapats. Du kan inte hämta värdet när du har navigerat bort från sidan. Spara det här värdet eftersom det är *klientens hemliga* värde som du måste ange i publicerings portalen för att ta emot leads för ditt Marketplace-erbjudande. 
11. Välj **API-behörigheter** från appens vänstra navigering och välj sedan **Lägg till en behörighet**.
12. Välj Microsoft API: er och välj sedan **Dynamics CRM** som API.
13. Kontrol lera att **delegerade behörigheter** är markerat under *vilken typ av behörigheter som krävs för ditt program*. Kontrol lera behörigheten för **user_impersonation** *åtkomst common data service som organisations användare*. Välj **Lägg till behörigheter**.

    ![Lägg till behörigheter](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

14. När du har slutfört steg 1-13 på Azure Portal navigerar du till din Dynamics 365-instans för kund engagemang genom att gå till URL: `https://tenant.crm.dynamics.com`en (till exempel).
15. Öppna inställningar genom att välja kugg hjuls ikonen och **Avancerade inställningar** i det övre navigerings fältet.
16. På sidan inställningar går du till inställnings menyn från det övre navigerings fältet och väljer **säkerhet**.
17. På sidan säkerhet väljer du **användare**.  På sidan användare väljer du List rutan "aktiverade användare" för att växla över till **program användare**.
18. Välj **ny** för att skapa en ny användare. 

    ![Skapa en ny användare](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

19. I **ny användare**kontrollerar du att användaren: PROGRAM användare har valts. Ange ett användar namn, fullständigt namn och e-postadress för den användare som du vill använda med den här anslutningen. Klistra också in **program-ID: t** för den app som du skapade i Azure Portal från steg 8. Klicka på **Spara och Stäng** för att slutföra tillägget av användaren.

    ![Ny användare](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

20. Gå till "säkerhets inställningar" i den här artikeln om du vill slutföra konfigurationen av anslutningen för den här användaren.

### <a name="office-365"></a>Office 365

Om du inte vill använda Azure Active Directory kan du registrera en ny användare på *Microsoft 365 administrations Center*. Du måste uppdatera ditt användar namn/lösen ord var 90 dag om du vill fortsätta få leads.

Använd följande steg för att konfigurera Office 365 för Dynamics 365 för kund engagemang.

1. Logga in på [Microsoft 365 administrations Center](https://admin.microsoft.com).

2. Välj **Lägg till en användare**.

    ![Microsoft 365 administrations Center – Lägg till en användare](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

4. Skapa en ny användare för tjänsten för lead Writer. Konfigurera följande inställningar:

    * Ange ett användar namn
    * Ange ett lösen ord och avmarkera alternativet "låt den här användaren ändra sitt lösen ord när de loggar in första gången".
    * Välj "användare (ingen administratörs åtkomst)" som roll för användaren.
    * Välj "Dynamics 365 Customer Engagement plan" som produkt licens som visas i nästa skärmdump. Du debiteras för den licens du väljer. 

Spara värdena som de är *användar namn och lösen ord* som du måste ange i publicerings portalen för att ta emot leads för ditt Marketplace-erbjudande.

![Microsoft 365 administrations Center – ny användare](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>Säkerhetsinställningar

Det sista steget är att aktivera den användare som du skapade för att skriva leads.

1. Öppna Dynamics 365 för kund engagemang genom att gå till URL: en för din Dynamics-instans ( `https://tenant.crm.dynamics.com`till exempel).
2. Åtkomst inställningar genom att välja kugg hjuls ikonen och **Avancerade inställningar** i det övre navigerings fältet.
3. På sidan inställningar går du till inställnings menyn från det övre navigerings fältet och väljer **säkerhet**.
4. På sidan säkerhet väljer du **användare** och väljer den användare som du skapade i avsnittet Konfigurera användar behörigheter i det här dokumentet och väljer sedan **hantera roller**. 

    ![Hantera roller](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

5. Sök efter roll namnet "Microsoft Marketplace lead Writer" och välj den för att tilldela användaren rollen.

    ![Hantera användarroller](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!Note]
    >Den här rollen skapas av den lösning som du har importerat och har bara behörighet att skriva leads och spåra lösnings versionen för att säkerställa kompatibiliteten.

6. Gå tillbaka till sidan säkerhet och välj **säkerhets roller**. Sök efter rollen Microsoft Marketplace lead Writer och markera den.

    ![Säkerhets roller](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

7. När du är i säkerhets rollen väljer du fliken **kärn poster** . Sök efter entiteten användar gränssnitt för användar enheter och aktivera behörigheterna Skapa, läsa och skriv till användare (1/4 gul cirkel) för den entiteten genom att klicka en gång i varje motsvarande cirklar.

    ![Microsoft Marketplace lead Writer-Core-poster](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

8. Gå nu till fliken **anpassning** . Sök Tor i entiteten "system jobb" och aktiverar Läs-, skriv-och AppendTo-behörighet till organisation (heldragen grön) för den entiteten genom att klicka fyra gånger i var och en av motsvarande cirklar.

    ![Microsoft Marketplace ledar skrivare – anpassning](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

9. **Spara och Stäng**.

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-for-customer-engagement"></a>Konfigurera ditt erbjudande för att skicka leads till Dynamics 365 för kund engagemang

När du är redo att konfigurera ledar hanterings informationen för ditt erbjudande i publicerings portalen följer du stegen nedan:

1. Gå till sidan med **installations programmet** för erbjudandet.
2. Välj **Anslut** under avsnittet ledar hantering.

    ![Ansluta till lead management](./media/commercial-marketplace-lead-management-instructions-dynamics/connect-lead-management.png)

3. I popup-fönstret anslutnings information väljer du **Dynamics 365 för kund engagemang** för lead-målet

    ![Anslutnings information – lead-mål](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

4. Ange den **URL för Dynamics 365** -instansen som `https://contoso.crm4.dynamics.com`.
5. Välj autentiseringsmetod, Azure Active Directoryeller Office 365. 
6. Om du har valt Azure Active Directory anger du **programmets (klient) ID** (exempel: `23456052-aaaa-bbbb-8662-1234df56788f`), **katalog-ID** (exempel `12345678-8af1-4asf-1234-12234d01db47`:) och **klient hemlighet** (exempel: `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`).

    ![Anslutnings information – Azure Active Directory](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

7. Om du har valt Office 365 anger du **användar namnet** (exempel: `contoso@contoso.onmicrosoft.com`) och lösen ord (exempel: `P@ssw0rd`).

    ![Anslutnings information – användar namn](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

>[!Note]
>Du måste slutföra konfigurationen av resten av erbjudandet och publicera den innan du kan ta emot leads för erbjudandet.
