---
title: Dölja ett program från användarupplevelsen i Azure Active Directory | Microsoft Docs
description: Hur du kan dölja ett program från användarupplevelsen i åtkomstpaneler för Azure Active Directory eller Office 365-startprogram.
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: celested
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59c4fa0149b4a8f59dc4fa1e30936b35c1a5ac90
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56191766"
---
# <a name="hide-applications-from-end-users-in-azure-active-directory"></a>Dölj program från slutanvändare i Azure Active Directory

Anvisningar för hur du kan dölja program från slutanvändares MyApps panelen eller startprogrammet för Office 365. När ett program är dold har användare fortfarande behörighet för programmet. 

## <a name="prerequisites"></a>Förutsättningar

Programmet administratörsbehörighet krävs för att dölja ett program från MyApps panelen och startprogram för Office 365.

Behörigheter som global administratör krävs för att dölja alla Office 365-program.


## <a name="hide-an-application-from-the-end-user"></a>Dölja ett program från användaren
Använd följande steg för att dölja ett program från MyApps panelen och startprogrammet för Office 365.

1.  Logga in på den [Azure-portalen](https://portal.azure.com) som global administratör för din katalog.
2.  Välj **Azure Active Directory**.
3.  Välj **företagsprogram**. Den **företagsprogram – alla program** blad öppnas.
4.  Under **programtyp**väljer **företagsprogram**, om det inte redan är markerat.
5.  Sök efter program som du vill dölja och på programmet.  Översikt över programmets öppnas.
6.  Klicka på **Egenskaper**. 
7.  För den **synlig för användarna?** fråga, klickar du på **nr**.
8.  Klicka på **Spara**.


## <a name="hide-office-365-applications-from-the-myapps-panel"></a>Dölj Office 365-program från panelen MyApps

Använd följande steg för att dölja alla Office 365-program från panelen MyApps. Programmen är fortfarande visas i Office 365-portalen.

1.  Logga in på den [Azure-portalen](https://portal.azure.com) som global administratör för din katalog.
2.  Välj **Azure Active Directory**.
3.  Välj **användarinställningar**.
4.  Under **företagsprogram**, klickar du på **hantera hur slutanvändare startar och visar sina program.**
5.  För **användare kan bara se Office 365-appar i Office 365-portalen**, klickar du på **Ja**.
6.  Klicka på **Spara**.


## <a name="next-steps"></a>Nästa steg
* [Se alla mina grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Tilldela en användare eller grupp till en företagsapp](assign-user-or-group-access-portal.md)
* [Ta bort en användare eller grupp från en företagsapp](remove-user-or-group-access-portal.md)
* [Ändra namnet eller logotyp i en företagsapp](change-name-or-logo-portal.md)

