---
title: "Azure Active Directory Domain Services: Aktivera lösenordssynkronisering | Microsoft Docs"
description: "Komma igång med Azure Active Directory Domain Services"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 5a32a0df-a3ca-4ebe-b980-91f58f8030fc
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 6ed35ca1b83f5f7221824d99817800c1e42e68c1
ms.lasthandoff: 04/12/2017


---
# <a name="enable-password-synchronization-with-azure-active-directory-domain-services"></a>Aktivera lösenordssynkronisering med Azure Active Directory Domain Services
I föregående uppgifter aktiverade du Azure Active Directory Domain Services (AD DS) för din Azure Active Directory-klient (Azure AD). Nästa uppgift är att aktivera autentiseringshasher, som krävs för att NT LAN Manager- (NTLM) och Kerberos-autentisering ska kunna synkronisera med Azure Active Directory Domain Services. När du har konfigurerat synkroniseringen av autentiseringsuppgifter kan användarna logga in till den hanterade domänen med sina företagsuppgifter.

Procedurerna varierar beroende på om din organisation har en helt molnbaserad Azure AD-klient eller är om den är inställd för att synkronisera med din lokala katalog med hjälp av Azure AD Connect.

> [!div class="op_single_selector"]
> * [Endast molnbaserad Azure AD-klient](active-directory-ds-getting-started-password-sync.md)
> * [Synkroniserad Azure AD-klient](active-directory-ds-getting-started-password-sync-synced-tenant.md)
>
>

## <a name="task-5-enable-password-synchronization-with-azure-active-directory-domain-services-for-a-cloud-only-azure-ad-tenant"></a>Uppgift 5: Aktivera lösenordssynkronisering med Azure Active Directory Domain Services för en Azure AD-klient som är helt molnbaserad
För att autentisera användare i den hanterade domänen behöver Azure Active Directory Domain Services autentiseringshasher i ett format som passar för NTLM- och Kerberos-autentisering. Om du inte aktiverar Azure Active Directory Domain Services för din klient, så varken genererar eller lagrar Azure AD autentiseringshasher i det format som krävs för NTLM- eller Kerberos-autentisering. Av självklara säkerhetsskäl lagrar Azure AD inte heller autentiseringsuppgifter i klartext. Detta betyder att Azure AD inte kan skapa dessa NTLM- eller Kerberos-autentiseringshasher utifrån användarnas befintliga autentiseringsuppgifter.

> [!NOTE]
> Om din organisation har en helt molnbaserad Azure AD-klient måste användare som behöver använda Azure Active Directory Domain Services ändra sina lösenord.
>
>

Den här lösenordsändringsprocessen gör att autentiseringshasherna som krävs av Azure Active Directory Domain Services för Kerberos- och NTLM-autentisering genereras i Azure AD. Du kan antingen ange att lösenorden för alla användare i klienten som behöver använda Azure Active Directory Domain Services ska förfalla eller uppmana användarna att ändra sina lösenord.

### <a name="enable-ntlm-and-kerberos-credential-hash-generation-for-a-cloud-only-azure-ad-tenant"></a>Aktivera genereringen av hashvärden för NTLM- och Kerberos-autentisering för en helt molnbaserad Azure AD-klient
Här är de instruktioner som du behöver ge användarna så att de kan ändra sina lösenord:

1. Gå till [Azure AD-åtkomstpanelen](http://myapps.microsoft.com) för din organisation.
2. I åtkomstpanelsfönstret väljer du fliken **profil**.
3. Klicka på ikonen **Ändra lösenord**.

    ![Azure AD-åtkomstpanelens ikon ”Ändra lösenord”](./media/active-directory-domain-services-getting-started/user-change-password.png)

   > [!NOTE]
   > Om alternativet **Ändra lösenord** inte visas i åtkomstpanelsfönstret kontrollerar du att din organisation har konfigurerat [lösenordshantering i Azure AD](../active-directory/active-directory-passwords-getting-started.md).
   >
   >
4. På sidan för att **ändra lösenord** skriver du det befintliga (gamla) lösenordet och skriver sedan ett nytt lösenord och bekräftar det. 

    ![Skapa ett virtuellt nätverk för Azure AD Domain Services.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

5. Klicka på **Skicka**.

Ett par minuter efter att du har ändrat ditt lösenord kan det nya lösenordet användas i Azure Active Directory Domain Services. Efter ytterligare ett par minuter (oftast cirka 20 minuter) kan du logga in på datorer som är anslutna till den hanterade domänen med det nya lösenordet.

## <a name="next-steps"></a>Nästa steg
* [Uppdatera ditt eget lösenord](../active-directory/active-directory-passwords-update-your-own-password.md#reset-your-password)
* [Komma igång med lösenordshantering i Azure AD](../active-directory/active-directory-passwords-getting-started.md)
* [Aktivera lösenordssynkronisering till Azure Active Directory Domain Services för en synkroniserad Azure AD-klient](active-directory-ds-getting-started-password-sync-synced-tenant.md)
* [Administrera en Azure Active Directory Domain Services-hanterad domän](active-directory-ds-admin-guide-administer-domain.md)
* [Ansluta en virtuell Windows-dator till en Azure Active Directory Domain Services-hanterad domän](active-directory-ds-admin-guide-join-windows-vm.md)
* [Ansluta en virtuell Linux-dator med Red Hat Enterprise till en Azure Active Directory Domain Services-hanterad domän](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

