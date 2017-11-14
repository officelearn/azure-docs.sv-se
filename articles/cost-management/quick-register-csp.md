---
title: Registrera med Azure kostnaden Management CSP partnerinformation | Microsoft Docs
description: "Använd din CSP partnerinformation registreras med Azure kostnaden Management med Cloudyn."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 11/13/2017
ms.topic: quickstart
ms.custom: mvc
ms.service: cost-management
manager: carmonm
ms.openlocfilehash: 84f2fec61f791d4fc9264eaa01e24180696da853
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2017
---
# <a name="register-with-the-csp-partner-program-and-view-cost-data"></a>Registrera med CSP-Partner-program och visa data

Du kan registrera med Azure kostnaden Management med Cloudyn som CSP-partner. Registreringen ger åtkomst till Cloudyn-portalen. Den här snabbstartsguide information registreringsprocessen behövs för att skapa en utvärderingsprenumeration Cloudyn och logga in på Cloudyn-portalen. Den visar också hur du startar visa kostnadsdata direkt.


>[!NOTE]

>Endast Kryptografiprovider direkt partners och leverantörer av CSP indirekt kan slutföra Cloudyn registreringen.
>
>Konfigurera Partner Center API krävs för autentisering och åtkomst till data. Ett konto som Global administratör för Partner Center behövs för att etablera API-åtkomst.
Mer information finns i [Anslut till Partner Center API](https://msdn.microsoft.com/library/partnercenter/mt709136.aspx).
>
>Åtkomst till Cloudyn kan göras tillgänglig för CSP indirekt återförsäljare när deras indirekt kryptografiprovidern registreras i Cloudyn. CSP indirekt återförsäljare kan sedan ge Cloudyn åtkomst till Azure-kunder och prenumerationer.

## <a name="log-in-to-azure"></a>Logga in på Azure

- Logga in på Azure Portal på http://portal.azure.com.

## <a name="create-a-trial-registration"></a>Skapa en utvärderingsregistrering

1. I Azure-portalen klickar du på **kostnaden Management + fakturering** i listan över tjänster.
2. Under **översikt**, klickar du på **kostnaden Management**  
    ![Kostnad sidan för hantering](./media/quick-register-csp/cost-mgt-billing-service.png)
3. På den **kostnaden Management** klickar du på **går du till kostnaden Management** att öppna sidan Cloudyn registrering i ett nytt fönster.
4. På sidan Cloudyn portal utvärderingsregistrering skriver du ditt företagsnamn, markerar **Microsoft CSP Partner programadministratören**, och klicka sedan på **nästa**.  
5. Ange en **program-ID**, **Commerce ID**, **programmet hemlig nyckel**, och välj den **standard priser planera**. Om du inte har informationen till hands, logga in på portalen Partner Center på [https://partnercenter.microsoft.com](https://partnercenter.microsoft.com) primära administratören kontot och gör du följande:
  1. Gå till **instrumentpanelen** > **kontoinställningar** > **Apphantering**.
  2. Hoppa över det här steget om du tidigare har skapat ett webbprogram. Annars klickar du på **Lägg till ny webbapp** i den **Web App** avsnitt.
  3. Kopiera den **App-ID** GUID från ditt webbprogram.
  4. Kopiera den **Commerce ID** GUID från ditt webbprogram.
  5. Välj giltighetstid för nyckeln som en eller två år efter behov. Välj **Lägg till nyckel** och sedan kopiera och spara värdet för hemliga nyckeln.  
    ![CSP Partner Center](./media/quick-register-csp/csp-partner-center.png)
  6. Gå tillbaka till registreringssidan och klistra in informationen.  
      ![CSP autentiseringsuppgifter](./media/quick-register-csp/csp-reg.png)
6. Godkänner du användningsvillkoren och sedan validera dina uppgifter. Klicka på **nästa** att auktorisera Cloudyn att samla in data i Azure-resurs. Data som samlas in innehåller användning, prestanda, fakturering och taggdata från dina prenumerationer.  
7. Under **Bjud in andra berörda parter**, du kan lägga till användare genom att skriva sina e-postadresser. När du är klar klickar du på **nästa**. Det tar ungefär två timmar för alla dina faktureringsinformation att läggs till Cloudyn.
8. Klickar du på **går du till Cloudyn** att öppna Cloudyn portal och klicka sedan på den **konton Molnhantering** sidan du bör se din registrerade CSP-kontoinformation.

## <a name="configure-indirect-csp-access-in-cloudyn"></a>Konfigurera indirekt CSP-åtkomst i Cloudyn

Partner Center API är endast tillgänglig för att dirigera CSP: er som standard. En direkt CSP-provider kan dock konfigurera åtkomst för indirekt CSP kunder eller partners med hjälp av entitetsgrupper i Cloudyn.

Om du vill aktivera åtkomst för indirekt CSP kunder eller partners, följer du stegen i [skapa en utvärderingsregistrering](#create-a-trial-registration) att ställa in en utvärderingsversion registrering. Därefter slutföra följande steg för att segment indirekt CSP-data med Cloudyn entitetsgrupper. Sedan tilldela lämpliga användarbehörigheter till grupperna entitet.

1. Skapa en grupp för entiteten med informationen på [skapa entiteter](tutorial-user-access.md#create-entities).
2. Följ stegen i [tilldela kostnaden entiteter prenumerationer](https://support.cloudyn.com/hc/en-us/articles/115005139425-Video-Assigning-subscriptions-to-Cost-Entities). Associera indirekt CSP kundens konto och deras Azure-prenumerationer till den enhet som du har skapat tidigare.
3. Följ stegen i [skapa en användare med administratörsåtkomst](tutorial-user-access.md#create-a-user-with-admin-access) att skapa ett konto med administratörsåtkomst. Kontrollera sedan att användarkontot har administratörsåtkomst till de specifika enheter som du skapade tidigare för indirekt kontot.

Indirekt CSP partners logga in på portalen Cloudyn med hjälp av konton som du skapade för dem.


[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten används du CSP-information för att registrera med hantering av kostnaden. Du loggat in på portalen Cloudyn och igång visa kostnadsdata. Om du vill veta mer om Azure kostnaden Management med Cloudyn kan fortsätta att kursen för hantering av kostnaden.

> [!div class="nextstepaction"]
> [Granska användning och kostnader](./tutorial-review-usage.md)
