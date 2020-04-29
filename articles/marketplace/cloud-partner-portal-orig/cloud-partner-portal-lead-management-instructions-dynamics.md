---
title: Dynamics CRM | Azure Marketplace
description: Konfigurera ledar hantering för Dynamics CRM.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: d64c8460f5653f28b96396025f29ea13af15c8c3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416308"
---
# <a name="configure-lead-management-for-dynamics-crm-online"></a>Konfigurera ledar hantering för Dynamics CRM Online

Den här artikeln beskriver hur du konfigurerar Dynamics CRM Online för att bearbeta försäljnings leads.

## <a name="prerequisites"></a>Krav

Följande användar behörigheter behövs för att slutföra stegen i den här artikeln:
- Du måste vara administratör för Dynamics CRM Online-instansen för att installera en lösning.
- Du måste vara klient administratör för att skapa ett nytt tjänst konto för lead service.

<a name="install-the-solution"></a>Installera lösningen
--------------------

1.  Ladda ned [lösningen för Microsoft Marketplace lead Writer](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) och spara den lokalt.

2.  Öppna Dynamics CRM Online och gå till inställningar.
    >[!NOTE]
    >Om du inte ser alternativen i nästa skärm bild, har du inte de behörigheter som du behöver.
 
       ![Dynamics setup-vy](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline1.png)

3.  Välj **Importera**och välj sedan den lösning som du laddade ned i steg 1.
 
    ![Dynamics import alternativ](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline2.png)

4.  Slutför installationen av lösningen.

## <a name="configure-user-permissions"></a>Konfigurera användar behörigheter

Om du vill skriva leads i Dynamics CRM-instansen måste du dela ett tjänst konto med oss och konfigurera behörigheter för kontot.

Använd följande steg för att skapa tjänst kontot och tilldela behörigheter. Du kan använda **Azure Active Directory** eller **Office 365**.

### <a name="azure-active-directory"></a>Azure Active Directory

Vi rekommenderar det här alternativet eftersom du får den extra fördelen att du aldrig behöver uppdatera ditt användar namn/lösen ord för att kunna fortsätta få leads. Om du vill använda alternativet Azure Active Directory anger du app-ID, program nyckel och katalog-ID från ditt Active Directory-program.

Använd följande steg för att konfigurera Azure Active Directory för Dynamics CRM.

1.  Logga in på [Azure Portal](https://portal.azure.com/) och välj sedan tjänsten Azure Active Directory.

2.  Välj **Egenskaper** och kopiera sedan **katalog-ID: t**. Detta är ditt klient kontos ID som du behöver använda i Cloud Partner Portal.

    ![Hämta katalog-ID](./media/cloud-partner-portal-lead-management-instructions-dynamics/directoryid.png)

3.  Välj **Appregistreringar**och välj sedan **ny program registrering**.
4.  Ange programmets namn.
5.  För typ väljer du **webbapp/API**.
6.  Ange en URL. Det här fältet behövs inte för leads, men det krävs för att skapa ett program.
7. Välj **Skapa**.
8.  Nu när ditt program är registrerat väljer du **Egenskaper** och sedan **Kopiera program-ID**. Du kommer att använda den här anslutnings informationen i Cloud Partner Portal.
9.  I egenskaper anger du programmet som flera innehavare och väljer sedan **Spara**.

10. Välj **nycklar** och skapa en ny nyckel med varaktigheten inställd på *upphör aldrig att gälla*. Klicka på **Spara** för att skapa nyckeln. 
11. På menyn nycklar väljer **du kopiera värdet nyckel.** Spara en kopia av det här värdet eftersom du behöver det för Cloud Partner Portal.
    
    ![Dynamics get-registrerad nyckel](./media/cloud-partner-portal-lead-management-instructions-dynamics/registerkeys.png)
    
12. Välj **nödvändiga behörigheter** och välj sedan **Lägg till**. 
13. Välj **Dynamics CRM Online** som det nya API: et och kontrol lera behörigheten för *åtkomst till CRM Online som organisations användare*.

14. I Dynamics CRM går du till användare och väljer List rutan "aktiverade användare" för att växla över till **program användare**.
    
    ![Program användare](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuserfirst.PNG)

15. Välj **ny** för att skapa en ny användare. Välj List rutan **användare: program användare** .
    
    ![Lägg till ny program användare](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuser.PNG)

16. I **ny användare**anger du det namn och den e-postadress som du vill använda med den här anslutningen. Klistra in **program-ID** för den app som du skapade i Azure Portal.

     ![Konfigurera ny användare](./media/cloud-partner-portal-lead-management-instructions-dynamics/leadgencreateuser.PNG)

17. Gå till "säkerhets inställningar" i den här artikeln om du vill slutföra konfigurationen av anslutningen för den här användaren.

### <a name="office-365"></a>Office 365

Om du inte vill använda Azure Active Directory kan du registrera en ny användare på *Microsoft 365 administrations Center*. Du måste uppdatera ditt användar namn/lösen ord var 90 dag om du vill fortsätta få leads.

Använd följande steg för att konfigurera Office 365 för Dynamics CRM.

1. Logga in på [Administrationscenter för Microsoft 365](https://admin.microsoft.com).

2. Välj **Administratörs** panelen.

    ![Office Online-administratör](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline3.png)

3. Välj **Lägg till en användare**.

    ![Lägga till en användare](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline4.png)

4. Skapa en ny användare för tjänsten för lead Writer. Konfigurera följande inställningar:

    -   Ange ett lösen ord och avmarkera alternativet "Låt användaren ändra sitt lösen ord när de loggar in första gången".
    -   Välj "användare (ingen administratörs åtkomst)" som roll för användaren.
    -   Välj den produkt licens som visas i nästa skärmdump. Du debiteras för den licens du väljer. Lösningen kommer också att fungera med Dynamics CRM Online Basic-licensen.
    
    ![Konfigurera användar behörigheter och licens](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline5.png)

## <a name="security-settings"></a>Säkerhetsinställningar

Det sista steget är att aktivera den användare som du skapade för att skriva leads.

1.  Logga in på Dynamics CRM Online.
2.  I **Inställningar**väljer du **säkerhet**.
    
    ![Säkerhetsinställningar](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline6.png)

3.  Välj den användare som du skapade i **användar behörigheter**och välj sedan **hantera användar roller**. Markera **Microsoft Marketplace ledar skrivare** för att tilldela rollen.

    ![Tilldela användar roll](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline7.png)\

    >[!NOTE]
    >Den här rollen skapas av den lösning som du har importerat och har bara behörighet att skriva leads och spåra lösnings versionen för att säkerställa kompatibiliteten.

4.  I säkerhet, väljer du **säkerhets roller** och letar upp rollen för Microsoft Marketplace-ledar skrivare.
    
    ![Konfigurera säkerhet för lead-skrivare](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline10.jpg)\

5. Välj fliken **kärn poster** . Aktivera skapa/Läs/skriv för användar enhetens användar gränssnitt.

    ![Aktivera skapande/läsning/skrivning för användare](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline11.jpg)\

## <a name="wrap-up"></a>Bryt upp

Slutför konfigurationen av Dynamics CRM för ledar hantering genom att lägga till den genererade konto informationen i Cloud Partner Portal. Ett exempel:

-   **Azure Active Directory** - **program-ID** (exempel *: 23456052-AAAA-bbbb-8662-1234df56788f*), **katalog-ID** (exempel: *12345678-8af1-4asf-1234-12234d01db47*) och **program nyckel** (exempel: *1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc =*).
-   **Office 365** - **URL** (exempel **`https://contoso.crm4.dynamics.com`**:), **användar namn** (exempel **`contoso\@contoso.onmicrosoft.com`**:) och **lösen ord** (exempel *:\@P Ssw0Rd*).
