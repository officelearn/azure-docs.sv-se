---
title: Dynamics CRM | Azure Marketplace
description: Konfigurera leadhantering för Dynamics CRM.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: d64c8460f5653f28b96396025f29ea13af15c8c3
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416308"
---
# <a name="configure-lead-management-for-dynamics-crm-online"></a>Konfigurera leadhantering för Dynamics CRM online

I den här artikeln beskrivs hur du ställer in Dynamics CRM Online för att bearbeta försäljningsleads.

## <a name="prerequisites"></a>Krav

Följande användarbehörigheter måste slutföra stegen i den här artikeln:
- Du måste vara administratör i Din Dynamics CRM Online-instans för att kunna installera en lösning.
- Du måste vara klientadministratör för att skapa ett nytt tjänstkonto för leadtjänst.

<a name="install-the-solution"></a>Installera lösningen
--------------------

1.  Ladda ned [Microsoft Marketplace Lead Writer-lösningen](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) och spara den lokalt.

2.  Öppna Dynamics CRM Online och gå till Inställningar.
    >[!NOTE]
    >Om du inte ser alternativen i nästa skärminspelning har du inte de behörigheter du behöver.
 
       ![Vyn Inställningar för Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline1.png)

3.  Välj **Importera**och välj sedan den lösning som du hämtade i steg 1.
 
    ![Alternativ för import av Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline2.png)

4.  Slutför installationen av lösningen.

## <a name="configure-user-permissions"></a>Konfigurera användarbehörigheter

För att skriva leads till din Dynamics CRM-instans måste du dela ett tjänstkonto med oss och konfigurera behörigheter för kontot.

Följ följande steg för att skapa tjänstkontot och tilldela behörigheter. Du kan använda **Azure Active Directory** eller Office **365**.

### <a name="azure-active-directory"></a>Azure Active Directory

Vi rekommenderar det här alternativet eftersom du får den extra fördelen att aldrig behöva uppdatera ditt användarnamn / lösenord för att fortsätta få leads. Om du vill använda alternativet Azure Active Directory anger du app-ID-, programnyckel- och katalog-ID:et från Active Directory-programmet.

Använd följande steg för att konfigurera Azure Active Directory för Dynamics CRM.

1.  Logga in på [Azure-portalen](https://portal.azure.com/) och välj sedan Azure Active Directory-tjänsten.

2.  Välj **Egenskaper** och kopiera sedan **katalog-ID.** Det här är din klientkontoidentifiering som du behöver använda i Cloud Partner Portal.

    ![Hämta katalog-ID](./media/cloud-partner-portal-lead-management-instructions-dynamics/directoryid.png)

3.  Välj **Appregistreringar**och välj sedan **Ny programregistrering**.
4.  Ange programmets namn.
5.  För Typ väljer du **Webbapp/ API**.
6.  Ange en webbadress. Det här fältet behövs inte för leads, men krävs för att skapa ett program.
7. Välj **Skapa**.
8.  Nu när programmet har registrerats väljer du **Egenskaper** och väljer sedan **kopiera program-ID.** Du använder den här anslutningsinformationen i Cloud Partner Portal.
9.  I Egenskaper anger du programmet som Flera klienter och väljer sedan **Spara**.

10. Välj **Nycklar** och skapa en ny nyckel med varaktigheten inställd *på Aldrig upphör att gälla*. Välj **Spara** om du vill skapa nyckeln. 
11. Välj **Kopiera nyckelvärdet på Keys-menyn.** Spara en kopia av det här värdet eftersom du behöver det för Cloud Partner Portal.
    
    ![Dynamics få registrerad nyckel](./media/cloud-partner-portal-lead-management-instructions-dynamics/registerkeys.png)
    
12. Välj **Behörigheter som krävs** och välj sedan Lägg **till**. 
13. Välj **Dynamics CRM Online** som nytt API och kontrollera behörigheten för Access CRM Online som *organisationsanvändare*.

14. På Dynamics CRM går du till Användare och väljer listrutan "Aktiverade användare" för att växla över till **Programanvändare**.
    
    ![Programanvändare](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuserfirst.PNG)

15. Välj **Ny** om du vill skapa en ny användare. Välj listrutan ANVÄNDARE AV ANVÄNDARE AV ANVÄNDARE AV ANVÄNDARE AV **ANVÄNDARE.**
    
    ![Lägga till ny programanvändare](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuser.PNG)

16. Ange namn och e-post som du vill använda med den här anslutningen i **Ny**användare. Klistra in **i program-ID** för appen som du skapade i Azure-portalen.

     ![Konfigurera ny användare](./media/cloud-partner-portal-lead-management-instructions-dynamics/leadgencreateuser.PNG)

17. Gå till "Säkerhetsinställningar" i den här artikeln för att slutföra konfigurationen av anslutningen för den här användaren.

### <a name="office-365"></a>Office 365

Om du inte vill använda Azure Active Directory kan du registrera en ny användare i *Microsoft 365 admincenter*. Du måste uppdatera ditt användarnamn/lösenord var 90:e dag för att fortsätta få leads.

Använd följande steg för att konfigurera Office 365 för Dynamics CRM.

1. Logga in på [Administrationscenter för Microsoft 365](https://admin.microsoft.com).

2. Välj panelen **Admin.**

    ![Administration av Office Online](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline3.png)

3. Välj **Lägg till en användare**.

    ![Lägga till en användare](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline4.png)

4. Skapa en ny användare för lead writer-tjänsten. Konfigurera följande inställningar:

    -   Ange ett lösenord och avmarkera alternativet "Ändra lösenordet när användaren ändrar sitt lösenord när de loggar in för första gången".
    -   Välj "Användare (ingen administratörsåtkomst)" som roll för användaren.
    -   Välj produktlicensen som visas i nästa skärminspelning. Du debiteras för den licens du väljer. Lösningen kommer också att fungera med Dynamics CRM Online Basic-licens.
    
    ![Konfigurera användarbehörigheter och -licens](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline5.png)

## <a name="security-settings"></a>Säkerhetsinställningar

Det sista steget är att aktivera användaren som du skapade för att skriva leads.

1.  Logga in på Dynamics CRM online.
2.  Välj **Säkerhet**i **Inställningar**.
    
    ![Säkerhetsinställningar](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline6.png)

3.  Markera den användare som du skapade i **Användarbehörigheter**och välj sedan **Hantera användarroller**. Kontrollera **att Microsoft Marketplace Lead Writer** tilldelar rollen.

    ![Tilldela användarroll](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline7.png)\

    >[!NOTE]
    >Den här rollen skapas av lösningen som du importerade och har bara behörighet att skriva leads och spåra lösningsversionen för att säkerställa kompatibilitet.

4.  I Säkerhet väljer du **Säkerhetsroller** och hittar rollen för Microsoft Marketplace Lead Writer.
    
    ![Konfigurera brännare för säkerhetsleadser](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline10.jpg)\

5. Välj fliken **Kärnposter.** Aktivera Skapa/läs/skriv för användarentitetsgränssnittet.

    ![Aktivera skapa/läsa/skriva för användaren](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline11.jpg)\

## <a name="wrap-up"></a>Avsluta

Slutför konfigurationen av Dynamics CRM för leadhantering genom att lägga till den genererade kontoinformationen i Cloud Partner Portal. Ett exempel:

-   **Azure Active Directory** - **Application Id** (exempel: *23456052-aaaaa-bbbb-8662-1234df56788f*), **Katalog-ID** (exempel: *12345678-8af1-4asf-1234-12234d01db47*), och **application key** (exempel: *1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=*).
-   **Office 365** - **Url** **`https://contoso.crm4.dynamics.com`**(exempel: ), **`contoso\@contoso.onmicrosoft.com`** **Användarnamn** (exempel: ), och **Lösenord** (exempel: *P\@ssw0rd*).
