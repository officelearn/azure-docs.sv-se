---
title: "Komma igång med Azure Multi-Factor Authentication-provider | Microsoft Docs"
description: "Lär dig hur du skapar en Azure Multi-Factor Authentication-provider."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: a7dd5030-7d40-4654-8fbd-88e53ddc1ef5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/14/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: b3dadee1ea8d1ff4a0164a90eb617f9d276b6670


---
# <a name="getting-started-with-an-azure-multi-factor-auth-provider"></a>Komma igång med en Azure Multi-Factor Authentication-provider
Tvåstegsverifiering är tillgängligt som standard för globala administratörer med Azure Active Directory och Office 365-användare. Men om du vill dra nytta av [avancerade funktioner](multi-factor-authentication-whats-next.md) ska du köpa den fullständiga versionen av Azure Multi-Factor Authentication (MFA).

> [!NOTE]
> En Azure Multi-Factor Authentication-provider används för att dra nytta av funktionerna i den fullständiga versionen av Azure MFA. Den är avsedd för användare som **inte har licenser genom Azure MFA, Azure AD Premium eller EMS**.  Azure MFA Azure AD Premium och EMS innehåller den fullständiga versionen av Azure MFA som standard.  Om du har licenser behöver du inte någon Azure Multi-Factor Authentication-provider.
> 
> 

En Azure Multi-Factor Auth-provider krävs för att hämta SDK:n.

> [!IMPORTANT]
> För att hämta SDK:n skapar du en Azure Multi-Factor Auth-provider, även om du har Azure MFA-, AAD Premium- eller EMS-licenser.  Om du skapar en Azure Multi-Factor Authentication-provider för detta ändamål och redan har licenser, se till att skapa providern med modellen **Per aktiverad användare**. Koppla sedan providern till katalogen som innehåller Azure MFA, Azure AD Premium eller EMS-licenser.  Detta ser till att du bara faktureras om har fler unika användare som använder SDK:n än antalet licenser som du äger.
> 
> 

## <a name="to-create-a-multi-factor-auth-provider"></a>Så här skapar du en Multi-Factor Authentication-provider
Följ stegen nedan om du vill skapa en Azure Multi-FactorAuthentication-provider.

1. Logga in på [den klassiska Azure-portalen](https://manage.windowsazure.com) som administratör.
2. Välj **Active Directory** till vänster.
3. Längst upp på sidan Active Directory väljer du **Multi-Factor Authentication-providers**.
   ![Skapa en MFA-provider](./media/multi-factor-authentication-get-started-auth-provider/authprovider1.png)
4. Klicka på **Ny** längst ned på sidan.
   ![Skapa en MFA-provider](./media/multi-factor-authentication-get-started-auth-provider/authprovider2.png)
5. Under Apptjänster väljer du **Multi-Factor Auth-provider**
   ![Skapa en MFA-provider](./media/multi-factor-authentication-get-started-auth-provider/authprovider3.png)
6. Välj **Snabbregistrering**.
   ![Skapa en MFA-provider](./media/multi-factor-authentication-get-started-auth-provider/authprovider4.png)
7. Fyll i följande fält och välj **Skapa**.
   1. **Namn** – Namnet på Multi-Factor Auth-providern.
   2. **Användningsmodell** – Om du vill aktivera enskilda användare eller betala per verifiering. Välj ett av två alternativ:
      * Per autentisering – köpmodell som debiterar per autentisering. Används vanligtvis för scenarier som använder Azure Multi-Factor Authentication i ett program som riktar sig till konsumenter.
      * Per aktiverad användare – köpmodell som debiterar per aktiverad användare. Används vanligtvis för medarbetaråtkomst till program som Office 365. Välj det här alternativet om du har några användare som redan har licens för Azure MFA.
   3. **Katalog** – Azure Active Directory-klienten som Multi-Factor Authentication-providern är kopplad till. Tänk på följande:
      * Du behöver ingen Azure AD-katalog för att skapa en Multi-Factor Auth-provider. Lämna rutan tom om du endast planerar att använda Azure Multi-Factor Authentication Server eller SDK.
      * Multi-Factor Auth-providern måste vara associerad med en Azure AD-katalog för att du ska kunna dra nytta av de avancerade funktionerna.
      * Azure AD Connect, AAD Sync eller DirSync är endast ett krav om du synkroniserar din lokala Active Directory-miljö med en Azure AD-katalog.  Om du bara använder en Azure AD-katalog som inte är synkroniserad, krävs inte detta.
        ![Skapa en MFA-provider](./media/multi-factor-authentication-get-started-auth-provider/authprovider5.png)
8. När du klickar på Skapa skapas Multi-Factor Authentication-providern och ett meddelande visas som anger att **Multi-Factor Authentication-providern** har skapats. Klicka på **OK**.
   ![Skapa en MFA-provider](./media/multi-factor-authentication-get-started-auth-provider/authprovider6.png)




<!--HONumber=Dec16_HO1-->


