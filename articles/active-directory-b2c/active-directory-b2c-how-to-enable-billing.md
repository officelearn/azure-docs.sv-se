---
title: Så här länkar du en Azure-prenumeration – Azure Active Directory B2C | Microsoft Docs
description: Stegvis guide för att aktivera fakturering för Azure AD B2C-klient till en Azure-prenumeration.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 01/24/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5cc6c8b4947f8c5ebeb27be2dc82f323f84a6902
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835426"
---
# <a name="link-an-azure-subscription-to-an-azure-active-directory-b2c-tenant"></a>Länka en Azure-prenumeration till en Azure Active Directory B2C-klient

> [!IMPORTANT]
> Den senaste informationen om fakturering och priser för Azure Active Directory (Azure AD) B2C finns i [priser för Azure AD B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

Användningskostnader för Azure AD B2C debiteras till en Azure-prenumeration. När en Azure AD B2C-klient har skapats kan måste klientadministratören uttryckligen länka Azure AD B2C-klient till en Azure-prenumeration. Den här artikeln visar hur du gör.

> [!NOTE]
> En prenumeration som är länkad till en Azure AD B2C-klient kan användas för att faktureringen för Azure AD B2C användnings- eller andra Azure-resurser, inklusive ytterligare Azure AD B2C-resurser.  Det kan inte användas för att lägga till andra Azure-licens-baserade tjänster eller Office 365-licenser i Azure AD B2C-klient.

Prenumerationslänken uppnås genom att skapa en Azure AD-B2C ”resurs” i Azure-målprenumerationen. Många Azure AD-B2C ”resurser” kan skapas i en enda Azure-prenumeration, tillsammans med andra Azure-resurser (till exempel virtuella datorer, datalagring, LogicApps). Du kan se alla resurser i prenumerationen genom att gå till Azure AD-klient som prenumerationen är associerad till.

Azure Cloud Solution Providers (CSP)-prenumerationer stöds i Azure AD B2C. Funktionen är tillgänglig i API: er eller Azure-portalen för Azure AD B2C och för alla Azure-resurser. CSP prenumerationens administratörer kan länka, flytta och ta bort relationer med Azure AD B2C på samma sätt som är klar för alla Azure-resurser. Hantering av Azure AD B2C med hjälp av rollbaserad åtkomstkontroll påverkas inte av kopplingen mellan Azure AD B2C-klient och en Azure CSP-prenumeration. Rollbaserad åtkomstkontroll uppnås med hjälp av klient-base roller, inte prenumerationsbaserade roller.

En giltig Azure-prenumeration krävs för att fortsätta.

## <a name="create-an-azure-ad-b2c-tenant"></a>Skapa en Azure AD B2C-klient

Du måste först [skapa en Azure AD B2C-klient](active-directory-b2c-get-started.md) som du vill länka en prenumeration på. Hoppa över det här steget om du redan har skapat en Azure AD B2C-klient.

## <a name="open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>Öppna Azure-portalen i Azure AD-klient som visar din Azure-prenumeration

Gå till Azure AD-klient som visar din Azure-prenumeration. Öppna den [Azure-portalen](https://portal.azure.com), och växla till Azure AD-klient som visar Azure-prenumeration du vill använda.

![Växla till Azure AD-klienten](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="find-azure-ad-b2c-in-the-azure-marketplace"></a>Hitta Azure AD B2C på Azure Marketplace

Klicka på den **skapa en resurs** knappen. I den **Sök på Marketplace** anger `Active Directory B2C`.

![Portalen skärmbild med ”Active Directory B2C' i Marketplace-sökning](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c.png)

I resultatlistan väljer **Azure AD B2C**.

![Azure AD B2C markerat i resultatlistan](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c-result.png)

Information om Azure AD B2C visas. Du börjar konfigurera den nya Azure Active Directory B2C-klientorganisationen genom att klicka på **Skapa**.

På skärmen resurs skapas väljer **länka en befintlig Azure AD B2C-Klientorganisation till min Azure-prenumeration**.

## <a name="create-an-azure-ad-b2c-resource-within-the-azure-subscription"></a>Skapa en Azure AD B2C-resurs i Azure-prenumeration

I den resurs skapas, väljer du en Azure AD B2C-klient i listrutan. Du ser att alla klienter som du är global administratör för och de som inte redan är länkade till en prenumeration.

Azure AD B2C-resursnamn ska väljas i förväg för att matcha domännamnet för Azure AD B2C-klient.

Välj en aktiv Azure-prenumeration som du är administratör för prenumerationen.

Välj en resursgrupp och plats för resursgruppen. Det här urvalet har ingen inverkan på din Azure AD B2C-klientplats, prestanda eller fakturering status.

![Sidan Azure AD B2C-resursen skapas i Azure-portalen](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Hantera dina resurser för Azure AD B2C-klient

När en Azure AD B2C-resurs har skapats i Azure-prenumeration, bör du se en ny resurs av typen ”B2C-klient” har lagts till tillsammans med andra Azure-resurser.

Du kan använda den här resursen till:

- Navigera till prenumerationen som ska granska faktureringsinformation.
- Gå till din Azure AD B2C-klient
- Skicka en supportförfrågan
- Flytta din Azure AD B2C-klient-resurs till en annan Azure-prenumeration eller till en annan resursgrupp.

![Inställningssidan för B2C-resursen i Azure portal](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.PNG)

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Ändra Azure AD B2C-klient fakturering prenumeration

Azure AD B2C-klienter kan flyttas till en annan prenumeration om käll- och -prenumerationer finns i samma Azure Active Directory-klient.

Läs hur du flyttar Azure-resurser som din Azure AD B2C-klient till en annan prenumeration i [flytta resurser till ny resursgrupp eller prenumeration](../azure-resource-manager/resource-group-move-resources.md).

Innan du startar flytten bör du läsa hela artikeln för att helt förstå begränsningar och krav för en sådan flytt. Förutom anvisningar för att flytta resurser innehåller den kritiska information som en checklista för före flytten och hur du validerar flyttåtgärden.

## <a name="known-issues"></a>Kända problem

### <a name="self-imposed-restrictions"></a>Lokal begränsningsfel begränsningar

En användare har upprättat en regional begränsning för att skapa en Azure-resurs. Den här begränsningen kan inte skapandet av Azure AD B2C-resursen. Slappna av den här begränsningen för att lösa.

## <a name="next-steps"></a>Nästa steg

När dessa steg har slutförts för var och en av dina Azure AD B2C-klienter, är din Azure-prenumeration debiteras i enlighet med din information om Azure Direct- eller Enterprise-avtal.

Du kan granska användning och visa faktureringsinformation i din valda Azure-prenumeration. Du kan också granska detaljerad användning av dag för dag-rapporter med hjälp av den [användning reporting API](active-directory-b2c-reference-usage-reporting-api.md).
