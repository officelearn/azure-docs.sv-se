---
title: Azure Active Directory-integrering för Azure Red Hat OpenShift | Microsoft Docs
description: Lär dig hur du skapar en Azure AD-säkerhetsgruppen och användare för att testa appar i Microsoft Azure Red Hat OpenShift-klustret.
author: tylermsft
ms.author: twhitney
ms.service: openshift
manager: jeconnoc
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/13/2019
ms.openlocfilehash: f6b87748c33c1afd047ae25dfb7df3670a73e7c8
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65779686"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Azure Active Directory-integrering för Azure Red Hat OpenShift

Om du inte redan har skapat en Azure Active Directory (Azure AD)-klient, följer du anvisningarna i [skapa en Azure AD-klient för Azure Red Hat OpenShift](howto-create-tenant.md) innan du fortsätter med instruktionerna.

Microsoft Azure Red Hat OpenShift måste ha behörighet att utföra åtgärder åt ditt kluster. Om din organisation inte redan har en Azure AD-användare, Azure AD-säkerhetsgrupp eller en Azure AD-appregistrering ska användas som tjänstens huvudnamn, följer du dessa instruktioner för att skapa dem.

## <a name="create-a-new-azure-active-directory-user"></a>Skapa en ny Azure Active Directory-användare

I den [Azure-portalen](https://portal.azure.com), se till att din klient visas under ditt användarnamn längst upp höger på portalen:

![Skärmbild av portalen med klient som anges i längst upp till höger](./media/howto-create-tenant/tenant-callout.png) om fel klient visas, klicka på ditt användarnamn längst upp till höger och sedan på **växla katalog**, och välj rätt klient från den **alla Kataloger** lista.

Skapa en ny Azure Active Directory global administratör logga in på ditt Azure Red Hat OpenShift-kluster.

1. Gå till den [användare alla användare](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) bladet.
2. Klicka på **+ ny användare** att öppna den **användaren** fönstret.
3. Ange en **namn** för den här användaren.
4. Skapa en **användarnamn** baserat på namnet på den klient du skapade med `.onmicrosoft.com` tillägg i slutet. Till exempel `yourUserName@yourTenantName.onmicrosoft.com`. Anteckna det här användarnamnet. Du behöver den för att logga in på ditt kluster.
5. Klicka på **katalogroll** att öppna fönstret directory rollen och välja **Global administratör** och klicka sedan på **Ok** längst ned i fönstret.
6. I den **användaren** fönstret klickar du på **visa lösenord** och registrera det tillfälliga lösenordet. När du har loggat in för första gången uppmanas du att återställa den.
7. Längst ned i fönstret klickar du på **skapa** att skapa användaren.

## <a name="create-an-azure-ad-security-group"></a>Skapa en Azure AD-säkerhetsgrupp

Om du vill bevilja administratörsåtkomst för klustret, synkroniseras medlemskap i en Azure AD-säkerhetsgrupp i OpenShift grupp ”osa-kund-administratörer”. Om inte anges kommer att beviljas ingen administratörsåtkomst för klustret.

1. Öppna den [Azure Active Directory-grupper](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups) bladet.
2. Klicka på **+ ny grupp**
3. Ange ett namn och beskrivning.
4. Ange **grupptyp** till **Security**.
5. Ange **Medlemskapstyp** till **tilldelad**.

    Lägg till Azure AD-användare som du skapade i det tidigare steget i den här säkerhetsgruppen.

6. Klicka på **medlemmar** att öppna den **Välj medlemmar** fönstret.
7. Välj den Azure AD-användare som du skapade ovan i listan över medlemmar.
8. Längst ned i portalen klickar du på **Välj** och sedan **skapa** att skapa gruppen.

    Anteckna värdet för grupp-ID

9. När gruppen har skapats visas den i listan över alla grupper. Klicka på den nya gruppen.
10. På sidan som visas, kopiera den **objekt-ID**. Vi kommer att referera till det här värdet som `GROUPID` i den [skapa ett kluster i Azure Red Hat OpenShift](tutorial-create-cluster.md) självstudien.

## <a name="create-an-azure-ad-app-registration"></a>Skapa en Azure AD-appregistrering

Du kan automatiskt skapa en Azure Active Directory (Azure AD) registrering av klienten som en del av klustret skapas genom att utelämna den `--aad-client-app-id` flaggan till den `az openshift create` kommando. Den här självstudien visar hur du skapar Azure AD app-registrering för fullständighetens skull.

Om din organisation inte redan har en Azure Active Directory (Azure AD)-appregistrering ska användas som ett huvudnamn för tjänsten, följer du dessa instruktioner för att skapa en.

1. Öppna den [registreringar appbladet](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) och klicka på **+ ny registrering**.
2. I den **registrera ett program** rutan Ange ett namn för din programregistrering.
3. Se till att under **stöds kontotyper** som **konton i den här organisationens katalogen** har valts. Detta är det säkraste alternativet.
4. Vi lägger till en omdirigerings-URI senare när vi vet URI för klustret. Klicka på den **registrera** för att skapa Azure AD-programregistrering.
5. På sidan som visas, kopiera den **(klient)-ID: T**. Vi kommer att referera till det här värdet som `APPID` i den [skapa ett kluster i Azure Red Hat OpenShift](tutorial-create-cluster.md) självstudien.

![Skärmbild av appsidan för objekt](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>Skapa en klienthemlighet

Generera en klienthemlighet för att autentisera din app till Azure Active Directory.

1. I den **hantera** avsnittet av sidan för registreringar, klickar du på **certifikat och hemligheter**.
2. På den **certifikat och hemligheter** fönstret klickar du på **+ nya klienthemligheten**.  Den **lägga till en klienthemlighet** visas fönstret.
3. Ange en **beskrivning**.
4. Ange **förfaller** hur länge du föredrar, till exempel **i 2 år**.
5. Klicka på **Lägg till** och nyckelvärdet visas i den **klienten hemligheter** på sidan.
6. Kopiera nyckelvärdet. Vi kommer att referera till det här värdet som `SECRET` i den [skapa ett kluster i Azure Red Hat OpenShift](tutorial-create-cluster.md) självstudien.
 
![Skärmbild av fönstret certifikat och hemligheter](./media/howto-create-tenant/create-key.png)
 
Mer information om Azure programobjekt finns [program och tjänstobjekt i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals).

Mer information om hur du skapar en ny Azure AD-program finns i [registrera en app med Azure Active Directory v1.0 slutpunkten](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

## <a name="resources"></a>Resurser

* [Program och tjänstobjekt i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)  
* [Snabbstart: Registrera en app med Azure Active Directory v1.0 slutpunkt](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)  

## <a name="next-steps"></a>Nästa steg

Om du har uppfyllt alla de [krav för Azure Red Hat OpenShift](howto-setup-environment.md), är du redo att skapa ditt första kluster!

Prova guiden:
> [!div class="nextstepaction"]
> [Skapa ett Azure Red Hat OpenShift-kluster](tutorial-create-cluster.md)