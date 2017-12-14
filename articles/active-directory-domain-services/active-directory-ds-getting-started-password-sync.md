---
title: "Azure Active Directory Domain Services: Aktivera lösenordssynkronisering | Microsoft Docs"
description: "Komma igång med Azure Active Directory Domain Services"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 5a32a0df-a3ca-4ebe-b980-91f58f8030fc
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/18/2017
ms.author: maheshu
ms.openlocfilehash: ea65659b1d35246bc3e7235b3faed8d2a5368010
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="enable-password-synchronization-to-azure-active-directory-domain-services"></a>Aktivera lösenordssynkronisering med Azure Active Directory Domain Services
I föregående uppgifter aktiverade du Azure Active Directory Domain Services för din Azure Active Directory-klient (Azure AD). Nästa uppgift är att aktivera synkronisering av autentiseringshasher som krävs för NT LAN Manager (NTLM)- och Kerberos-autentisering till Azure AD Domain Services. När du har konfigurerat synkroniseringen av autentiseringsuppgifter kan användarna logga in till den hanterade domänen med sina företagsuppgifter.

Stegen är olika för endast molnbaserade användarkonton och användarkonton som synkroniseras från din lokala katalog med Azure AD Connect. 

<br>
| **Typ av användarkonto** | **Steg att utföra** |
| --- |---|
| **Molnanvändarkonton som har skapats i Azure AD** |**&#x2713;** [Följ anvisningarna i den här artikeln](active-directory-ds-getting-started-password-sync.md#task-5-enable-password-synchronization-to-your-managed-domain-for-cloud-only-user-accounts) |
| **Användarkonton som synkroniserats från en lokal katalog** |**&#x2713;** [Synkronisera lösenord för användarkonton som synkroniserats från din lokala AD till din hanterade domän](active-directory-ds-getting-started-password-sync-synced-tenant.md) | 

<br>

> [!TIP]
> **Du kan behöva utföra båda steguppsättningarna.**
> Om Azure AD-klienten har en kombination av endast molnbaserade användare och användare från din lokala AD så måste du utföra båda dessa steguppsättningar.
>

## <a name="task-5-enable-password-synchronization-to-your-managed-domain-for-cloud-only-user-accounts"></a>Uppgift 5: aktivera lösenordssynkronisering till din hanterade domän för endast molnbaserade användarkonton
För att autentisera användare i den hanterade domänen behöver Azure Active Directory Domain Services autentiseringshasher i ett format som passar för NTLM- och Kerberos-autentisering. Om du inte aktiverar Azure Active Directory Domain Services för din klient så varken genererar eller lagrar Azure AD autentiseringshasher i det format som krävs för NTLM- eller Kerberos-autentisering. Av självklara säkerhetsskäl lagrar Azure AD inte heller lösenordsuppgifter i klartext. Detta betyder att Azure AD inte kan skapa dessa NTLM- eller Kerberos-autentiseringshasher automatiskt utifrån användarnas befintliga autentiseringsuppgifter.

> [!NOTE]
> **Om organisationen endast har molnbaserade användarkonton måste användare som behöver använda Azure Active Directory Domain Services ändra sina lösenord.** Ett endast molnbaserat användarkonto är ett konto som skapats i Azure AD-katalogen med antingen Azure Portal eller Azure AD PowerShell-cmdletar. Dessa användarkonton är inte synkroniserade från en lokal katalog.
>
>

Den här lösenordsändringsprocessen gör att autentiseringshasherna som krävs av Azure Active Directory Domain Services för Kerberos- och NTLM-autentisering genereras i Azure AD. Du kan antingen ange att lösenorden för alla användare i klienten som behöver använda Azure Active Directory Domain Services ska förfalla eller uppmana användarna att ändra sina lösenord.

### <a name="enable-ntlm-and-kerberos-credential-hash-generation-for-a-cloud-only-user-account"></a>Aktivera genereringen av hashvärden för NTLM- och Kerberos-autentisering för ett endast molnbaserat användarkonto
Här är de instruktioner som du behöver ge användarna så att de kan ändra sina lösenord:

1. Gå till [Azure AD-åtkomstpanelen](http://myapps.microsoft.com) för din organisation.

    ![Starta Azure AD-åtkomstpanelen](./media/active-directory-domain-services-getting-started/access-panel.png)

2. Klicka på ditt namn i övre högra hörnet och välj **Profil** i menyn.

    ![Välj profil](./media/active-directory-domain-services-getting-started/select-profile.png)

3. På sidan **Profil** klickar du på **Ändra lösenord**.

    ![Klicka på "Ändra lösenord"](./media/active-directory-domain-services-getting-started/user-change-password.png)

   > [!TIP]
   > Om alternativet **Ändra lösenord** inte visas i åtkomstpanelsfönstret kontrollerar du att din organisation har konfigurerat [lösenordshantering i Azure AD](../active-directory/active-directory-passwords-getting-started.md).
   >
   >
4. På sidan för att **ändra lösenord** skriver du det befintliga (gamla) lösenordet och skriver sedan ett nytt lösenord och bekräftar det.

    ![Skapa ett virtuellt nätverk för Azure AD Domain Services.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

5. Klicka på **Skicka**.

Ett par minuter efter att du har ändrat ditt lösenord kan det nya lösenordet användas i Azure Active Directory Domain Services. Efter cirka 20 minuter kan du logga in på datorer som är anslutna till den hanterade domänen med det nya lösenordet.

## <a name="related-content"></a>Relaterat innehåll
* [Uppdatera ditt eget lösenord](../active-directory/active-directory-passwords-update-your-own-password.md)
* [Komma igång med lösenordshantering i Azure AD](../active-directory/active-directory-passwords-getting-started.md)
* [Aktivera lösenordssynkronisering till Azure Active Directory Domain Services för en synkroniserad Azure AD-klient](active-directory-ds-getting-started-password-sync-synced-tenant.md)
* [Administrera en Azure Active Directory Domain Services-hanterad domän](active-directory-ds-admin-guide-administer-domain.md)
* [Ansluta en virtuell Windows-dator till en Azure Active Directory Domain Services-hanterad domän](active-directory-ds-admin-guide-join-windows-vm.md)
* [Ansluta en virtuell Linux-dator med Red Hat Enterprise till en Azure Active Directory Domain Services-hanterad domän](active-directory-ds-admin-guide-join-rhel-linux-vm.md)
