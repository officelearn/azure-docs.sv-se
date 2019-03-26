---
title: Dynamics CRM | Microsoft Docs
description: Konfigurera lead-hantering för Dynamics CRM.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: a1398d172a5c578ec3c0f16627eadd1da3fd1e45
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437631"
---
# <a name="configure-lead-management-for-dynamics-crm-online"></a>Konfigurera lead-hantering för Dynamics CRM online

Den här artikeln beskriver hur du ställer in Dynamics CRM Online för att bearbeta säljleads.

## <a name="prerequisites"></a>Förutsättningar

Följande användarbehörigheter är behöver för att slutföra stegen i den här artikeln:
- Du måste vara administratör för din Dynamics CRM Online-instans att installera en lösning.
- Du måste vara Innehavaradministratör för att skapa ett nytt tjänstkonto för lead-tjänsten.

<a name="install-the-solution"></a>Installera lösningen
--------------------

1.  Ladda ned den [lösning för Microsoft Marketplace leda Writer](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) och spara den lokalt.

2.  Öppna Dynamics CRM Online och gå till inställningar.
    >[!NOTE]
    >Om du inte ser alternativen i nästa skärmdumpen kan har du inte de behörigheter som du behöver.
 
       ![Konfigurationsvy för Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline1.png)

3.  Välj **Import**, och välj sedan den lösning som du hämtade i steg 1.
 
    ![Dynamics importalternativ](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline2.png)

4.  Installationen av lösningen.

## <a name="configure-user-permissions"></a>Konfigurera användarbehörigheter

Att skriva leads till Dynamics CRM-instansen du behöva dela ett tjänstkonto med oss och konfigurera behörigheter för kontot.

Använd följande steg för att skapa kontot och tilldela behörigheter. Du kan använda **Azure Active Directory** eller **Office 365**.

### <a name="azure-active-directory"></a>Azure Active Directory

Vi rekommenderar det här alternativet eftersom du får den ytterligare fördelen med aldrig behöver uppdatera ditt användarnamn/lösenord för att kunna fortsätta för att få leads. Om du vill använda Azure Active Directory-alternativet Ange du den App-Id och Programnyckel katalog-Id från Active Directory-program.

Använd följande steg för att konfigurera Azure Active Directory för Dynamics CRM.

1.  Logga in på [Azure-portalen](https://portal.azure.com/) och välj sedan Azure Active Directory-tjänsten.

2.  Välj **egenskaper** och kopierar sedan de **katalog-Id**. Det här är din klient-ID: t som du behöver använda i partnerportalen i molnet.

    ![Hämta katalog-ID](./media/cloud-partner-portal-lead-management-instructions-dynamics/directoryid.png)

3.  Välj **appregistreringar**, och välj sedan **ny programregistrering**.
4.  Ange namnet på programmet.
5.  Typ, Välj **webbapp / API**.
6.  Ange en URL. Det här fältet behövs inte för leads, men krävs för att skapa ett program.
7. Välj **Skapa**.
8.  Nu när ditt program har registrerats, Välj **egenskaper** och välj sedan **kopiera program-Id**. Du använder den här anslutningsinformationen i partnerportalen i molnet.
9.  I egenskaperna för att ange programmet som med flera innehavare och välj sedan **spara**.

10. Välj **nycklar** och skapa en ny nyckel med varaktigheten inställd *upphör aldrig att gälla*. Välj **spara** att skapa nyckeln. 
11. Välj på menyn nycklar **kopiera nyckelvärdet.** Spara en kopia av det här värdet eftersom du behöver för partnerportalen i molnet.
    
    ![Dynamics hämta registrerade nyckel](./media/cloud-partner-portal-lead-management-instructions-dynamics/registerkeys.png)
    
12. Välj **nödvändiga behörigheter** och välj sedan **Lägg till**. 
13. Välj **Dynamics CRM Online** som den nya API: et och kontrollera behörigheten för *åtkomst CRM Online som organisationsanvändare*.

14. Gå till användare på Dynamics CRM och Välj ”användare”-listrutan för att växla till **programanvändare**.
    
    ![Programanvändare](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuserfirst.PNG)

15. Välj **New** att skapa en ny användare. Välj den **användaren: PROGRAMANVÄNDARE** listrutan.
    
    ![Lägg till ny användare för programmet](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuser.PNG)

16. I **ny användare**, ange namnet och e-post som du vill använda med den här anslutningen. Klistra in den **program-Id** för appen som du skapat i Azure-portalen.

     ![Konfigurera ny användare](./media/cloud-partner-portal-lead-management-instructions-dynamics/leadgencreateuser.PNG)

17. Gå till ”-säkerhetsinställningar” i den här artikeln för att slutföra konfigurationen av anslutningen för den här användaren.

### <a name="office-365"></a>Office 365

Om du inte vill använda Azure Active Directory, kan du registrera en ny användare på den *Microsoft 365 Administrationscenter*. Du kommer att behöva uppdatera ditt användarnamn/lösenord var 90: e dag för att fortsätta få leads.

Använd följande steg för att konfigurera Office 365 för Dynamics CRM.

1. Logga in på den [Microsoft 365 Administrationscenter](https://admin.microsoft.com).

2. Välj den **Admin** panelen.

    ![Office Online Admin](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline3.png)

3. Välj **lägga till en användare**.

    ![Lägga till en användare](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline4.png)

4. Skapa en ny användare för lead-skrivartjänsten. Konfigurera följande inställningar:

    -   Ange ett lösenord och avmarkera alternativet ”gör den här användaren ändra sina lösenord när de loggar in första gången”.
    -   Välj ”användare (ingen administratörsåtkomst)” som rollen för användaren.
    -   Välj den produktlicens som visas i nästa skärmdump. Du debiteras för den licens som du väljer. Lösningen fungerar även med Dynamics CRM Online Basic-licens.
    
    ![Konfigurera användarbehörigheter och licens](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline5.png)

## <a name="security-settings"></a>Säkerhetsinställningar

Det sista steget är att aktivera användaren du skapade för att skriva de potentiella kunderna.

1.  Logga in på Dynamics CRM online.
2.  På **inställningar**väljer **Security**.
    
    ![Säkerhetsinställningar](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline6.png)

3.  Välj den användare som du skapade i **användarbehörigheter**, och välj sedan **Hantera användarroller**. Kontrollera **Microsoft Marketplace leda Writer** du tilldela rollen.

    ![Tilldela rollen](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline7.png)\

    >[!NOTE]
    >Den här rollen har skapats av lösningen att du importerat och endast har behörighet att skriva de potentiella kunderna och spåra lösningsversion för att säkerställa kompatibilitet.

4.  Security, Välj **säkerhetsroller** och hitta rollen för Microsoft Marketplace leda Writer.
    
    ![Konfigurera säkerhet lead-skrivaren](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline10.jpg)\

5. Välj den **Kärnposter** fliken. Aktivera skapa/läsa/skriva för användarentiteten Användargränssnittet.

    ![Aktivera skapa/läsa/skriva för användare](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline11.jpg)\

## <a name="wrap-up"></a>Avsluta

Slutföra konfigurationen av Dynamics CRM för lead-hantering genom att lägga till informationen om genererade partnerportalen i molnet. Exempel:

-   **Azure Active Directory** - **program-Id** (exempel: *23456052-AAAA-bbbb-8662-1234df56788f*), **katalog-Id** (exempel: *12345678-8af1-4asf-1234-12234d01db47*), och **Programnyckel** (exempel: *1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=*).
-   **Office 365** - **Url** (exempel: *https://contoso.crm4.dynamics.com*), **användarnamn** (exempel: *contoso\@ Contoso.onmicrosoft.com*), och **lösenord** (exempel: *P\@ssw0rd*).
