---
title: Så här förbjuder du svaga lösen ord i Azure AD – Azure Active Directory
description: Förbjuda svaga lösen ord från din envirionment med Azure AD-blockerad passwrords
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb47b9df51803c76662b5fb4ca1fe23740e7af9a
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2020
ms.locfileid: "76155068"
---
# <a name="configuring-the-custom-banned-password-list"></a>Konfigurera listan anpassat blockerade lösen ord

Många organisationer hittar sina användare att skapa lösen ord med hjälp av vanliga lokala ord som skolan, idrotts lag eller berömda person, vilket gör det lätt att gissa sig. Med Microsofts anpassade lista över blockerade lösen ord kan organisationer lägga till strängar som ska utvärderas och blockeras, förutom listan globalt blockerade lösen ord, när användare och administratörer försöker ändra eller återställa ett lösen ord.

## <a name="add-to-the-custom-list"></a>Lägg till i den anpassade listan

Att konfigurera den anpassade listan över blockerade lösen ord kräver en Azure Active Directory Premium P1-eller P2-licens. Mer detaljerad information om Azure Active Directory-licensiering finns på [sidan för Azure Active Directory priser](https://azure.microsoft.com/pricing/details/active-directory/).

1. Logga in på [Azure Portal](https://portal.azure.com) och bläddra till **Azure Active Directory** > **säkerhets** > **autentiseringsmetoder** > **lösen ords skydd**.
1. Ange alternativet **tvinga anpassad lista**till **Ja**.
1. Lägg till strängar i **listan med anpassade förbjudna lösen ord**, en sträng per rad
   * Listan med anpassade förbjudna lösen ord kan innehålla upp till 1000 villkor.
   * Den anpassade listan över blockerade lösen ord är Skift läges okänslig.
   * Listan med anpassade förbjudna lösen ord ser ut som vanlig tecken ersättning.
      * Exempel: "o" och "0", "a" och "\@"
   * Den minsta sträng längden är fyra tecken och Max värdet är 16 tecken.
1. När du har lagt till alla strängar klickar du på **Spara**.

> [!NOTE]
> Det kan ta flera timmar att uppdatera listan med anpassade förbjudna lösen ord som ska användas.

> [!NOTE]
> Listan med anpassade förbjudna lösen ord är begränsad till högst 1000 villkor. Den är inte utformad för att blockera extremt stora listor med lösen ord. För att helt kunna utnyttja fördelarna med den anpassade listan över blockerade lösen ord rekommenderar Microsoft att du först granskar och förstår den avsedda utformningen och användningen av listan med anpassade förbjudna lösen ord (se [anpassad lista över förbjudna lösen](concept-password-ban-bad.md#custom-banned-password-list)ord) och även algoritmen för lösen ords utvärdering (se [hur är lösen ord utvärderas](concept-password-ban-bad.md#how-are-passwords-evaluated)).

![Ändra listan med anpassade förbjudna lösen ord under autentiseringsmetoder i Azure Portal](./media/howto-password-ban-bad/authentication-methods-password-protection.png)

## <a name="how-it-works"></a>Så här fungerar det

Varje gången en användare eller administratör återställer eller ändrar ett Azure AD-lösenord, flödar den genom listorna över blockerade lösen ord för att bekräfta att den inte finns i en lista. Den här kontrollen ingår i alla lösen ord som anges eller ändras med Azure AD.

## <a name="what-do-users-see"></a>Vad ser användarna

När en användare försöker återställa ett lösen ord till något som ska blockeras, visas något av följande fel meddelanden:

* Ditt lösen ord innehåller tyvärr ett ord, en fras eller ett mönster som gör ditt lösen ord lätt att gissa. Försök igen med ett annat lösen ord.
* Tyvärr kan du inte använda lösen ordet eftersom det innehåller ord eller tecken som har blockerats av administratören. Försök igen med ett annat lösen ord.

## <a name="next-steps"></a>Nästa steg

[Konceptuell översikt över listor över förbjudna lösen ord](concept-password-ban-bad.md)

[Konceptuell översikt över lösen ords skydd i Azure AD](concept-password-ban-bad-on-premises.md)

[Aktivera lokal integrering med listorna över förbjudna lösen ord](howto-password-ban-bad-on-premises.md)
