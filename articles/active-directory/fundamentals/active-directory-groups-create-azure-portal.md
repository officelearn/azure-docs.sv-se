---
title: Skapa en basgrupp och lägga till medlemmar – Azure Active Directory | Microsoft Docs
description: Anvisningar för hur du skapar en basgrupp med Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: quickstart
ms.date: 03/01/2019
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c551a81788df8d68664abaa03f467a4521222d0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "73473459"
---
# <a name="create-a-basic-group-and-add-members-using-azure-active-directory"></a>Skapa en basgrupp och lägga till medlemmar med hjälp av Azure Active Directory
Du kan skapa en basgrupp med hjälp av Azure Active Directory (Azure AD)-portalen. I den här artikeln läggs en basgrupp till i en enskild resurs av resursägaren (administratören) och innehåller specifika medlemmar (anställda) som behöver åtkomst till den här resursen. Mer komplicerade scenarier, inklusive dynamiskt medlemskap och regelskapande, finns i [dokumentationen för Azure Active Directory-användarhantering](../users-groups-roles/index.yml).

## <a name="create-a-basic-group-and-add-members"></a>Skapa en basgrupp och lägg till medlemmar
Du kan skapa en basgrupp och lägga till dina medlemmar på samma gång.

### <a name="to-create-a-basic-group-and-add-members"></a>Skapa en basgrupp och lägg till medlemmar
1. Logga in på [Azure-portalen](https://portal.azure.com) med ett Globalt administratörskonto för katalogen.

1. Sök efter och välj **Azure Active Directory**.

    ![Sidan Azure AD med grupper som visar](media/active-directory-groups-create-azure-portal/search-active-directory.png)

1. På sidan **Active Directory** väljer du **grupper** och väljer sedan **ny grupp**.

    ![Sidan Azure AD med grupper som visar](media/active-directory-groups-create-azure-portal/group-full-screen.png)

1. Fyll i den information som krävs på sidan **ny grupp** .

    ![Ny gruppsida ifylld med exempelinformation](media/active-directory-groups-create-azure-portal/new-group-blade.png)

   - **Grupptyp (krävs).** Välj en fördefinierad grupptyp. Detta omfattar:- **säkerhet**. Används för att hantera medlems- och datoråtkomst till delade resurser för en grupp användare. Du kan till exempel skapa en säkerhetsgrupp för en specifik säkerhetsprincip. Genom att göra det på det sättet så kan du ge en uppsättning behörigheter till alla medlemmar på samma gång utan att behöva lägga till behörigheter till varje medlem individuellt. Mer information om hur du hanterar åtkomst till resurser finns i [Hantera åtkomst till resurser med Azure Active Directory-grupper](active-directory-manage-groups.md).
               - **Office 365**. Ger samarbetsmöjligheter genom att ge medlemmar tillgång till en delad postlåda, kalender, filer, SharePoint-webbplats och mer. Med det här alternativet kan du också ge personer utanför organisationen åtkomst till gruppen. Mer information om Office 365-grupper finns i [Läs mer om Office 365-grupper](https://support.office.com/article/learn-about-office-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2).

   - **Gruppnamn (krävs).** Lägg till ett namn för gruppen, något som du kommer ihåg och som låter logiskt. En kontroll görs för att avgöra om namnet redan används för en annan grupp. Om namnet redan används, så uppmanas du att ändra namnet på din grupp för att undvika dubbla namn.

   - **Gruppens e-postadress (krävs).** Lägg till en e-postadress för gruppen eller Behåll e-postadressen som fylls i automatiskt.

   - **Gruppbeskrivning.** Lägg till en valfri beskrivning till din grupp.

   - **Typ av medlemskap (krävs).** Välj en fördefinierad medlemskapstyp. Detta omfattar:- **tilldelad.** Låter dig lägga till specifika användare som medlemmar i den här gruppen med unika behörigheter. I den här artikeln använder vi det här alternativet.
          - **Dynamisk användare.** Gör att du kan använda dynamiska medlemskaps regler för att automatiskt lägga till och ta bort medlemmar. Om en medlems attribut ändras så tittar systemet på dina dynamiska gruppregler för katalogen för att se om medlemmen uppfyller regelkraven (läggs till) eller inte längre uppfyller regelkraven (tas bort).
          - **Dynamisk enhet.** Låter dig använda dynamiska gruppregler för att automatiskt lägga till och ta bort enheter. Om en enhets attribut ändras så tittar systemet på dina dynamiska gruppregler för katalogen för att se om enheten uppfyller regelkraven (läggs till) eller inte längre uppfyller regelkraven (tas bort).

        >[!Important]
        >Du kan skapa en dynamisk grupp för antingen enheter eller användare, men inte både och. Du kan inte heller skapa en enhetsgrupp baserat på enhetsägarens attribut. Medlemskapsregler för enheten kan bara referera till enhetens uppgifter. Mer information om hur du skapar en dynamisk grupp för användare och enheter finns i [Skapa en dynamisk grupp och kontrollera status](../users-groups-roles/groups-create-rule.md).

1. Välj **Skapa**.

    Din grupp skapas och är redo att lägga till medlemmar.

1. Välj området **Medlemmar** från den **Grupp**-sidan och börja söka efter medlemmar att lägga till i din grupp från sidan **Välj medlemmar**.

    ![Välj medlemmar för din grupp när gruppen skapas](media/active-directory-groups-create-azure-portal/select-members-create-group.png)

1. När du är klar med att lägga till medlemmar, väljer du **Välj**.

    Sidan **Gruppöversikt** uppdateras för att visa antalet medlemmar som nu lagts till i gruppen.

    ![Gruppöversiktssidan med antalet medlemmar markerat](media/active-directory-groups-create-azure-portal/group-overview-blade-number-highlight.png)

## <a name="turn-on-or-off-welcome-email"></a>Aktivera eller inaktivera välkomst meddelande

När en ny Office 365-grupp skapas, oavsett om det är ett dynamiskt eller statiskt medlemskap, skickas ett välkomst meddelande till alla användare som läggs till i gruppen. När alla attribut för en användare eller enhet ändras bearbetas alla dynamiska grupp regler i organisationen för potentiella medlemskaps ändringar. Användare som läggs till får även välkomst meddelandet. Du kan inaktivera det här beteendet i [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps). 

## <a name="next-steps"></a>Nästa steg
Nu när du har lagt till en grupp och minst en användare så kan du:

- [Visa dina grupper och medlemmar](active-directory-groups-view-azure-portal.md)

- [Hantera gruppmedlemskap](active-directory-groups-membership-azure-portal.md)

- [Hantera dynamiska regler för användare i en grupp](../users-groups-roles/groups-create-rule.md)

- [Redigera dina gruppinställningar](active-directory-groups-settings-azure-portal.md)

- [Hantera åtkomst till resurser med grupper](active-directory-manage-groups.md)

- [Hantera åtkomst till SaaS-appar med grupper](../users-groups-roles/groups-saasapps.md)

- [Hantera grupper med PowerShell-kommandon](../users-groups-roles/groups-settings-v2-cmdlets.md)

- [Associera eller lägg till en Azure-prenumeration till Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
