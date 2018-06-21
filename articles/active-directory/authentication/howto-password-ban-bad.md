---
title: Så här förbjuda lösenord i Azure AD
description: Förbjuda svaga lösenord från din envirionment med Azure AD dynamiskt förbjudna passwrords
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: rogoya
ms.openlocfilehash: 0c1517f94d4a6d59077b62614eec8fef665b1529
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296143"
---
# <a name="configuring-the-custom-banned-password-list"></a>Konfigurera lösenordslistan med anpassade förbjudna

|     |
| --- |
| Azure AD-lösenordsskydd och lösenordslistan över anpassade förbjudna är förhandsversion funktioner i Azure Active Directory. Läs mer om förhandsgranskningar [kompletterande användningsvillkor för Microsoft Azure-förhandsgranskningar](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Många organisationer hitta användarna skapar lösenord med vanliga lokala ord som en skola, lag eller berömda person som lämnar dem lätta att gissa. Lista med Microsofts anpassade förbjudna lösenord gör att organisationer kan lägga till strängar för att utvärdera och blockera, utöver den globala förbjudna lista med lösenord, när användare och administratörer försöker ändra eller återställa ett lösenord.

## <a name="add-to-the-custom-list"></a>Lägg till i listan över anpassade

Konfigurera lösenordslistan med anpassade förbjudna kräver en licens för Azure Active Directory Premium P1 eller P2. Mer detaljerad information om Azure Active Directory-licensiering finns i [sida med priser för Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). |

1. Logga in på den [Azure-portalen](https://portal.azure.com) och bläddra till **Azure Active Directory**, **autentiseringsmetoder**, sedan **lösenordsskydd (förhandsgranskning)**.
1. Ange alternativet **framtvinga anpassad lista**, **Ja**.
1. Lägg till strängar som den **anpassad förbjudna lista med lösenord**, en sträng per rad
   * Lösenordslistan över anpassade förbjudna kan innehålla upp till 1000-ord.
   * Lösenordslistan över anpassade förbjudna är skiftlägeskänsliga.
   * Lösenordslistan över anpassade förbjudna anser vanliga Teckenersättning.
      * Exempel: ”o” och ”0” eller ”a” och ”@”
   * Minsta stränglängd är fyra tecken och det maximala antalet är 16 tecken.
1. När du har lagt till alla strängar, klickar du på **spara**.

> [!NOTE]
> Det kan ta flera timmar efter uppdateringar i listan med anpassade förbjudna lösenord som ska användas.

![Ändra listan anpassade förbjudna lösenord under autentiseringsmetoder i Azure-portalen](./media/howto-password-ban-bad/authentication-methods-password-protection.png)

## <a name="how-it-works"></a>Hur det fungerar

Varje gång en användare eller administratör återställer eller ändrar en Azure AD-lösenord som den förs vidare via listorna förbjudna lösenord för att bekräfta att det inte är i en lista. Den här kontrollen ingår i eventuella lösenord anges eller ändras med hjälp av Azure AD.

## <a name="what-do-users-see"></a>Vad ser användarna

När en användare försöker att återställa ett lösenord till något som skulle vara förbjuden, visas följande felmeddelande:

Lösenordet innehåller tyvärr ett ord, en fras eller en mönster som gör att enkelt fjärråtkomstlösning ditt lösenord. Försök igen med ett annat lösenord.

## <a name="next-steps"></a>Nästa steg

[Översikt över listorna förbjudna lösenord](concept-password-ban-bad.md)

[Översikt över Azure AD-lösenordsskydd](concept-password-ban-bad-on-premises.md)

[Aktivera lokal integrering med listorna förbjudna lösenord](howto-password-ban-bad-on-premises.md)
