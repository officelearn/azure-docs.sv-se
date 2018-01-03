---
title: "Så här länkar du en Azure-prenumeration till Azure AD B2C | Microsoft Docs"
description: "Steg för steg-guide för att aktivera fakturering för Azure AD B2C-klient till en Azure-prenumeration."
services: active-directory-b2c
documentationcenter: dev-center-name
author: parakhj
manager: mtillman
ms.service: active-directory-b2c
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2017
ms.author: parja
ms.openlocfilehash: 063c00fe47be25b9359e80d71abfaf453c7a7074
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2017
---
# <a name="linking-an-azure-subscription-to-an-azure-ad-b2c-tenant"></a>Länka en Azure-prenumeration till en Azure AD B2C-klient

> [!IMPORTANT]
> Den senaste informationen om användning fakturering och priser för Azure AD B2C finns på följande sida: [Azure AD B2C-priser](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

Kostnader för Azure AD B2C debiteras till en Azure-prenumeration. När en Azure AD B2C-klient skapas måste innehavaradministratören uttryckligen länka Azure AD B2C-klient till en Azure-prenumeration. Den här artikeln visar hur du gör.

> [!NOTE]
> En prenumeration som är kopplad till en Azure AD B2C-klient kan endast användas för fakturering för användning av Azure AD B2C. Prenumerationen kan inte användas för att lägga till andra Azure-tjänster eller Office 365 licenser *i Azure AD B2C-klient*.

 Länken prenumeration uppnås genom att skapa en Azure AD B2C ”resurs” inom den Azure-prenumeration. Många Azure AD B2C ”resurser” kan skapas i en enda Azure-prenumeration, tillsammans med andra Azure-resurser (till exempel virtuella datorer, lagring av Data, LogicApps). Du kan se alla resurser i prenumerationen genom att gå till Azure AD-klient som prenumerationen är kopplad till.

En giltig Azure-prenumeration krävs för att fortsätta.

## <a name="create-an-azure-ad-b2c-tenant"></a>Skapa en Azure AD B2C-klient

Du måste först [skapa en Azure AD B2C-klient](active-directory-b2c-get-started.md) som du vill länka en prenumeration på. Hoppa över det här steget om du redan har skapat en Azure AD B2C-klient.

## <a name="open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>Öppna Azure-portalen i Azure AD-klient som visar din Azure-prenumeration

Gå till Azure AD-klient som visar din Azure-prenumeration. Öppna den [Azure-portalen](https://portal.azure.com), och växla till Azure AD-klient som visar Azure-prenumeration om du vill använda.

![Växla till Azure AD-klient](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="find-azure-ad-b2c-in-the-azure-marketplace"></a>Hitta Azure AD B2C i Azure Marketplace

Klicka på **Nytt**. I fältet **Sök på marketplace** skriver du `B2C`.

![Lägg till knappen markerad och texten Azure AD B2C Sök i fältet marketplace](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c.png)

Välj i resultatlistan **Azure AD B2C**.

![Azure AD B2C som valts i resultatlistan över](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c-result.png)

Information om Azure AD B2C visas. Du börjar konfigurera den nya Azure Active Directory B2C-klientorganisationen genom att klicka på **Skapa**.

På skärmen resursen skapas väljer **länka ett befintligt Azure AD B2C-klient till min Azure-prenumeration**.

## <a name="create-an-azure-ad-b2c-resource-within-the-azure-subscription"></a>Skapa en Azure AD B2C-resurs i Azure-prenumeration

Välj en Azure AD B2C-klient i dialogrutan Skapa resurs i listrutan. Alla klienter som du är global administratör för och som inte redan är länkade till en prenumeration visas.

Resursnamnet Azure AD B2C kommer vara förvald för att matcha domännamnet för Azure AD B2C-klient.

Välj en aktiv Azure-prenumeration som du är administratör för prenumeration.

Välj en resursgrupp och plats för resursgruppen. Den här markeringen har ingen inverkan på din plats för Azure AD B2C-klient, prestanda eller fakturering status.

![Skapa resurs för B2C](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Hantera resurserna i Azure AD B2C-klient

När en resurs i Azure AD B2C har skapats i Azure-prenumeration, bör du se en ny resurs av typen ”B2C-klient” lagt till tillsammans med andra Azure-resurser.

Du kan använda den här resursen till:

- Gå till prenumerationen att granska faktureringsinformation.
- Gå till din Azure AD B2C-klient
- Skicka en supportförfrågan
- Flytta din Azure AD B2C-klient resurs till en annan Azure-prenumeration eller till en annan resursgrupp.

![B2C resursinställningar](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.png)

## <a name="known-issues"></a>Kända problem

### <a name="csp-subscriptions"></a>CSP-prenumerationer

För närvarande en Azure AD B2C-klient **kan** länk till CSP-prenumerationer.

### <a name="self-imposed-restrictions"></a>Automatisk införas begränsningar

En användare har upprättat en regional begränsning för att skapa en Azure-resurs. Den här begränsningen kan förhindra att Azure AD B2C-resurs. Slappna av den här begränsningen om du vill minska.

## <a name="next-steps"></a>Nästa steg

När de här stegen är klar för var och en av dina Azure AD B2C-klienter kan debiteras din Azure-prenumeration i enlighet med dina uppgifter för Azure direkt eller Enterprise-avtal.

Du kan granska användnings- och faktureringsinformation inom din valda Azure-prenumeration. Du kan också granska detaljerad dag för dag användningsrapporter med hjälp av den [användning reporting API](active-directory-b2c-reference-usage-reporting-api.md).
