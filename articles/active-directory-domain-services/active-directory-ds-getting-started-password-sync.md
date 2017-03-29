---
title: "Azure AD Domain Services: Aktivera lösenordssynkronisering | Microsoft Docs"
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
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: f994edd71c6d8ca8a2c3f5de370115747f87da57
ms.lasthandoff: 03/28/2017


---
# <a name="enable-password-synchronization-to-azure-ad-domain-services"></a>Aktivera lösenordssynkronisering till Azure AD Domain Services
I de föregående uppgifterna aktiverade du Azure AD Domain Services för din Azure AD-klient. Nästa uppgift är att aktivera autentiseringshasher som krävs för att NTLM- och Kerberos-autentisering ska kunna synkronisera till Azure AD Domain Services. När synkroniseringen av autentiseringsuppgifter har konfigurerats kan användarna logga in till den hanterade domänen med sina företagsuppgifter.

Stegen skiljer sig beroende på om din organisation har en helt molnbaserad Azure AD-klient eller om du synkroniserar med din lokala katalog med hjälp av Azure AD Connect.

<br>

> [!div class="op_single_selector"]
> * [Endast molnbaserad Azure AD-klient](active-directory-ds-getting-started-password-sync.md)
> * [Synkroniserad Azure AD-klient](active-directory-ds-getting-started-password-sync-synced-tenant.md)
>
>

<br>

## <a name="task-5-enable-password-synchronization-to-aad-domain-services-for-a-cloud-only-azure-ad-tenant"></a>Uppgift 5: Aktivera lösenordssynkronisering till AAD Domain Services för en molnbaserad Azure AD-klient
Azure AD Domain Services behöver autentiseringshasher i ett format som passar för NTLM- och Kerberos-autentisering för att autentisera användare i den hanterade domänen. Om du aktiverar AAD Domain Services för din klient så varken genererar eller lagrar Azure AD autentiseringshasher i det format som krävs för NTLM- eller Kerberos-autentisering. Av självklara säkerhetsskäl lagrar Azure AD inte heller autentiseringsuppgifter i klartext. Detta betyder att Azure AD inte kan skapa dessa NTLM- eller Kerberos-autentiseringshasher utifrån användarnas befintliga autentiseringsuppgifter.

> [!NOTE]
> Om din organisation har en helt molnbaserad Azure AD-klient måste användare som behöver använda Azure AD Domain Services ändra sina lösenord.
>
>

Den här lösenordsändringsprocessen gör att autentiseringshasherna som krävs av Azure AD Domain Services för Kerberos- och NTLM-autentisering genereras i Azure AD. Du kan antingen ange att lösenorden för alla användare i klienten som behöver använda Azure AD Domain Services ska förfalla eller uppmana dessa användare att ändra sina lösenord.

### <a name="enable-ntlm-and-kerberos-credential-hash-generation-for-a-cloud-only-azure-ad-tenant"></a>Aktivera genereringen av hashvärden för NTLM- och Kerberos-autentisering för en helt molnbaserad Azure AD-klient
Här är de instruktioner som du måste ge slutanvändarna så att de kan ändra sina lösenord:

1. Gå till sidan för Azure AD-åtkomstpanelen för din organisation på [http://myapps.microsoft.com](http://myapps.microsoft.com).
2. Välj fliken **Profil** på den här sidan.
3. Klicka på panelen **Ändra lösenord** på den här sidan.

    ![Skapa ett virtuellt nätverk för Azure AD Domain Services.](./media/active-directory-domain-services-getting-started/user-change-password.png)

   > [!NOTE]
   > Om du inte ser alternativet **Ändra lösenord** på åtkomstpanelsidan kontrollerar du att din organisation har konfigurerat [lösenordshantering i Azure AD](../active-directory/active-directory-passwords-getting-started.md).
   >
   >
4. På sidan **Ändra lösenord** skriver du det befintliga (gamla) lösenordet och skriver sedan ett nytt lösenord och bekräftar det. Klicka på **Skicka**.

    ![Skapa ett virtuellt nätverk för Azure AD Domain Services.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

När du har ändrat ditt lösenord kan det nya lösenordet användas i Azure AD Domain Services strax därefter. Efter några minuter (oftast cirka 20 minuter) kan du logga in på datorer som är anslutna till den hanterade domänen med det nya lösenordet.

<br>

## <a name="related-content"></a>Relaterat innehåll
* [Uppdatera ditt eget lösenord](../active-directory/active-directory-passwords-update-your-own-password.md#reset-your-password).)
* [Komma igång med lösenordshantering i Azure AD](../active-directory/active-directory-passwords-getting-started.md).
* [Aktivera lösenordssynkronisering till AAD Domain Services för en synkroniserad Azure AD-klient](active-directory-ds-getting-started-password-sync-synced-tenant.md)
* [Administrera en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-administer-domain.md)
* [Ansluta en virtuell Windows-dator till en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-join-windows-vm.md)
* [Ansluta en virtuell Linux-dator med Red Hat Enterprise till en Azure AD Domain Services-hanterad domän](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

