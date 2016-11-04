---
title: 'Azure Active Directory B2C Preview: Skapa en Azure Active Directory B2C-klient | Microsoft Docs'
description: Ett avsnitt om hur du skapar en Azure Active Directory B2C-klient
services: active-directory-b2c
documentationcenter: ''
author: swkrish
manager: msmbaldwin
editor: bryanla

ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/16/2016
ms.author: swkrish

---
# Azure Active Directory B2C Preview: Skapa en Azure AD B2C-klient
Om du vill börja använda Microsoft Azure AD (Active Directory Azure) B2C följer du de tre stegen som beskrivs i den här artikeln.

[!INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Steg 1: Registrera dig för en Azure-prenumeration
Hoppa över det här steget om du redan har en Azure-prenumeration. Om inte registrerar du dig för en [Azure-prenumeration](../active-directory/sign-up-organization.md) och skaffar åtkomst till Azure AD B2C.

> [!NOTE]
> För närvarande kan du använda Azure AD B2C Preview kostnadsfritt men användningen är begränsad till 50 000 användare per klient. En Azure-prenumeration krävs för åtkomst till [den klassiska Azure-portalen](http://manage.windowsazure.com/).
> 
> 

## Steg 2: Skapa en Azure AD B2C-klient
Följ stegen nedan för att skapa en ny Azure AD B2C-klient. För närvarande går det inte att aktivera B2C-funktioner i dina befintliga kataloger om du har några.

1. Logga in på [den klassiska Azure-portalen](https://manage.windowsazure.com/) som administratör för prenumerationen. Det här är samma arbets- eller skolkonto eller samma Microsoft-konto som du använde när du registrerade dig för Azure.
2. Klicka på **Nytt** > **Apptjänster** > **Active Directory** > **Katalog** > **Skapa anpassade**.
   
    ![Skärmbild som visar hur du börjar skapa en klient](./media/active-directory-b2c-get-started/new-directory.png)
3. Välj **namn**, **domännamn** och **land/region** för din klient.
4. Markera alternativet som anger att **det här är en B2C-katalog**.
5. Klicka på bockmarkeringen för att slutföra åtgärden.
   
    ![Skärmbild av bockmarkeringen för att skapa en B2C-katalog](./media/active-directory-b2c-get-started/create-b2c-directory.png)
6. Nu skapas din klient och visas i Active Directory-tillägget. Du utses dessutom till global administratör för klienten. Du kan lägga till andra globala administratörer om det behövs.

## Steg 3: Gå till B2C-funktionsbladet på Azure-portalen
1. Gå till Active Directory-tillägget i navigeringsfältet till vänster.
2. Leta upp din klient under fliken **Katalog** och klicka på den.
3. Klicka på fliken **Konfigurera**.
4. Klicka på länken **Hantera B2C-inställningar** i avsnittet **B2C-administration**.
   
    ![Skärmbild av katalogkonfiguration för B2C](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)
5. Azure-portalen öppnas med B2C-funktionsbladet i ett nytt fönster eller på en ny flik i webbläsaren.
   
   > [!IMPORTANT]
   > Det kan ta 2–3 minuter innan din klient är tillgänglig på Azure-portalen. Gå igenom stegen igen efter en liten stund så brukar problemet försvinna. Om inte kontaktar du supporten.
   > 
   > 
6. Fäst det här bladet på startsidan för enkel åtkomst. (Verktyget Fäst är markerat i rött längst upp till höger i funktionsbladet.)
   
    ![Skärmbild av B2C-funktionsbladet](./media/active-directory-b2c-get-started/b2c-features-blade.png)
   
   > [!NOTE]
   > Du kan hantera användare och grupper, konfigurationen av lösenordsåterställning via självbetjäning och funktioner för företagsanpassning för din klient på [den klassiska Azure-portalen](https://manage.windowsazure.com/).
   > 
   > 

## Nästa steg
Lär dig hur du registrerar ett program med Azure AD B2C och hur du skapar ett snabbstartsprogram genom att läsa [Azure Active Directory B2C Preview: Registrera ditt program](active-directory-b2c-app-registration.md).

<!--HONumber=Jun16_HO2-->


