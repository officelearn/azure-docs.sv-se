---
title: "Så här länkar du en Azure-prenumeration till Azure AD B2C | Microsoft Docs"
description: "Steg för steg-guide för att aktivera fakturering för Azure AD B2C-klient till en Azure-prenumeration."
services: active-directory-b2c
documentationcenter: dev-center-name
author: rojasja
manager: mbaldwin
ms.service: active-directory-b2c
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2016
ms.author: joroja
ms.openlocfilehash: 5b9955b2af7f20a79981315fa33a0eb5380a5465
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="linking-an-azure-subscription-to-an-azure-b2c-tenant-to-pay-for-usage-charges"></a>Länka en Azure-prenumeration till en Azure B2C-klient för att betala för avgifter för användning

Pågående kostnader för Azure Active Directory B2C (eller Azure AD B2C) debiteras till en Azure-prenumeration. Det är nödvändigt för klientadministratör explicit länka Azure AD B2C-klient till en Azure-prenumeration när du har skapat den B2C-klienten.  Den här länken uppnås genom att skapa en Azure AD ”B2C-klient” resurs i målet Azure-prenumeration. Många B2C-klienter kan vara länkad till en enda Azure-prenumeration tillsammans med andra Azure-resurser (till exempel virtuella datorer, lagring av Data, LogicApps)


> [!IMPORTANT]
> Den senaste informationen om användning fakturering och priser för B2C är på följande sida: [Azure AD B2C-priser](
https://azure.microsoft.com/pricing/details/active-directory-b2c/)

## <a name="step-1---create-an-azure-ad-b2c-tenant"></a>Steg 1 – skapa en Azure AD B2C-klient
Skapa en B2C-klient måste slutföras först. Hoppa över det här steget om du redan har skapat ditt mål B2C-klient. [Kom igång med Azure AD B2C](active-directory-b2c-get-started.md)

## <a name="step-2---open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>Steg 2 – öppna Azure-portalen i Azure AD-klient som visar din Azure-prenumeration
Navigera till [Azure-portalen](https://portal.azure.com). Växla till Azure AD-klient som visar Azure-prenumeration om du vill använda. Azure AD-klient skiljer sig från B2C-klient. Klicka på namnet på kontot på den övre högra hörnet på instrumentpanelen för att välja Azure AD-klient i Azure-portalen. En Azure-prenumeration krävs för att fortsätta. [Hämta en Azure-prenumeration](https://account.windowsazure.com/signup?showCatalog=True)

![Växla till din Azure AD-klient](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="step-3---create-a-b2c-tenant-resource-in-azure-marketplace"></a>Steg 3 – skapa en B2C-klient-resurs i Azure Marketplace
Öppna Marketplace genom att klicka på ikonen Marketplace eller markera gröna ”+” i det övre vänstra hörnet på instrumentpanelen.  Sök efter och välj Azure Active Directory B2C. Välj Skapa.

![Välj Marketplace](./media/active-directory-b2c-how-to-enable-billing/marketplace.png)

![Sök AD B2C](./media/active-directory-b2c-how-to-enable-billing/searchb2c.png)

Azure AD B2C-resursen skapa dialogrutan omfattar följande parametrar:

1. Azure AD B2C-klient – Välj en Azure AD B2C-klient i listrutan.  Visa endast berättigade Azure AD B2C-klienter.  Berättigad B2C hyresgäster uppfyller dessa villkor: du är global administratör för B2C-klient och B2C-klient är inte associerat till en Azure-prenumeration

2. Azure AD B2C resursnamnet - är förvalt för att matcha domännamnet för B2C-klient

3. Prenumeration – en aktiv Azure-prenumeration som du är administratör eller en medadministratör.  Flera innehavare av Azure AD B2C kan läggas till en Azure-prenumeration

4. Resursgrupp och resursgruppen plats - med hjälp av den här artefakten kan du ordna flera Azure-resurser.  Det här alternativet har ingen inverkan på din B2C-klient plats, prestanda eller fakturering status

5. Fäst på instrumentpanelen för enklaste åtkomst till din faktureringsinformation för B2C-klient och inställningar för B2C-klient ![skapa B2C-resurs](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="step-4---manage-your-b2c-tenant-resources-optional"></a>Steg 4 – hantera B2C-klient-resurser (valfritt)
När installationen är klar får en ny resurs ”B2C-klient” har skapats i målresursgruppen och relaterade Azure-prenumeration.  Du bör se en ny resurs av typen ”B2C-klient” lagt till tillsammans med andra Azure-resurser.

![Skapa resurs för B2C](./media/active-directory-b2c-how-to-enable-billing/b2cresourcedashboard.png)

Genom att klicka på resursen B2C-klient, kan du
- Klicka på prenumerationsnamn att granska faktureringsinformation. Se fakturerings- och användning.
- Klicka på Azure AD B2C-inställningar om du vill öppna en ny webbläsarflik direkt i till din B2C-klient inställningsbladet
- Skicka en supportförfrågan
- Flytta resursernas B2C-klient till en annan Azure-prenumeration eller till en annan resursgrupp.  Det här valet ändringar som Azure-prenumeration tar emot avgifter för användning.

![B2C resursinställningar](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.png)

## <a name="known-issues"></a>Kända problem
- Borttagning av B2C-klient. Om en B2C-klient skapas, tas bort och återskapas med samma domännamn du också ta bort och återskapa ”länka” resurs med samma domännamn.  Du hittar den här resursen ”länka” under ”alla resurser” i prenumerationen klienten via Azure portal.
- Automatisk införas begränsningar för regional resource-plats.  I sällsynta fall kan har en användare upprättat en regional begränsning för att skapa en Azure-resurs.  Den här begränsningen kan hindra skapandet av länken mellan en Azure-prenumeration och en B2C-klient. Slappna av den här begränsningen om du vill minska.

## <a name="next-steps"></a>Nästa steg
När de här stegen är klar för varje B2C-klienter kan debiteras din Azure-prenumeration i enlighet med dina uppgifter för Azure direkt eller Enterprise-avtal.
- Granska användnings- och fakturering inom din valda Azure-prenumeration
- Granska detaljerad dag för dag användningsrapporter med hjälp av den [användning Reporting API](active-directory-b2c-reference-usage-reporting-api.md)
