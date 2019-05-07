---
title: Skapa en Azure AD app-registrering och användare för Azure Red Hat OpenShift | Microsoft Docs
description: Lär dig hur du skapar ett huvudnamn för tjänsten, generera en klient-hemlighet och autentisering Motringnings-URL och skapa en ny Active Directory-användare för att testa appar i Microsoft Azure Red Hat OpenShift-klustret.
author: tylermsft
ms.author: twhitney
ms.service: openshift
manager: jeconnoc
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/06/2019
ms.openlocfilehash: de3f3c30848d26ea399bcccc29a6149a149f6a55
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078527"
---
# <a name="create-an-azure-ad-app-registration-and-user-for-azure-red-hat-openshift"></a>Skapa en Azure AD app-registrering och användare för Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift måste ha behörighet att utföra åtgärder åt ditt kluster. Om din organisation inte redan har en Azure Active Directory (Azure AD)-appregistrering ska användas som tjänstens huvudnamn, följer du dessa instruktioner för att skapa en.

Det här avsnittet avslutas med instruktioner för att skapa en ny Azure AD-användare som du kan behöva för att få åtkomst till appar som körs på Azure Red Hat OpenShift klustret.

Om du inte redan har skapat en Azure AD-klient, följer du anvisningarna i [skapa en Azure AD-klient för Azure Red Hat OpenShift](howto-create-tenant.md) innan du fortsätter med instruktionerna.

## <a name="create-a-new-app-registration"></a>Skapa en ny appregistrering

Ett program som vill använda funktioner i Azure AD måste först registreras i en Azure AD-klient. Registrering processen ger Azure AD-information om ditt program, till exempel URL: en där appen finns, URL: en att skicka svar när en användare autentiseras, den URI som identifierar appen och så vidare.

1. I den [Azure-portalen](https://portal.azure.com), se till att din klient visas under ditt användarnamn längst upp höger på portalen:

    ![Skärmbild av portalen med klient som anges i längst upp till höger][tenantcallout] om fel klient visas, klicka på ditt användarnamn längst upp till höger och sedan på **växla katalog**, och välj rätt klient från den **alla Kataloger** lista.

2. Öppna den [registreringar appbladet](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) och klicka på **ny programregistrering**.
3. I den **skapa** rutan Ange ett eget namn för ditt programobjekt.
4. Se till att **programtyp** är inställd på *Web app/API*.
5. Skapa en **inloggnings-URL** med hjälp av följande mönster:

    ```
    https://<cluster-name>.<azure-region>.cloudapp.azure.com
    ```

    . . . där `<cluster-name>` är avsedda namnet på ditt Azure Red Hat OpenShift-kluster och `<azure-region>` är den [Azure-region som är värd för ditt kluster i Azure Red Hat OpenShift](supported-resources.md#azure-regions). Exempel: Om klusternamnet är ska `contoso`, och du skapar den i den `eastus` region, det fullständigt kvalificerade domännamnet (FQDN) som du anger för den **inloggnings-URL** skulle vara `https://contoso.eastus.cloudapp.azure.com`

    > [!IMPORTANT]
    > Klusternamnet får bara innehålla gemener och FQDN URL: en måste vara unikt.
    > Se till att du väljer ett tillräckligt distinkta namn för klustret.

    Anteckna klusternamnet och inloggnings-URL – du behöver dem senare till få åtkomst till appar som körs på klustret. Vi ska referera till klustrets namn som `CLUSTER_NAME`, och inloggnings-URL: en som `FQDN` i den [skapa ett kluster i Azure Red Hat OpenShift](tutorial-create-cluster.md) självstudien.

6. Se till att din **inloggnings-URL** värdet verifierar med en grön bock. (Tryck på TABB för att fokusera av den *inloggnings-URL* fältet och kör verifieringen.)
7. Klicka på den **skapa** för att skapa programmet Azure AD-objektet.
8. På den **registrerad App** sida som visas, kopia nere den **program-ID**. Vi kommer att referera till det här värdet som `APPID` i den [skapa ett kluster i Azure Red Hat OpenShift](tutorial-create-cluster.md) självstudien.

    ![Skärmbild av textrutan program-ID][appidimage]
    
Mer information om Azure programobjekt finns [program och tjänstobjekt i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals).

Mer information om hur du skapar en ny Azure AD-program finns i [registrera en app med Azure Active Directory v1.0 slutpunkten](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

### <a name="create-a-client-secret"></a>Skapa en klienthemlighet

Nu är du redo att skapa en klienthemlighet för att autentisera din app till Azure Active Directory.

1. Från den **registrerad App** klickar du på **inställningar** att öppna inställningarna för registrerade appen.
2. På den **inställningar** fönstret som visas, klickar du på **nycklar**.  Den **nycklar** visas fönstret.
![Skärmbild av sidan Skapa nyckel i portalen][createkeyimage]
3. Ange en nyckel **beskrivning**.
4. Ange ett värde för **förfaller**, till exempel *i 2 år*.
5. Klicka på **spara** och nyckelvärdet visas.
6. Kopiera nyckelvärdet. Vi kommer att referera till det här värdet som `SECRET` i den [skapa ett kluster i Azure Red Hat OpenShift](tutorial-create-cluster.md) självstudien.

### <a name="create-a-reply-url"></a>Skapa en svars-URL

Azure AD använder objektet app *svars-URL* att ange återanropet som ska användas för att auktorisera appen. När det gäller en webb-API eller ett webbprogram är svars-URL den plats där Azure AD skickar autentiseringssvaret, inklusive en token om autentiseringen lyckades.

Även den minsta matchningsfel (avslutande snedstreck saknas, annat skiftläge) medför utfärdande av token-åtgärden kunde inte utföras och du kommer inte att kunna logga in.

1. Gå tillbaka till den **inställningar** fönstret (du kan klicka på **inställningar** i länken längst ned i portalen), och klicka på **Svarswebbadresser** till höger.  Den **Svarswebbadresser** visas fönstret.
2. Den första svars-URL i listan är den `FQDN` värdet från steg 6 i [skapa en ny appregistrering](#create-a-new-app-registration). Redigera den och lägga till `/oauth2callback/Azure%20AD`.  Till exempel svars-URL bör nu se ut ungefär som `https://mycluster.eastus.cloudapp.azure.com/oauth2callback/Azure%20AD`
3. Klicka på **spara** att spara svars-URL.

## <a name="create-a-new-active-directory-user"></a>Skapa en ny Active Directory-användare

Skapa en ny användare i Active Directory för att använda för att logga in på appen som körs på Azure Red Hat OpenShift klustret.

1. Gå till den [användare – alla användare](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) bladet.
2. Klicka på **+ ny användare**. Den **användaren** visas fönstret.
3. Ange en **namn** som du vill använda för den här användaren.
4. Skapa en **användarnamn** baserat på namnet på den klient du skapade med `.onmicrosoft.com` tillägg i slutet. Till exempel `yourUserName@yourTenantName.onmicrosoft.com`. Anteckna det här användarnamnet. Du behöver den för att logga in att använda appen i ditt kluster.
5. Klicka på **katalogroll** och välj **Global administratör** och klicka sedan på **Ok** längst ned i fönstret.
6. Mitt i den **användaren** fönstret klickar du på **visa lösenord** och registrera det tillfälliga lösenordet. När du har loggat in för första gången uppmanas du att återställa den.
7. Längst ned i fönstret klickar du på **skapa** att skapa användaren.

## <a name="resources"></a>Resurser

* [Program och tjänstobjekt i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)  
* [Snabbstart: Registrera en app med Azure Active Directory v1.0 slutpunkt](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)  

[appidimage]: ./media/howto-create-tenant/get-app-id.png
[createkeyimage]: ./media/howto-create-tenant/create-key.png
[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="next-steps"></a>Nästa steg

Om du har uppfyllt alla de [krav för Azure Red Hat OpenShift](howto-setup-environment.md), är du redo att skapa ditt första kluster!

Prova guiden:
> [!div class="nextstepaction"]
> [Skapa ett Azure Red Hat OpenShift-kluster](tutorial-create-cluster.md)
