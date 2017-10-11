---
title: "Så här säkrar du åtkomst till Azure Data Catalog | Microsoft Docs"
description: "Den här artikeln beskrivs hur data catalog och dess datatillgångar."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/17/2017
ms.author: maroche
ms.openlocfilehash: 9664a7bc8493b08c8e0797ac6f1b212079829833
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="how-to-secure-access-to-data-catalog-and-data-assets"></a>Så här säkrar du åtkomst till data catalog och datatillgångar
> [!IMPORTANT]
> Den här funktionen är endast tillgänglig i standard edition av Azure Data Catalog.

Azure Data Catalog kan du ange vem som kan komma åt data catalog och vilka åtgärder (registrera, lägga till anteckningar, bli ägare) som kan utföras för metadata i katalogen. 

## <a name="catalog-users-and-permissions"></a>Katalogens användare och behörigheter
Ge en användare eller grupp åtkomst till en datakatalog och ange behörigheter:

1. På den [startsidan i data catalog](http://www.azuredatacatalog.com), klickar du på **inställningar** i verktygsfältet.

    ![data catalog – inställningar](media/data-catalog-how-to-secure-catalog/data-catalog-settings.png)
2. På inställningssidan expanderar den **katalogens användare** avsnitt.
    ![Katalogen användare – Lägg till](media/data-catalog-how-to-secure-catalog/data-catalog-add-button.png)
3. Klicka på **Lägg till**.
4. Ange den fullständiga **användarnamn** eller namnet på den **säkerhetsgrupp** i i Azure Active Directory (AAD) som är kopplade till katalogen. Kommatecken (', ') som avgränsare om du lägger till fler än en användare eller grupp.
    ![Katalogens användare - användare eller grupper](media/data-catalog-how-to-secure-catalog/data-catalog-users-groups.png)
5. Tryck på **RETUR** eller **FLIKEN** utanför textrutan. 
6.  Bekräfta att alla behörigheter (**anteckna**, **registrera**, och **bli ägare**) är tilldelade till dessa användare eller grupper som standard. Det vill säga den användare eller grupp kan [registrera datatillgångar]( data-catalog-how-to-register.md), [kommenterar du datatillgångar]( data-catalog-how-to-annotate.md), och [överta ägarskapet för datatillgångar]( data-catalog-how-to-manage.md). 
    ![Katalogens användare - standardbehörigheter](media/data-catalog-how-to-secure-catalog/data-catalog-default-permissions.png)
7.  Om du vill ge en användare eller grupp bara läsbehörighet till katalogen, avmarkera den **kommentera** alternativ för användaren eller gruppen. När du gör det, användare eller grupp kan inte kommenterar du datatillgångar i katalogen, men de kan visa dem. 
8.  Om du vill neka en användare eller grupp registrerar datatillgångar, avmarkera den **registrera** alternativ för användaren eller gruppen.
9.  Om du vill neka en användare från att bli ägare till en datatillgång, avmarkera den **bli ägare** alternativ för användaren eller gruppen. 
10. Om du vill ta bort en användargrupp från katalogens användare klickar du på **x** för användargruppen längst ned i listan. 
    ![Katalogen användare – ta bort användaren](media/data-catalog-how-to-secure-catalog/data-catalog-delete-user.png)

    > [!IMPORTANT]
    > Vi rekommenderar att du lägger till säkerhetsgrupper för att direkt-katalogen användare i stället för att lägga till användare och tilldela behörigheter. Sedan kan lägga till användare på de säkerhetsgrupper som matchar deras roller och deras nödvändiga åtkomst till katalogen.

## <a name="special-considerations"></a>Speciella överväganden

- Behörigheter som tilldelats säkerhetsgrupper är additiva. En användare finns, i två grupper. En grupp har kommentera behörigheter och andra grupper inte har kommentera behörigheter. Användaren har sedan kommentera behörigheter. 
- Behörigheter som uttryckligen tilldelas en användare åsidosätta behörigheter till grupper som användaren tillhör. I föregående exempel, exempelvis du uttryckligen har lagt till katalogen användare och gör att användaren inte tilldela kommentera behörigheter. Användaren kan inte lägga till anteckningar datatillgångar trots att användaren är medlem i en grupp som har kommentera behörigheter.

## <a name="next-steps"></a>Nästa steg
- [Kom igång med Azure Data Catalog](data-catalog-get-started.md)

