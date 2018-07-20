---
title: Hur du förbjuda lösenord i Azure AD
description: Förbjud svaga lösenord från din envirionment med Azure AD som förbjuds dynamiskt passwrords
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: rogoya
ms.openlocfilehash: 30d8260d78b3b46a9f4caea63f6bed818935a9a1
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2018
ms.locfileid: "39158751"
---
# <a name="configuring-the-custom-banned-password-list"></a>Konfigurera listan över anpassade förbjudna lösenord

|     |
| --- |
| Azure AD-lösenordsskydd och listan över anpassade förbjudna lösenord är funktioner i offentlig förhandsversion av Azure Active Directory. Mer information om förhandsversioner finns [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Många organisationer hitta användarna skapa lösenord med hjälp av vanliga lokala ord, till exempel en skola, sport eller berömda personen, utan gör dem lätta att gissa. Lista med Microsofts anpassade förbjudna lösenord gör att organisationer kan lägga till strängar för att utvärdera och blockera, förutom den globala förbjuden lista med lösenord, när användare och administratörer försöker ändra och återställa ett lösenord.

## <a name="add-to-the-custom-list"></a>Lägg till den anpassade listan

Konfigurera listan över anpassade förbjudna lösenord kräver en Azure Active Directory Premium P1 eller P2-licens. Mer detaljerad information om Azure Active Directory-licensiering finns i den [sidan med priser för Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). |

1. Logga in på den [Azure-portalen](https://portal.azure.com) och bläddra till **Azure Active Directory**, **autentiseringsmetoder**, sedan **lösenordsskydd (förhandsversion)**.
1. Ställ in alternativet **tvinga anpassad lista**till **Ja**.
1. Lägg till strängar som ska den **anpassad förbjuden lösenordslista**, en sträng per rad
   * Lista med anpassade förbjudna lösenord kan innehålla upp till 1000 orden.
   * Lista med anpassade förbjudna lösenord inte är skiftlägeskänslig.
   * Lista med anpassade förbjudna lösenord tar hänsyn till vanliga tecken ersättningen.
      * Exempel: ”o” och ”0” eller ”a” och ”@”
   * Minsta stränglängd är fyra tecken och Max är 16 tecken.
1. När du har lagt till alla strängar, klickar du på **spara**.

> [!NOTE]
> Det kan ta flera timmar efter uppdateringar i listan anpassade förbjudna lösenord som ska användas.

![Ändra den anpassade lista med förbjudna lösenord under autentiseringsmetoder i Azure portal](./media/howto-password-ban-bad/authentication-methods-password-protection.png)

## <a name="how-it-works"></a>Hur det fungerar

Varje gång en användare eller administratör återställer eller ändrar en Azure AD-lösenord som den förs vidare via listor med förbjudna lösenord och bekräfta att den inte på en lista. Den här kontrollen ingår i eventuella lösenord anges eller ändras med hjälp av Azure AD.

## <a name="what-do-users-see"></a>Vad ser användarna

När en användare försöker att återställa ett lösenord till något som skulle vara förbjuden, visas följande felmeddelande visas:

Tyvärr innehåller ditt lösenord ett ord, en fras eller ett mönster som gör det enkelt att gissa ditt lösenord. Försök igen med ett annat lösenord.

## <a name="next-steps"></a>Nästa steg

[Översikt över listor med förbjudna lösenord](concept-password-ban-bad.md)

[Översikt över Azure AD-lösenordsskydd](concept-password-ban-bad-on-premises.md)

[Aktivera lokal integrering med listor med förbjudna lösenord](howto-password-ban-bad-on-premises.md)
