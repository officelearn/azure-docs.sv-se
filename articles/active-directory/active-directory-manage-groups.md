---
title: Använda grupper för att hantera åtkomst till resurser i Azure Active Directory | Microsoft Docs
description: Hur du använder grupper i Azure Active Directory för att hantera användarnas åtkomst till lokala och molnprogram och resurser.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017;it-pro
ms.reviewer: piotrci
ms.openlocfilehash: 6163f8d2466637933024760c6d748c6007803b1d
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712954"
---
# <a name="manage-access-to-resources-with-azure-active-directory-groups"></a>Hantera åtkomst till resurser med Azure Active Directory-grupper
Azure Active Directory (AD Azure) är en omfattande identitets- och hanteringslösning som tillhandahåller kraftfulla och stabila funktioner för att hantera åtkomst till lokala och molnprogram och resurser inklusive Microsofts onlinetjänster som Office 365 och en värld av icke - Microsoft SaaS-program. Den här artikeln innehåller en översikt, men om du vill börja använda Azure AD grupper just nu, följ instruktionerna i [hantera säkerhetsgrupper i Azure AD](active-directory-groups-create-azure-portal.md). Om du vill se hur du kan använda PowerShell för att hantera grupper i Azure Active directory kan du läsa mer i [Azure Active Directory-cmdlets för grupphantering](active-directory-accessmanagement-groups-settings-v2-cmdlets.md).

> [!NOTE]
> Du behöver ett Azure-konto om du vill använda Azure Active Directory. Om du inte har ett konto kan du [registrera dig för ett kostnadsfritt Azure-konto](https://azure.microsoft.com/pricing/free-trial/).
>
>

En av de viktigaste funktionerna är möjligheten att hantera åtkomst till resurser i Azure AD. Dessa resurser kan inte ingå i katalogen, som i fallet med behörigheter för att hantera objekt genom roller i katalogen eller resurser som är externa för katalogen som SaaS-program, Azure-tjänster, och SharePoint-webbplatser eller lokala resurser. Det finns en användare kan tilldelas behörigheter till en resurs på fyra olika sätt:

1. Direkttilldelning

    Användare kan tilldelas direkt till en resurs som ägare av den här resursen.
2. Gruppmedlemskap

    En grupp kan bara kopplas till en resurs med resursägare och genom att göra det, bevilja medlemmarna i den aktuella gruppåtkomst till resursen. Medlemskap i gruppen kan sedan hanteras av ägare av gruppen. Effektivt, resursägaren behörighet att tilldela användare till resursen till ägare av gruppen.
3. Regelbaserad

    Resursägaren kan använda en regel för att uttrycka vilka användare ska tilldelas åtkomst till en resurs. Resultatet av regeln beror på de attribut som används i regeln och deras värden för specifika användare, och därigenom resursägaren effektivt behörighet att hantera åtkomst till resursen till auktoritativ datakälla för attribut som används i regeln. Resursägare hanterar regeln själva fortfarande och avgör vilka attribut och värden som ger åtkomst till resursen.
4. Externa utfärdare

    Åtkomst till en resurs som har härletts från en extern källa; till exempel en grupp som ska synkroniseras från en betrodd källa, till exempel en lokal katalog eller en SaaS-app, till exempel WorkDay. Resursägare tilldelas grupp för att ge åtkomst till resursen och den externa källan hanterar medlemmar i gruppen.

   ![Översikt över access management diagram](./media/active-directory-access-management-groups/access-management-overview.png)

## <a name="watch-a-video-that-explains-access-management"></a>Se en video som förklarar åtkomsthantering
Du kan titta på en kort video som förklarar mer om detta:

**Azure AD: Introduktion till dynamiskt medlemskap för grupper**

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD--Introduction-to-Dynamic-Memberships-for-Groups/player]
>
>

## <a name="how-does-access-management-in-azure-active-directory-work"></a>Hur har tillgång till hantering i Azure Active Directory arbete?
Lösning för hantering av åtkomst är säkerhetsgruppen i mitten av Azure AD. Använda en säkerhetsgrupp för att hantera åtkomst till resurser är en välkänd paradigmet så ger ett flexibelt och enkelt att förstå sätt att ge åtkomst till en resurs för den avsedda gruppen med användare. Resursägare (eller administratör för katalogen) kan tilldela en grupp som ger en viss åtkomst till resurser som de äger. Medlemmar i gruppen ges åtkomst och resursägaren kan delegera behörighet för att hantera listan över medlemmar i en grupp till någon annan, till exempel en avdelningschef eller administratör supportavdelningen.

![Azure Active Directory access management diagram](./media/active-directory-access-management-groups/active-directory-access-management-works.png)

Ägare för en grupp kan också göra gruppen tillgänglig för självbetjäning begäranden. På så sätt kan en slutanvändare söka efter och hitta gruppen och gör en begäran att ansluta till, sökning effektivt behörighet att komma åt resurser som hanteras via gruppen. Ägare av gruppen kan ställa in gruppen så att anslutningsbegäranden godkänns automatiskt eller begära godkännande av ägare av gruppen. När en användare gör en begäran om att delta i en grupp, vidarebefordras begäran till ägare av gruppen. Om en av ägarna godkänner begäran meddelas användaren och användaren är ansluten till gruppen. Om en av ägarna nekar begäran meddelas användaren men inte tillhör gruppen.

## <a name="getting-started-with-access-management"></a>Komma igång med åtkomsthantering
Redo att sätta igång? Du bör testa några av de grundläggande åtgärderna som du kan göra med Azure AD-grupper. Använd dessa funktioner för att ge särskilda åtkomst till olika grupper av personer för olika resurser i din organisation. Nedan visas en lista över grundläggande första steg.

* [Skapa en enkel regel om du vill konfigurera dynamiskt medlemskap för en grupp](active-directory-groups-create-azure-portal.md)
* [Hantera åtkomst till SaaS-program med hjälp av en grupp](active-directory-accessmanagement-group-saasapps.md)
* [Göra en grupp tillgänglig för slutanvändaren självbetjäning](active-directory-accessmanagement-self-service-group-management.md)
* [Synkroniserar en lokal grupp till Azure med Azure AD Connect](active-directory-aadconnect.md)
* [Hantera ägare för en grupp](active-directory-accessmanagement-managing-group-owners.md)

## <a name="next-steps"></a>Nästa steg
Nu när du har förstått grunderna i åtkomsthantering finns här några ytterligare avancerade funktioner i Azure Active Directory för att hantera åtkomst till dina program och resurser.

* [Använda attribut för att skapa avancerade regler](active-directory-groups-dynamic-membership-azure-portal.md)
* [Hantera säkerhetsgrupper i Azure AD](active-directory-groups-create-azure-portal.md)
* [Ställa in dedikerade grupper i Azure AD](active-directory-accessmanagement-dedicated-groups.md)
* [Referens för Graph API för grupper](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#GroupFunctions)
* [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](active-directory-accessmanagement-groups-settings-cmdlets.md)
