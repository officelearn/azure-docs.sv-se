---
title: Introduktions-API för Azure-appar på den kommersiella marknadsplatsen
description: API-förutsättningar för Azure-appar på kommersiell marknadsplats på Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: cc4d56058ce3985ec3a1d9124ef4ec73ff6be1a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279767"
---
# <a name="api-for-onboarding-azure-apps-in-partner-center"></a>API för introduktion av Azure-appar i Partner Center

Använd *API:et för inlämnande av partnercenter* för att programmässigt fråga, skapa inlämningar för och publicera Azure-erbjudanden.  Det här API:et är användbart om ditt konto hanterar många erbjudanden och du vill automatisera och optimera inlämningsprocessen för dessa erbjudanden.

## <a name="api-prerequisites"></a>API-förutsättningar

Det finns några programmatiska resurser som du behöver för att kunna använda Partner Center-API:et för Azure-produkter: 

- ett Azure Active Directory-program.
- en Azure Active Directory (Azure AD) åtkomsttoken.

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>Steg 1: Slutför förutsättningar för att använda API:et för inlämnande av Partnercenter

Innan du börjar skriva kod för att anropa Api:et för partnercenter, se till att du har slutfört följande förutsättningar.

- Du (eller din organisation) måste ha en Azure AD-katalog och du måste ha [global administratörsbehörighet](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) för katalogen. Om du redan använder Office 365 eller andra företagstjänster från Microsoft har du redan Azure AD-katalog. Annars kan du [skapa en ny Azure AD i Partner Center](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account) utan extra kostnad.

- Du måste [associera ett Azure AD-program med ditt Partner Center-konto](https://docs.microsoft.com/windows/uwp/monetize/create-and-manage-submissions-using-windows-store-services#associate-an-azure-ad-application-with-your-windows-partner-center-account) och skaffa ditt klient-ID, klient-ID och nyckel. Du behöver dessa värden för att hämta en Azure AD-åtkomsttoken som du använder i anrop till Microsoft Store-inlämnings-API:et.

#### <a name="how-to-associate-an-azure-ad-application-with-your-partner-center-account"></a>Så här associerar du ett Azure AD-program med ditt Partner Center-konto

Om du vill använda Microsoft Store-inlämnings-API:et måste du associera ett Azure AD-program med ditt Partner Center-konto, hämta klient-ID och klient-ID för programmet och generera en nyckel. Azure AD-programmet representerar den app eller tjänst som du vill anropa API:et för partnercenter. Du behöver klient-ID, klient-ID och nyckel för att få en Azure AD-åtkomsttoken som du skickar till API:et.

>[!Note]
>Du behöver bara utföra den här uppgiften en gång. När du har klient-ID, klient-ID och nyckel kan du återanvända dem när du behöver skapa en ny Azure AD-åtkomsttoken.

1. Associera organisationens Partner Center-konto i Partner Center i Partner Center [med organisationens Azure AD-katalog](https://docs.microsoft.com/windows/uwp/publish/associate-azure-ad-with-partner-center).
1. Lägg sedan [till Azure AD-programmet](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#add-azure-ad-applications-to-your-partner-center-account) som representerar appen eller tjänsten som du använder för att komma åt inlämningar för ditt Partner Center-konto från sidan **Användare** i avsnittet **Kontoinställningar** i Partnercenter. Se till att du tilldelar det här programmet rollen **Chef.** Om programmet ännu inte finns i din Azure AD-katalog kan du [skapa ett nytt Azure AD-program i Partner Center](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account).
1. Gå tillbaka till sidan **Användare,** klicka på namnet på ditt Azure AD-program för att gå till programinställningarna och kopiera **klient-ID-** och **klient-ID-värdena.**
1. Klicka på **Lägg till ny nyckel**. Kopiera nedåtvärdet **Nyckel** på följande skärm. Du kan inte komma åt den här informationen igen när du har lämnat den här sidan. Mer information finns i [Hantera nycklar för ett Azure AD-program](https://docs.microsoft.com/windows/uwp/publish/add-users-groups-and-azure-ad-applications#manage-keys).

### <a name="step-2-obtain-an-azure-ad-access-token"></a>Steg 2: Hämta en Azure AD-åtkomsttoken

Innan du anropar någon av metoderna i API:et för partnercenter-inlämning måste du först hämta en Azure AD-åtkomsttoken som du skickar till **auktoriseringshuvudet** för varje metod i API:et. När du har skaffat en åtkomsttoken har du 60 minuter på dig att använda den innan den upphör att gälla. När token har gått ut kan du uppdatera token så att du kan fortsätta att använda den i framtida anrop till API:et.

Om du vill hämta åtkomsttoken följer du instruktionerna i Service `HTTP POST` to `https://login.microsoftonline.com/<tenant_id>/oauth2/token` Service Calls [Med klientautentiseringsuppgifter](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-service-to-service/) för att skicka en till slutpunkten. Här är ett exempel begäran:

JSONCopy (en)
```Json
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource= https://api.partner.microsoft.com
```

För *tenant_id* i parametrarna `POST URI` och *client_id* och *client_secret* anger du klient-ID, klient-ID och nyckeln för ditt program som du hämtade från Partnercenter i föregående avsnitt. För *resursparametern* måste `https://api.partner.microsoft.com`du ange .

### <a name="step-3-use-the-microsoft-store-submission-api"></a>Steg 3: Använda Microsoft Store-inlämnings-API:et

När du har en Azure AD-åtkomsttoken kan du anropa metoder i API:et för partnercenter. Om du vill skapa eller uppdatera inlämningar anropar du vanligtvis flera metoder i API:et för partnercenter i en viss ordning. Information om varje scenario och syntaxen för varje metod finns i api-skrytbekrytning för inmatning.

https://apidocs.microsoft.com/services/partneringestion/
