---
title: Skapa en basgrupp och lägga till medlemmar – Azure Active Directory | Microsoft Docs
description: Anvisningar för hur du skapar en basgrupp med Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 08/22/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.openlocfilehash: 50900bb58fdec909a5922e0e33873a97ce2a72dc
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54448830"
---
# <a name="create-a-basic-group-and-add-members-using-azure-active-directory"></a>Skapa en basgrupp och lägga till medlemmar med hjälp av Azure Active Directory
Du kan skapa en basgrupp med hjälp av Azure Active Directory (Azure AD)-portalen. I den här artikeln läggs en basgrupp till i en enskild resurs av resursägaren (administratören) och innehåller specifika medlemmar (anställda) som behöver åtkomst till den här resursen. Mer komplicerade scenarier, inklusive dynamiskt medlemskap och regelskapande, finns i [dokumentationen för Azure Active Directory-användarhantering](../users-groups-roles/index.yml).

## <a name="create-a-basic-group-and-add-members"></a>Skapa en basgrupp och lägg till medlemmar
Du kan skapa en basgrupp och lägga till dina medlemmar på samma gång.

### <a name="to-create-a-basic-group-and-add-members"></a>Skapa en basgrupp och lägg till medlemmar
1. Logga in på [Azure-portalen](https://portal.azure.com) med ett Globalt administratörskonto för katalogen.

2. Välj **Azure Active Directory**, **Grupper** och välj sedan **Ny grupp**.

    ![Azure AD med grupper som visas](media/active-directory-groups-create-azure-portal/group-full-screen.png)

3. På sidan **Grupp** fyller du i den information som krävs.

    ![Ny gruppsida ifylld med exempelinformation](media/active-directory-groups-create-azure-portal/new-group-blade.png)

    - **Grupptyp (krävs).** Välj en fördefinierad grupptyp. Det här omfattar:
        
        - **Säkerhet**. Används för att hantera medlems- och datoråtkomst till delade resurser för en grupp användare. Du kan till exempel skapa en säkerhetsgrupp för en specifik säkerhetsprincip. Genom att göra det på det sättet så kan du ge en uppsättning behörigheter till alla medlemmar på samma gång utan att behöva lägga till behörigheter till varje medlem individuellt. Mer information om hur du hanterar åtkomst till resurser finns i [Hantera åtkomst till resurser med Azure Active Directory-grupper](active-directory-manage-groups.md).
        
        - **Office 365**. Ger samarbetsmöjligheter genom att ge medlemmar tillgång till en delad postlåda, kalender, filer, SharePoint-webbplats och mer. Det här alternativet låter dig även ge personer utanför din organisation tillgång till gruppen. Mer information om Office 365-grupper finns i [Läs mer om Office 365-grupper](https://support.office.com/article/learn-about-office-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2).

    - **Gruppnamn (krävs).** Lägg till ett namn för gruppen, något som du kommer ihåg och som låter logiskt.

    - **Gruppbeskrivning.** Lägg till en valfri beskrivning till din grupp.

    - **Typ av medlemskap (krävs).** Välj en fördefinierad medlemskapstyp. Det här omfattar:

        - **Tilldelad.** Låter dig lägga till specifika användare som medlemmar i den här gruppen med unika behörigheter. I den här artikeln använder vi det här alternativet.

        - **Dynamisk användare.** Låter dig använda dynamiska gruppregler för att automatiskt lägga till och ta bort medlemmar. Om en medlems attribut ändras så tittar systemet på dina dynamiska gruppregler för katalogen för att se om medlemmen uppfyller regelkraven (läggs till) eller inte längre uppfyller regelkraven (tas bort).

        - **Dynamisk enhet.** Låter dig använda dynamiska gruppregler för att automatiskt lägga till och ta bort enheter. Om en enhets attribut ändras så tittar systemet på dina dynamiska gruppregler för katalogen för att se om enheten uppfyller regelkraven (läggs till) eller inte längre uppfyller regelkraven (tas bort).

        >[!Important]
        >Du kan skapa en dynamisk grupp för antingen enheter eller användare, men inte både och. Du kan inte heller skapa en enhetsgrupp baserat på enhetsägarens attribut. Medlemskapsregler för enheten kan bara referera till enhetens uppgifter. Mer information om hur du skapar en dynamisk grupp för användare och enheter finns i [Skapa en dynamisk grupp och kontrollera status](../users-groups-roles/groups-create-rule.md).

4. Välj **Skapa**.

    Din grupp skapas och är redo att lägga till medlemmar.

5. Välj området **Medlemmar** från den **Grupp**-sidan och börja söka efter medlemmar att lägga till i din grupp från sidan **Välj medlemmar**.

    ![Välj medlemmar för din grupp när gruppen skapas](media/active-directory-groups-create-azure-portal/select-members-create-group.png)

6. När du är klar med att lägga till medlemmar, väljer du **Välj**.

    Sidan **Gruppöversikt** uppdateras för att visa antalet medlemmar som nu lagts till i gruppen.

    ![Gruppöversiktssidan med antalet medlemmar markerat](media/active-directory-groups-create-azure-portal/group-overview-blade-number-highlight.png)

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
