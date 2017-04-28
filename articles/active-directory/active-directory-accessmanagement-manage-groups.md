---
title: Hantera grupper i Azure Active Directory | Microsoft Docs
description: "Så här skapar och hanterar du grupper för att hantera Azure med Azure Active Directory-användare."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: d1f5451c-3807-423c-8bac-2822d27b893f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/26/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 8f171456f74925eba16ebd6f56ced558a11f9d99
ms.openlocfilehash: 7d2cc99925e01f8135f04f5863f798e13d7413e3
ms.lasthandoff: 02/11/2017


---
# <a name="managing-groups-in-azure-active-directory"></a>Hantera grupper i Azure Active Directory
> [!div class="op_single_selector"]
> * [Azure-portal](active-directory-groups-create-azure-portal.md)
> * [Klassisk Azure-portal](active-directory-accessmanagement-manage-groups.md)
> * [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
>
>

En av användarhanteringsfunktionerna i Azure Active Directory (AD Azure) är möjligheten att skapa grupper av användare. Du kan använda en grupp för att utföra hanteringsuppgifter som att tilldela licenser eller behörigheter till flera användare på en gång. Du kan också använda grupper för att ge behörighet till

* resurser, till exempel objekt i katalogen,
* Resurser som är externa för katalogen, t.ex. SaaS-program, Azure-tjänster, SharePoint-webbplatser eller lokala resurser

En resursägare kan dessutom ge en Azure AD-grupp som ägs av någon annan åtkomst till en resurs. Denna tilldelning ger medlemmarna i den aktuella gruppen åtkomst till resursen. Gruppens ägare hanterar sedan medlemskap i gruppen. I själva verket delegerar resursägaren behörighet till gruppens ägare att tilldela användare till resursen.

## <a name="how-do-i-create-a-group"></a>Hur skapar jag en grupp?
Beroende på vilka tjänster som din organisation prenumererar på, kan du skapa en grupp med något av följande:

* den klassiska Azure-portalen
* Office 365-kontoportalen
* Windows Intune-kontoportalen

Vi kommer att beskriva uppgifter som de genomförs på den klassiska Azure-portalen. Mer information om hur du hanterar din Azure AD-katalog med hjälp av andra portaler än Azure-portalen finns i [Administrera Azure AD-katalogen](active-directory-administer.md).

1. På [den klassiska Azure-portalen](https://manage.windowsazure.com) väljer du först **Active Directory** och sedan namnet på din organisations katalog.
2. Välj fliken **Grupper**.
3. Välj **Lägg till grupp**.
4. I fönstret **Lägg till grupp** anger du namnet och beskrivningen för en grupp.

## <a name="how-do-i-add-or-remove-individual-users-in-a-security-group"></a>Hur lägger jag till eller tar bort enskilda användare i en säkerhetsgrupp?
**Så här lägger du till en enskild användare till en grupp**

1. På [den klassiska Azure-portalen](https://manage.windowsazure.com) väljer du först **Active Directory** och sedan namnet på din organisations katalog.
2. Välj fliken **Grupper**.
3. Öppna den grupp som du vill lägga till medlemmar i. Öppna fliken **Medlemmar** för den valda gruppen, såvida den inte redan visas.
4. Välj **Lägg till medlemmar**.
5. Välj namnet på den användare eller grupp som du vill lägga till som en medlem i den här gruppen på sidan **Lägg till medlemmar**. Kontrollera att det här namnet verkligen läggs till i fönstret **Valda**.

**Så här tar du bort en användare från en grupp**

1. På [den klassiska Azure-portalen](https://manage.windowsazure.com) väljer du först **Active Directory** och sedan namnet på din organisations katalog.
2. Välj fliken **Grupper**.
3. Öppna den grupp som du vill ta bort medlemmar från.
4. Välj fliken **Medlemmar**, välj namnet på medlemmen som du vill ta bort från den här gruppen och klicka sedan på **Ta bort**.
5. Bekräfta att du vill ta bort medlemmen från gruppen när du uppmanas att göra det.

## <a name="how-can-i-manage-the-membership-of-a-group-dynamically"></a>Hur kan jag hantera medlemskapet för en grupp dynamiskt?
I Azure AD kan du enkelt skapa en enkel regel för att avgöra vilka användare som ska vara medlemmar i gruppen. En enkel regel är en regel som bara gör en jämförelse. Om ett SaaS-program t.ex. har tilldelats en grupp, kan du konfigurera en regel som lägger till användare som har befattningen Försäljare. Den här regeln beviljar sedan åtkomst till det här SaaS-programmet för alla användare med denna befattning i katalogen.

När ett attribut för en användare ändras utvärderar systemet alla dynamiska gruppregler i en katalog för att se om attributändringen ska utlösa grupptillägg eller gruppborttagningar. Om användaren uppfyller en regel i en grupp läggs användaren till som medlem i gruppen. Om användaren inte längre uppfyller regeln i en grupp som han eller hon är medlem i tas användaren bort som medlem i gruppen.

> [!NOTE]
> Du kan skapa en regel för dynamiskt medlemskap för säkerhetsgrupper eller Office 365-grupper. Kapslade gruppmedlemskap stöds för närvarande inte för gruppbaserad tilldelning till program.
>
> Dynamiskt medlemskap för grupper kräver en Azure AD Premium-licens som tilldelas till
>
> * administratören som hanterar regeln för en grupp
> * Alla medlemmar i gruppen
>
>

**Så här aktiverar du dynamiskt medlemskap för en grupp**

1. På [den klassiska Azure-portalen](https://manage.windowsazure.com) väljer du först **Active Directory** och sedan namnet på din organisations katalog.
2. Välj fliken **Grupper** och öppna den grupp som du vill redigera.
3. Välj fliken **Konfigurera** och ändra **Aktivera dynamiskt medlemskap** till **Ja**.
4. Skapa en enkel regel för gruppen som avgör hur dynamiskt medlemskap för den här gruppen ska fungera. Kontrollera att alternativet **Lägg till användare där** är valt och välj sedan en användaregenskap i listan (till exempel avdelning, befattning osv.)
5. Välj ett villkor (inte lika med, lika med, börjar inte med, börjar med, innehåller ej, innehåller, matchar inte, matchar).
6. Ange ett jämförelsevärde för den valda användaregenskapen.

Mer information om hur du skapar *avancerade* regler (regler som kan innehålla flera jämförelser) för dynamiska gruppmedlemskap finns i [Använda attribut för att skapa avancerade regler](active-directory-accessmanagement-groups-with-advanced-rules.md).

## <a name="additional-information"></a>Ytterligare information
Dessa artiklar innehåller ytterligare information om Azure Active Directory.

* [Hantera åtkomst till resurser med Azure Active Directory-grupper](active-directory-manage-groups.md)
* [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Artikelindex för programhantering i Azure Active Directory](active-directory-apps-index.md)
* [Vad är Azure Active Directory?](active-directory-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md)

