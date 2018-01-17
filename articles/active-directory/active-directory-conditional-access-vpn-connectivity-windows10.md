---
title: "Azure Active Directory villkorlig åtkomst för VPN-anslutning (förhandsversion) | Microsoft Docs"
description: "Lär dig hur fungerar Azure Active Directory villkorlig åtkomst för VPN-anslutning. "
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 51a1ee61-3ffe-4f65-b8de-ff21903e1e74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 511df58befe9aed4aa65fc6944cae3a8e2c74c2d
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2018
---
# <a name="azure-active-directory-conditional-access-for-vpn-connectivity-preview"></a>Azure Active Directory villkorlig åtkomst för VPN-anslutning (förhandsgranskning)

Med [villkorlig åtkomst i Azure Active Directory (AD Azure)](active-directory-conditional-access-azure-portal.md), du kan finjustera hur behöriga användare åtkomst till resurser. Med Azure AD villkorlig åtkomst för virtuellt privat nätverk (VPN)-anslutning kan skydda du din VPN-anslutningar.


För att konfigurera villkorlig åtkomst för VPN-anslutning, måste du utföra följande steg: 

1.  Konfigurera VPN-servern.
2.  Konfigurera VPN-klienten.
3.  Konfigurera princip för villkorlig åtkomst.


## <a name="before-you-begin"></a>Innan du börjar

Det här avsnittet förutsätter att du är bekant med följande avsnitt:

- [Villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-azure-portal.md)
- [VPN- och villkorlig åtkomst](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access)

För att få insikter om hur Microsoft implementerar den här funktionen, se [förbättra fjärråtkomst i Windows 10 med en automatisk VPN-profil](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile).   


## <a name="prerequisites"></a>Förutsättningar

Du måste ha en VPN-server som konfigurerats för att konfigurera Azure Active Directory villkorlig åtkomst för VPN-anslutning. 



## <a name="step-1-configure-your-vpn-server"></a>Steg 1: Konfigurera VPN-servern 

Det här steget konfigurerar rotcertifikat för VPN-autentisering med Azure AD. För att konfigurera villkorlig åtkomst för VPN-anslutning, måste du:

1. Skapa en VPN-certifikat i Azure-portalen.
2. Hämta VPN-certifikatet.
2. Distribuera certifikatet till VPN-servern.

Azure AD använder VPN-certifikat för att signera certifikat som utfärdats till Windows 10-klienter vid autentisering till Azure AD för VPN-anslutning. Den token som begär klienten för Windows 10 är ett certifikat som visar sedan till programmet, som i det här fallet är den VPN-servern.

![Hämta certifikat för villkorlig åtkomst](./media/active-directory-conditional-access-vpn-connectivity-windows10/06.png)

Du kan skapa två certifikat för att hantera övergången när ett certifikat upphör snart att gälla i Azure-portalen. När du skapar ett certifikat kan välja du om den är primär certifikatet som används vid autentisering för att signera certifikat för anslutningen.

Att skapa en VPN-certifikat:

1. Logga in på ditt [Azure-portalen](https://portal.azure.com) som global administratör.

2. Klicka på den vänstra menyn **Azure Active Directory**. 

    ![Välj Azure Active Directory](./media/active-directory-conditional-access-vpn-connectivity-windows10/01.png)

3. På den **Azure Active Directory** sidan den **hantera** klickar du på **villkorlig åtkomst**.

    ![Välj villkorlig åtkomst](./media/active-directory-conditional-access-azure-portal-get-started/02.png)

4. På den **villkorlig åtkomst** sidan den **hantera** klickar du på **VPN-anslutning (förhandsgranskning)**.

    ![Välj VPN-anslutning](./media/active-directory-conditional-access-vpn-connectivity-windows10/03.png)

5. På den **VPN-anslutning** klickar du på **nytt certifikat**.

    ![Välj nya certifikat](./media/active-directory-conditional-access-vpn-connectivity-windows10/04.png)

6. På den **ny** utför följande steg:

    ![Välj varaktighet och primär](./media/active-directory-conditional-access-vpn-connectivity-windows10/05.png)

    a. För **Markera varaktighet**väljer **1 års**.

    b. För **primära**väljer **Ja**.

    c. Klicka på **Skapa**.

7. På sidan VPN-anslutningen **hämta certifikat**.


Nu är du klar att distribuera nyligen skapade certifikatet till VPN-servern. På VPN-servern, lägger du till hämtade certifikatet som en *betrodd rotcertifikatutfärdare för VPN-autentisering*.

För Windows-RRAS-baserade distributioner på NPS-servern: Lägg till rotcertifikat till den *Enterprise NTauth* lagra genom att köra följande kommandon:

1. `certutil -dspublish <CACERT> RootCA`
2. `certutil -dspublish <CACERT> NtAuthCA`



## <a name="step-2-configure-your-vpn-client"></a>Steg 2: Konfigurera VPN-klienten 

I det här steget konfigurerar du din profil för VPN-klienten anslutning som beskrivs i [VPN och villkorlig åtkomst](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access).


## <a name="step-3-configure-your-conditional-access-policy"></a>Steg 3: Konfigurera principen för villkorlig åtkomst

Det här avsnittet innehåller anvisningar för att konfigurera din princip för villkorlig åtkomst för VPN-anslutning.


1. På den **villkorlig åtkomst** i verktygsfältet högst upp på sidan klickar du på **Lägg till**.

    ![Välj Lägg till på sidan för villkorlig åtkomst](./media/active-directory-conditional-access-vpn-connectivity-windows10/07.png)

2. På den **ny** sidan den **namn** Skriv ett namn för principen. Skriv till exempel **VPN-princip**.

    ![Lägg till namn för principen på sidan för villkorlig åtkomst](./media/active-directory-conditional-access-vpn-connectivity-windows10/08.png)

5. I den **tilldelning** klickar du på **användare och grupper**.

    ![Välj användare och grupper](./media/active-directory-conditional-access-vpn-connectivity-windows10/09.png)

6. På den **användare och grupper** utför följande steg:

    ![Välj testanvändare](./media/active-directory-conditional-access-vpn-connectivity-windows10/10.png)

    a. Klicka på **Välj användare och grupper**.

    b. Klicka på **Välj**.

    c. På den **Välj** sidan, Välj din testanvändare och klicka sedan på **Välj**.

    d. På den **användare och grupper** klickar du på **klar**.

7. På den **ny** utför följande steg:

    ![Välj molnappar](./media/active-directory-conditional-access-vpn-connectivity-windows10/11.png)

    a. I den **tilldelningar** klickar du på **Molnappar**.

    b. På den **Molnappar** klickar du på **Välj appar**, och klicka sedan på **Välj**.

    c. På den **Välj** sidan den **program** skriver **vpn**.

    d. Välj **VPN-servern**.

    e. Klicka på **Välj**.


13. På den **ny** sidan för att öppna den **bevilja** sidan den **kontroller** klickar du på **bevilja**.

    ![SELECT-tilldelning](./media/active-directory-conditional-access-azure-portal-get-started/13.png)

14. På den **bevilja** utför följande steg:

    ![Välj kräver multifaktorautentisering](./media/active-directory-conditional-access-azure-portal-get-started/14.png)

    a. Välj **kräver Multi-Factor authentication**.

    b. Klicka på **Välj**.

15. På den **ny** sidan under **aktiverar principen**, klickar du på **på**.

    ![Aktivera princip](./media/active-directory-conditional-access-azure-portal-get-started/15.png)

16. På den **ny** klickar du på **skapa**.



## <a name="next-steps"></a>Nästa steg

För att få insikter om hur Microsoft implementerar den här funktionen, se [förbättra fjärråtkomst i Windows 10 med en automatisk VPN-profil](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile).    

