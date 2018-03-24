---
title: Skapa en Azure Active Directory B2C-klient | Microsoft Docs
description: Ett avsnitt om hur du skapar en Azure Active Directory B2C-klient
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 06/07/2017
ms.author: davidmu
ms.openlocfilehash: 56e0ae7454e86911c894da88b5aa8ccc03a08af3
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="create-an-azure-active-directory-b2c-tenant-in-the-azure-portal"></a>Skapa en Azure Active Directory B2C-klient i Azure-portalen

Den här snabbstarten hjälper dig att skapa en Microsoft Azure Active Directory (AD Azure) B2C-klient på bara några minuter. När du är klar har du en B2C-klient (även kallat en katalog) ska användas för att registrera B2C-program.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-an-azure-ad-b2c-tenant"></a>Skapa en Azure AD B2C-klient

B2C-funktioner kan inte aktiveras i din befintliga klienter. Du måste skapa en Azure AD B2C-klient.

[!INCLUDE [active-directory-b2c-create-tenant](../../includes/active-directory-b2c-create-tenant.md)]

Grattis, du har skapat en Azure Active Directory B2C-klient. Du är en Global administratör för klienten. Du kan lägga till andra globala administratörer om det behövs. Växla till den nya innehavaren, klicka på den *hantera nya klient länken*.

![Hantera din ny klient-länk](./media/active-directory-b2c-get-started/manage-new-b2c-tenant-link.png)

> [!IMPORTANT]
> Om du planerar att använda en B2C-klient för en produktionsapp artikeln om [produktionsskala kontra preview B2C hyresgäster](active-directory-b2c-reference-tenant-type.md). Det finns kända problem när du tar bort en befintlig B2C-klient och skapa den igen med samma domännamn. Du måste skapa en B2C-klient med ett annat domännamn.
>
>

## <a name="link-your-tenant-to-your-subscription"></a>Länka din klient till din prenumeration

Du måste länka din Azure AD B2C-klient till din Azure-prenumeration att aktivera alla B2C-funktioner och betalar avgifter för användning. Mer information, [i den här artikeln](active-directory-b2c-how-to-enable-billing.md). Om du inte länka din Azure AD B2C-klient till din Azure-prenumeration, vissa funktioner är blockerad och visas ett varningsmeddelande (”ingen prenumeration länkad till den här B2C-klient eller prenumeration måste kontrolleras”.) i B2C-inställningar. Det är viktigt att du vidtar åtgärden innan du levererar dina appar i produktionen.

## <a name="easy-access-to-settings"></a>Enkel åtkomst till inställningar

[!INCLUDE [active-directory-b2c-find-service-settings](../../includes/active-directory-b2c-find-service-settings.md)]

Du kan också komma åt bladet genom att ange `Azure AD B2C` i **söka resurser** överst i portalen. Välj i resultatlistan **Azure AD B2C** att komma åt bladet B2C-inställningar.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Registrera din B2C-program i en B2C-klient](active-directory-b2c-app-registration.md)