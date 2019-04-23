---
title: Hur du skyddar åtkomsten till Azure Data Catalog
description: Den här artikeln beskrivs hur du skyddar data catalog och dess datatillgångar.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 6c09b509399647f4cacbc96427200da5a1b00ac9
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60010763"
---
# <a name="how-to-secure-access-to-data-catalog-and-data-assets"></a>Hur du skyddar åtkomsten till data catalog och datatillgångar
> [!IMPORTANT]
> Den här funktionen är endast tillgänglig i Azure Data Catalog standard edition.

Azure Data Catalog kan du ange vem som kan komma åt data catalog och vilka åtgärder (registrera, kommentera, bli ägare) som kan utföras för metadata i katalogen. 

## <a name="catalog-users-and-permissions"></a>Katalogens användare och behörigheter
Ge en användare eller grupp åtkomst till en katalog och ange behörigheter:

1. På den [öppnas startsidan för datakatalogen](https://www.azuredatacatalog.com), klickar du på **inställningar** i verktygsfältet.

    ![data catalog - inställningar](media/data-catalog-how-to-secure-catalog/data-catalog-settings.png)
2. På inställningssidan expanderar den **kataloganvändare** avsnittet.
    ![Catalog-användare – Lägg till](media/data-catalog-how-to-secure-catalog/data-catalog-add-button.png)
3. Klicka på **Lägg till**.
4. Ange det fullständiga **användarnamn** eller namnet på den **säkerhetsgrupp** i den Azure Active Directory (AAD) som är associerade med katalogen. Använd kommatecken (', ') som avgränsare om du lägger till mer än en användare eller grupp.
    ![Kataloganvändare - användare eller grupper](media/data-catalog-how-to-secure-catalog/data-catalog-users-groups.png)
5. Tryck på **RETUR** eller **FLIKEN** utanför textrutan. 
6.  Bekräfta att alla behörigheter (**kommentera**, **registrera**, och **bli ägare**) är tilldelade till dessa användare eller grupper som standard. Det vill säga den användare eller grupp kan [registrera datatillgångar]( data-catalog-how-to-register.md), [kommentera datatillgångar]( data-catalog-how-to-annotate.md), och [överta ägarskapet för datatillgångar]( data-catalog-how-to-manage.md). 
    ![Kataloganvändare - standardbehörigheter](media/data-catalog-how-to-secure-catalog/data-catalog-default-permissions.png)
7.  Om du vill ge en användare eller grupp bara läsbehörighet till katalogen, avmarkera de **kommentera** alternativ för användaren eller gruppen. När du gör det, användare eller grupp kan kommentera datatillgångar i katalogen, men de kan visa dem. 
8.  Om du vill neka en användare eller grupp från att registrera datatillgångar, avmarkera de **registrera** alternativ för användaren eller gruppen.
9.  Om du vill neka en användare från att bli ägare till en datatillgång, avmarkera de **bli ägare** alternativ för användaren eller gruppen. 
10. Om du vill ta bort en användargrupp från katalogens användare, klickar du på **x** för användargruppen längst ned i listan. 
    ![Katalogen användare – ta bort användare](media/data-catalog-how-to-secure-catalog/data-catalog-delete-user.png)

    > [!IMPORTANT]
    > Vi rekommenderar att du lägger till säkerhetsgrupper för att katalogisera användare i stället för att lägga till användare direkt och tilldela behörigheter. Sedan kan lägga till användare i de säkerhetsgrupper som matchar deras roller och deras nödvändiga åtkomst till katalogen.

## <a name="special-considerations"></a>Att tänka på

- De behörigheter som tilldelats säkerhetsgrupper är additiva. Betyder att finns en användare i två grupper. En grupp har kommentera behörigheter och annan grupp inte har kommentera behörigheter. Användaren har sedan kommentera behörigheter. 
- De behörigheter som tilldelats explicit till en användare åsidosätta behörigheter som tilldelats grupper som användaren tillhör. I exemplet ovan exempelvis du uttryckligen har lagt till användare-katalogen och gör att användaren inte tilldela kommentera behörigheter. Användaren kan inte kommentera datatillgångar även om användaren är medlem i en grupp som har kommentera behörigheter.

## <a name="next-steps"></a>Nästa steg
- [Kom igång med Azure Data Catalog](data-catalog-get-started.md)

