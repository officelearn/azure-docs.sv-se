---
title: Lägg till eller uppdatera en användares profilinformation – Azure Active Directory | Microsoft Docs
description: Anvisningar om hur du lägger till information till en användares profil i Azure Active Directory, inklusive en bild och jobbet information.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: lizross
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d710a86bb63765ea8a1a777818ca5f99e38d3a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60248112"
---
# <a name="add-or-update-a-users-profile-information-using-azure-active-directory"></a>Lägg till eller uppdatera en användares profilinformation med hjälp av Azure Active Directory
Lägg till information om användarprofiler, inklusive en profilbild, projektspecifika information och vissa inställningar med hjälp av Azure Active Directory (AD Azure). Läs mer om att lägga till nya användare, [lägga till eller ta bort användare i Azure Active Directory](add-users-azure-active-directory.md).

## <a name="add-or-change-profile-information"></a>Lägga till eller ändra profilinformation
Som du ser finns det mer information i en användares profil än vad du kan lägga till när användarens skapas. Alla ytterligare information är valfri och kan läggas till efter behov av din organisation.

## <a name="to-add-or-change-profile-information"></a>Lägga till eller ändra profilinformation
1. Logga in på den [Azure-portalen](https://portal.azure.com/) som en användare med rollen för organisationen.

2. Välj **Azure Active Directory**väljer **användare**, och sedan välja en användare. Till exempel _Alain Charon_.

    Den **Alain Charon - profil** visas.

    ![Användarens profilsida, inklusive redigerbar information](media/active-directory-users-profile-azure-portal/user-profile-all-blade.png)

3. Välj **redigera** om du vill lägga till eller uppdatera informationen som ingår i var och en av de tillgängliga avsnitten.

    ![Användarens profilsida som visar redigerbara områden](media/active-directory-users-profile-azure-portal/user-profile-edit.png)

    - **Profilbild.** Välj en miniatyrbild för användarens konto. Den här bilden visas i Azure Active Directory och på användarens personliga sidor, till exempel myapps.microsoft.com-sidan.

    - **Identitet.** Lägg till eller uppdatera en ytterligare identitetsvärdet för användare, till exempel ett gift efternamn. Du kan ange det här namnet oberoende från värden för förnamn och efternamn. Du kan till exempel använda det att inkludera initialer, ett företagsnamn eller ändra de namn som visas. Ett annat exempel är två användare vars namn är ”Chris Green” kan du använda ID-sträng för att ange deras namn till ”Chris B. Green” ”Chris R. Green (Contoso)”.

    - **Jobbinformation.** Lägg till alla jobbrelaterade information, till exempel användarens befattning, avdelning eller manager.

    - **Inställningar.** Bestäm om användaren kan logga in på Azure Active Directory-klient. Du kan också ange globala Användarplats.

    - **Kontaktinformation.** Lägg till alla relevanta kontaktinformation för användaren. Till exempel en gatuadress eller ett mobiltelefonnummer.

    - **Om autentiseringskontakt.** Kontrollera den här informationen för att kontrollera att det finns en aktiv telefonnummer och e-postadress för användaren. Den här informationen används av Azure Active Directory för att se till att du verkligen är användaren vid inloggning. Om autentiseringskontakt kan bara uppdateras genom en global administratör.

4. Välj **Spara**.

    Alla ändringar har sparats för användaren.

    >[!Note]
    >Du måste använda Windows Server Active Directory för att uppdatera identitet, kontaktinformation eller jobbinformation för användare vars auktoritetskälla är Windows Server Active Directory. När du har slutfört uppdateringen måste du vänta tills nästa synkroniseringscykel ska slutföras innan ändringarna visas.

## <a name="next-steps"></a>Nästa steg
När du har uppdaterat dina användares profiler, kan du utföra följande basic-processer:

- [Lägga till eller ta bort användare](add-users-azure-active-directory.md)

- [Tilldela roller till användare](active-directory-users-assign-role-azure-portal.md)

- [Skapa en basgrupp och lägga till medlemmar](active-directory-groups-create-azure-portal.md)

Eller du kan utföra andra uppgifter för användare, till exempel tilldela ombud, med hjälp av principer och dela användarkonton. Läs mer om andra tillgängliga åtgärder, [Azure Active Directory management supportdokumentation](../users-groups-roles/index.yml).
