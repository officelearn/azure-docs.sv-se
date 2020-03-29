---
title: Lägga till eller uppdatera information om användarprofiler – Azure AD
description: Instruktioner om hur du lägger till information i en användares profil i Azure Active Directory, inklusive en bild och jobbinformation.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ba51b0ad7f314058f34092dff4e26411020086a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422885"
---
# <a name="add-or-update-a-users-profile-information-using-azure-active-directory"></a>Lägga till eller uppdatera en användares profilinformation med hjälp av Azure Active Directory
Lägg till användarprofilinformation, inklusive en profilbild, jobbspecifik information och vissa inställningar med Azure Active Directory (Azure AD). Mer information om hur du lägger till nya användare finns i [Så här lägger du till eller tar bort användare i Azure Active Directory](add-users-azure-active-directory.md).

## <a name="add-or-change-profile-information"></a>Lägga till eller ändra profilinformation
Som du ser finns det mer information tillgänglig i en användares profil än vad du kan lägga till när användaren skapas. All denna ytterligare information är valfri och kan läggas till efter behov av din organisation.

## <a name="to-add-or-change-profile-information"></a>Så här lägger du till eller ändrar profilinformation
1. Logga in på [Azure-portalen](https://portal.azure.com/) som användaradministratör för organisationen.

2. Välj **Azure Active Directory**, välj **Användare**och välj sedan en användare. Till exempel _Alain Charon_.

    Sidan **Alain Charon - Profil** visas.

    ![Användarens profilsida, inklusive redigerbar information](media/active-directory-users-profile-azure-portal/user-profile-all-blade.png)

3. Välj **Redigera** om du vill lägga till eller uppdatera informationen som ingår i vart och ett av de tillgängliga avsnitten.

    ![Användarens profilsida som visar de redigerbara områdena](media/active-directory-users-profile-azure-portal/user-profile-edit.png)

    - **Profilbild.** Välj en miniatyrbild för användarens konto. Den här bilden visas i Azure Active Directory och på användarens personliga sidor, till exempel myapps.microsoft.com sidan.

    - **Identitet.** Lägg till eller uppdatera ett ytterligare identitetsvärde för användaren, till exempel ett gift efternamn. Du kan ange det här namnet oberoende av värdena för förnamn och efternamn. Du kan till exempel använda den för att inkludera initialer, ett företagsnamn eller för att ändra sekvensen med namn som visas. I ett annat exempel, för två användare vars namn är "Chris Green" kan du använda identitetssträngen för att ställa in deras namn till "Chris B. Green" "Chris R. Green (Contoso)."

    - **Jobbinformation.** Lägg till jobbrelaterad information, till exempel användarens befattning, avdelning eller chef.

    - **Inställningar.** Bestäm om användaren kan logga in på Azure Active Directory-klient. Du kan också ange användarens globala plats.

    - **Kontaktinformation.** Lägg till relevant kontaktinformation för användaren, med undantag för användarens telefon- eller mobilkontaktinformation (endast en global administratör kan uppdatera för användare i administratörsroller).

    - **Kontaktinformation för autentisering.** Kontrollera den här informationen för att kontrollera att det finns ett aktivt telefonnummer och en e-postadress för användaren. Den här informationen används av Azure Active Directory för att se till att användaren verkligen är användaren under inloggningen. Autentiseringskontaktinformation kan bara uppdateras av en global administratör.

4. Välj **Spara**.

    Alla dina ändringar sparas för användaren.

    >[!Note]
    >Du måste använda Active Directory i Windows Server för att uppdatera identitet, kontaktinformation eller jobbinformation för användare vars auktoritetskälla är Active Directory för Windows Server. När du har slutfört uppdateringen måste du vänta tills nästa synkroniseringscykel har slutförts innan ändringarna visas.

## <a name="next-steps"></a>Nästa steg
När du har uppdaterat användarnas profiler kan du utföra följande grundläggande processer:

- [Lägga till eller ta bort användare](add-users-azure-active-directory.md)

- [Tilldela roller till användare](active-directory-users-assign-role-azure-portal.md)

- [Skapa en basgrupp och lägg till medlemmar](active-directory-groups-create-azure-portal.md)

Du kan också utföra andra användarhanteringsuppgifter, till exempel tilldela ombud, använda principer och dela användarkonton. Mer information om andra tillgängliga åtgärder finns i Dokumentationen för [Azure Active Directory-användarhantering](../users-groups-roles/index.yml).
