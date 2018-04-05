---
title: Skaffa en Azure AD-klient | Microsoft Docs
description: Hur du skaffar en Azure Active Directory-klient för registrering och utveckling av program.
services: active-directory
documentationcenter: ''
author: mtillman
manager: mtillman
editor: ''
ms.assetid: 1f4b24eb-ab4d-4baa-a717-2a0e5b8d27cd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/23/2018
ms.author: mtillman
ms.custom: aaddev
ms.openlocfilehash: ab7db49fa07f260de6ebbe4b2cee943b64cab7fe
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2018
---
# <a name="how-to-get-an-azure-active-directory-tenant"></a>Skaffa en Azure Active Directory-klient
I Azure Active Directory (Azure AD) representerar en [klient](https://msdn.microsoft.com/library/azure/jj573650.aspx#Anchor_0) en organisation.  Det är en dedikerad instans av Azure AD-tjänsten som en organisation tilldelas och äger när den skapar en relation med Microsoft t.ex. genom att registrera sig för en Microsoft-molntjänst som Azure, Microsoft Intune och Office 365.  Varje Azure AD-klient är separat och åtskild från andra Azure AD-klienter.  

En klient inrymmer användarna på ett företag och informationen om dem – deras lösenord, användarprofildata, behörigheter och så vidare.  Den innehåller också grupper, program och annan information som hör till en organisation och dess säkerhet.

Om du vill tillåta att Azure AD-användare loggar in till ditt program måste du registrera programmet i en klient som du äger.  Att skapa en Azure AD-klient och publicera ett program i den är **helt kostnadsfritt** (även om du betalar för premiumfunktioner i din klient).  Många utvecklare skapar i själva verket flera klienter och program för experimentering, utveckling, mellanlagring och testning.

## <a name="use-an-existing-azure-ad-tenant"></a>Använd en befintlig Azure AD-klient

Många utvecklare har redan klienter via tjänster eller prenumerationer som är knutna till Azure AD-klienter (till exempel: Office 365 eller Azure-prenumerationer).  Om du vill kontrollera om du redan har en klient, logga in på [Azure Portal](https://portal.azure.com) med det konto som du vill använda för att hantera ditt program och kontrollera i det övre högra hörnet där din kontoinformation visas.  Om du har en klient kommer du automatiskt att loggas in på den och du ser klintens namn direkt under namnet på ditt konto.  Om ditt konto är kopplat till flera klienter måste du klicka på namnet på ditt konto för att öppna en meny där du kan växla mellan klienter.

Om du inte har en befintlig klient som är kopplad till ditt konto kan du ser ett GUID under namnet på ditt konto och kommer inte att kunna utföra åtgärder som att registrera appar förrän du [skapar en ny klient](#create-a-new-azure-ad-tenant).

## <a name="create-a-new-azure-ad-tenant"></a>Skapa en ny Azure AD-klient

Om du inte redan har en Azure AD-klient eller vill skapa en ny, kan du göra det med hjälp av [upplevelsen för att skapa kataloger](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) i [Azure Portal](https://portal.azure.com).  Processen tar ungefär en minut och i slutet kommer du att ombes att navigera till din nya klient.