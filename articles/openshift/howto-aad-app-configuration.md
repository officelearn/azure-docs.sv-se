---
title: Azure Active Directory-integrering för Azure Red Hat OpenShift
description: Lär dig hur du skapar en Azure AD-säkerhetsgrupp och användare för att testa appar i ditt Microsoft Azure Red Hat OpenShift-kluster.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: a2eade6c5a9c826d28d435a09861ba58463ae8c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280539"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Azure Active Directory-integrering för Azure Red Hat OpenShift

Om du inte redan har skapat en Azure Active Directory-klientorganisation (Azure AD) följer du anvisningarna i [Skapa en Azure AD-klient för Azure Red Hat OpenShift](howto-create-tenant.md) innan du fortsätter med dessa instruktioner.

Microsoft Azure Red Hat OpenShift behöver behörigheter för att utföra uppgifter för ditt kluster. Om din organisation inte redan har en Azure AD-användare, Azure AD-säkerhetsgrupp eller en Azure AD-appregistrering som ska användas som tjänstens huvudnamn följer du dessa instruktioner för att skapa dem.

## <a name="create-a-new-azure-active-directory-user"></a>Skapa en ny Azure Active Directory-användare

I [Azure-portalen](https://portal.azure.com)kontrollerar du att din klient visas under ditt användarnamn längst upp till höger i portalen:

![Skärmbild av portal med klienten överst till höger](./media/howto-create-tenant/tenant-callout.png) Om fel klient visas klickar du på ditt användarnamn längst upp till höger, klickar sedan på Växla **katalog**och väljer rätt klient i listan Alla **kataloger.**

Skapa en ny global Azure Active Directory-administratörsanvändare för att logga in på azure red hat OpenShift-klustret.

1. Gå till bladet [Användare-Alla](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) användare.
2. Klicka på **+Ny användare** för att öppna **användarfönstret.**
3. Ange ett **namn** för den här användaren.
4. Skapa ett **användarnamn** baserat på namnet på klienten som du skapade, med `.onmicrosoft.com` tillägg i slutet. Till exempel `yourUserName@yourTenantName.onmicrosoft.com`. Skriv ned det här användarnamnet. Du behöver den för att logga in på klustret.
5. Klicka på **Katalogrollen** för att öppna katalogrollsfönstret och välj **Global administratör** och klicka sedan på **Ok** längst ned i fönstret.
6. Klicka på Visa **lösenord** i **användarfönstret** och registrera det tillfälliga lösenordet. När du har loggat in första gången uppmanas du att återställa den.
7. Klicka på **Skapa** längst ned i fönstret för att skapa användaren.

## <a name="create-an-azure-ad-security-group"></a>Skapa en Azure AD-säkerhetsgrupp

För att bevilja klusteradministratörsåtkomst synkroniseras medlemskapen i en Azure AD-säkerhetsgrupp i OpenShift-gruppen "osa-customer-admins". Om inget klusteradministratörsbehör har angetts beviljas ingen åtkomst till klusteradministratören.

1. Öppna bladet [Azure Active Directory-grupper.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups)
2. Klicka på **+Ny grupp**.
3. Ange ett gruppnamn och en beskrivning.
4. Ange **grupptyp** till **Säkerhet**.
5. Ange **medlemstyp** till **Tilldelad**.

    Lägg till Azure AD-användaren som du skapade i det tidigare steget i den här säkerhetsgruppen.

6. Öppna fönstret **Välj medlemmar** genom att klicka på **Medlemmar.**
7. I medlemslistan väljer du den Azure AD-användare som du skapade ovan.
8. Längst ned i portalen klickar du på **Markera** och **sedan skapa** för att skapa säkerhetsgruppen.

    Skriv ned grupp-ID-värdet.

9. När gruppen skapas visas den i listan över alla grupper. Klicka på den nya gruppen.
10. På sidan som visas kopierar du **objekt-ID:et**. Vi refererar till `GROUPID` det här värdet som i självstudien [Skapa en Azure Red Hat OpenShift-kluster.](tutorial-create-cluster.md)

> [!IMPORTANT]
> Om du vill synkronisera den här gruppen med OpenShift-gruppen för osa-kundadministratörer skapar du klustret med hjälp av Azure CLI. Azure-portalen saknar för närvarande ett fält för att ange den här gruppen.

## <a name="create-an-azure-ad-app-registration"></a>Skapa en Azure AD-appregistrering

Du kan automatiskt skapa en Azure Active Directory -appregistreringsklient (Azure AD) som en del av att skapa klustret genom att utelämna `--aad-client-app-id` flaggan till `az openshift create` kommandot. Den här självstudien visar hur du skapar Azure AD-appregistreringen för fullständighet.

Om din organisation inte redan har en Azure Active Directory-appregistrering (Azure AD) som ska användas som tjänsthuvudnamn följer du dessa instruktioner för att skapa en.

1. Öppna [bladet Appregistreringar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) och klicka på **+Ny registrering**.
2. Ange ett namn på din ansökan registrering i fönstret **Registrera ett program.**
3. Kontrollera att konton **i den här organisationskatalogen endast** är markerade under **kontotyper som stöds.** Detta är det säkraste valet.
4. Vi kommer att lägga till en omdirigera URI senare när vi känner till URI i klustret. Klicka på knappen **Registrera** för att skapa registreringen av Azure AD-program.
5. På sidan som visas kopierar du **programmets (klient)-ID.** Vi refererar till `APPID` det här värdet som i självstudien [Skapa en Azure Red Hat OpenShift-kluster.](tutorial-create-cluster.md)

![Skärmbild av appobjektsida](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>Skapa en klienthemlighet

Generera en klienthemlighet för att autentisera din app till Azure Active Directory.

1. Klicka på **Certifikat & hemligheter**på sidan Hantera på sidan **Hantera.**
2. Klicka på **+Ny klienthemlighet**i fönstret **Certifikat & hemligheter.**  Fönstret **Lägg till en klienthemlighet** visas.
3. Ange en **beskrivning**.
4. Ange upphör att **gälla** till den tid du föredrar, till exempel **i 2 år**.
5. Klicka på **Lägg till** så visas nyckelvärdet i avsnittet **Klienthemligheter** på sidan.
6. Kopiera nyckelvärdet. Vi refererar till `SECRET` det här värdet som i självstudien [Skapa en Azure Red Hat OpenShift-kluster.](tutorial-create-cluster.md)

![Skärmbild av fönstret certifikat och hemligheter](./media/howto-create-tenant/create-key.png)

Mer information om Azure Application Objects finns [i Principobjekt för program och tjänst i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals).

Mer information om hur du skapar ett nytt Azure AD-program finns i [Registrera en app med slutpunkten Azure Active Directory v1.0](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

## <a name="add-api-permissions"></a>Lägga till API-behörigheter

[//]: # (Ändra inte till Microsoft Graph. Det fungerar inte med Microsoft Graph.)
1. Klicka på **API-behörigheter** i avsnittet **Hantera**
2. Klicka på **Lägg till behörighet** och välj Azure Active Directory **Graph** och sedan **delegerade behörigheter**.
> [!NOTE]
> Kontrollera att du har valt "Azure Active Directory Graph" och inte panelen "Microsoft Graph".

3. Expandera **Användare** i listan nedan och aktivera behörigheten **User.Read.** Om **User.Read** är aktiverat som standard, kontrollera att det är **Azure Active Directory Graph-behörigheten** **User.Read**.
4. Rulla uppåt och välj **Programbehörigheter**.
5. Expandera **katalogen** i listan nedan och aktivera **Directory.ReadAll**.
6. Klicka på **Lägg till behörigheter** om du vill acceptera ändringarna.
7. Api-behörighetspanelen ska nu visa både *User.Read* och *Directory.ReadAll*. Observera att varningen i **kolumnen Admin-medgivande krävs bredvid** *Directory.ReadAll*.
8. Om du är *Azure-prenumerationsadministratör*klickar du på **Bevilja administratörsmedgivande för *prenumerationsnamn* ** nedan. Om du inte är *Azure-prenumerationsadministratör*begär du samtycke från administratören.

![Skärmbild av API-behörighetspanelen. User.Read och Directory.ReadAll behörigheter tillagda, admin samtycke krävs för Directory.ReadAll](./media/howto-aad-app-configuration/permissions-required.png)

> [!IMPORTANT]
> Synkronisering av klusteradministratörsgruppen fungerar först när medgivande har beviljats. Du kommer att se en grön cirkel med en bock och meddelandet "Beviljad för *prenumerationsnamn"* i kolumnen *Administratörsmedgivande.*

Mer information om hur du hanterar administratörer och andra roller finns i [Lägga till eller ändra Azure-prenumerationsadministratörer](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator).

## <a name="resources"></a>Resurser

* [Principobjekt för program och tjänst i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)
* [Snabbstart: Registrera en app med Azure Active Directory v1.0-slutpunkten](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)

## <a name="next-steps"></a>Nästa steg

Om du har uppfyllt alla [Azure Red Hat OpenShift-förutsättningar](howto-setup-environment.md)är du redo att skapa ditt första kluster!

Prova självstudien:
> [!div class="nextstepaction"]
> [Skapa ett Azure Red Hat OpenShift-kluster](tutorial-create-cluster.md)
