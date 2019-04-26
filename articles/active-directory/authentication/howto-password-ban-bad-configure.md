---
title: Hur du förbjuda svaga lösenord i Azure AD - Azure Active Directory
description: Förbjud svaga lösenord från din envirionment med Azure AD som förbjuds dynamiskt passwrords
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7f6dbc869db4a0a444d09a2dc234e171758c706
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60414875"
---
# <a name="configuring-the-custom-banned-password-list"></a>Konfigurera listan över anpassade förbjudna lösenord

Många organisationer hitta användarna skapa lösenord med hjälp av vanliga lokala ord, till exempel en skola, sport eller berömda personen, utan gör dem lätta att gissa. Lista med Microsofts anpassade förbjudna lösenord gör att organisationer kan lägga till strängar för att utvärdera och blockera, förutom den globala förbjuden lista med lösenord, när användare och administratörer försöker ändra och återställa ett lösenord.

## <a name="add-to-the-custom-list"></a>Lägg till den anpassade listan

Konfigurera listan över anpassade förbjudna lösenord kräver en Azure Active Directory Premium P1 eller P2-licens. Mer detaljerad information om Azure Active Directory-licensiering finns i den [sidan med priser för Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). |

1. Logga in på den [Azure-portalen](https://portal.azure.com) och bläddra till **Azure Active Directory**, **autentiseringsmetoder**, sedan **lösenordsskydd**.
1. Ställ in alternativet **tvinga anpassad lista**till **Ja**.
1. Lägg till strängar som ska den **anpassad förbjuden lösenordslista**, en sträng per rad
   * Lista med anpassade förbjudna lösenord kan innehålla upp till 1000 orden.
   * Lista med anpassade förbjudna lösenord inte är skiftlägeskänslig.
   * Lista med anpassade förbjudna lösenord tar hänsyn till vanliga tecken ersättningen.
      * Exempel: ”o” och ”0” eller ”a” och ”\@”
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
