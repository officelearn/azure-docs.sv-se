---
title: Skydda dina personliga data med Azure identitets- och kontroller | Microsoft Docs
description: "Azure identitets- och styr hjälper dig att skydda personliga data och kan vara till hjälp som överensstämmer med allmänna Data Protection förordning (BNPR)"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: fd3beb57cda4993f922fb935263b0e962b38da6a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="azure-active-directory-and-multi-factor-authentication-protect-personal-data-with-identity-and-access-controls"></a>Azure Active Directory och Multi-Factor Authentication: skydda personuppgifter med identitets- och kontroller

Den här artikeln innehåller information och procedurer som du kan använda för att skydda personliga data med Azure Active Directory och Multi-Factor authentication-säkerhetsfunktioner och tjänster. Informationen i den här artikeln kan vara användbart i du ansträngningar för att vara kompatibel med allmänna Data Protection förordning (BNPR).

## <a name="scenario"></a>Scenario

Ett stort kryssning företag, sitt säte i USA utökar åtgärderna för att erbjuda färdvägar i Medelhavet, Adriatiska havet, baltiska havet samt Förenta staterna. För att stödja dessa ansträngningar genererade den flera mindre kryssning rader i Italien, Tyskland, Danmark och Storbritannien 

Företaget använder Microsoft Azure för att lagra företagets data i molnet. Detta inkluderar personligt identifierbar information, till exempel namn, adresser, telefonnummer och kreditkortsinformation av dess globala kundbas. Den innehåller också traditionella personal information, till exempel adresser, telefonnummer, skatt ID-nummer och annan information om företagets anställda på alla platser. Raden kryssning har också en stor databas med medlemmar av trafik och förmåner som innehåller personuppgifter för att spåra relationer med aktuella och tidigare kunder.

Företagets anställda åtkomst till nätverket från företagets fjärranslutna kontor och resa agenter finns runtom i världen har åtkomst till vissa företagsresurser.

## <a name="problem-statement"></a>Problembeskrivning

Företaget måste skydda kundernas och anställdas personliga data från angripare som försöker använda avslöjade identiteter för att få åtkomst. De måste också kontrollera att åtkomsten till behöriga användare personuppgifter är begränsad till endast de som behöver den sköta sitt arbete.

## <a name="company-goal"></a>Företagets mål

Företagets mål är att säkerställa att åtkomst till personliga data är strikt kontrollerad. Det är viktigt att identiteten för användare med åtkomst till personliga data skyddas av stark autentisering. En princip av [minsta privilegium] (https://en.wikipedia.org/wiki/Principle_of_least_privilege) måste vara tvingande så att behöriga användare har bara åtkomst till de behöver och inga fler.

## <a name="solutions"></a>Lösningar

Microsoft Azure tillhandahåller identitets- och hanteringsverktyg för att hjälpa företag styra vem som har åtkomst till resurser som innehåller personuppgifter.

### <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) (AAD) hanterar identiteter och styr åtkomsten till Azure samt andra lokalt och andra molnresurser, data och program. [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access) hjälper administratörer att Azure för att minimera antalet personer som har tillgång till viss information, till exempel personuppgifter. Det gör det enkelt att identifiera, begränsa och övervaka Privilegierade identiteter och deras åtkomst till resurser och tilldela tillfälliga in JIT-administrativ behörighet till behöriga användare. Det ger också inblick i de som har administratörsbehörighet för AAD.

Aktiviteterna som ingår i med hjälp av AAD PIM inkluderar:

- Aktivera Privileged Identity Management för din katalog

- Med Privileged Identity Management admin instrumentpanelen viktig information på ett ögonblick

- Hantera Privilegierade identiteter (administratörer) genom att lägga till eller ta bort permanent eller berättigade administratörer för varje roll

- Konfigurera inställningar för roll-aktivering

- Aktivera roller

- Granska rollen aktivitet

#### <a name="how-do-i-enable-aad-pim"></a>Hur aktiverar AAD PIM?

Du börjar använda PIM för din katalog genom att göra följande:

1. Logga in på Azure-portalen som global administratör för din katalog.

2. Om din organisation har mer än en katalog väljer du ditt användarnamn längst upp till höger på Azure-portalen. Välj den katalog där du ska använda Azure AD Privileged Identity Management.

3. Välj **fler tjänster** och använda den **Filter** textruta för att söka efter Azure AD Privileged Identity Management.

4. Markera **Fäst på instrumentpanelen** och klicka sedan på **Skapa**. Privileged Identity Management-programmet öppnas.

När Azure AD Privileged Identity Management har konfigurerats ser du alltid navigeringsbladet när du öppnar programmet.

![](media/protect-personal-data-identity-access-controls/azure-pim.png)

Mer information och instruktioner för att komma igång med AAD PIM finns [starta med hjälp av Azure AD Privileged Identity Management.](https://docs.microsoft.com/active-directory/active-directory-privileged-identity-management-getting-started)

### <a name="azure-role-based-access-control"></a>Rollbaserad åtkomstkontroll i Azure

[Azure rollbaserad åtkomstkontroll](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) (RBAC) hjälper Azure administratörer att hantera åtkomst till Azure-resurser genom att aktivera att bevilja åtkomst baserat på användarens tilldelade rollen. Du kan särskilja uppgifter i ett team och ge bara mängden åtkomst till användare, grupper och program som de behöver för att utföra sitt arbete.

Rollbaserad åtkomst kan beviljas till användare som använder Azure-portalen, Azure-kommandoradsverktygen eller Azure Management-API:er.

Läs mer om grunderna i Azure RBAC [Kom igång med rollbaserad åtkomstkontroll i Azure Portal.](https://docs.microsoft.com/active-directory/role-based-access-control-what-is)

#### <a name="how-do-i-manage-azure-rbac-with-powershell"></a>Hur hanterar Azure RBAC med PowerShell?

Du kan använda PowerShell-cmdlets för att hantera Azure RBAC, inklusive följande hanteringsuppgifter:

- Lista roller

- Se vem som har åtkomst

- Bevilja åtkomst

- Ta bort åtkomst

- Skapa en anpassad roll

- Hämta åtgärder för en Resursprovider

- Ändra en anpassad roll

- Ta bort en anpassad roll

- Lista över anpassade roller

Instruktioner om hur du hanterar Azure RBAC med PowerShell finns i [Hantera rollbaserad åtkomst med Azure PowerShell](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-powershell).

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

[Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/) (MFA) är en lösning för verifiering av två steg som hjälper dig att skydda åtkomst till data och program, samtidigt som du uppfyller efterfrågan från användarna för en process för enkel inloggning. Den ger stark autentisering via en mängd verifieringsmetoder, inklusive telefonsamtal, textmeddelande eller verifiering av mobilappar.

Om du vill distribuera MFA i Azure-molnet, måste du först aktivera den och sedan aktivera tvåstegsverifiering för användare.

#### <a name="how-do-i-enable-azure-to-use-mfa"></a>Hur aktiverar Azure för att använda MFA?

Om användarna har licenser som innehåller Azure Multi-Factor Authentication, du behöver bara konfigurera Azure MFA på en per användare eller gruppbasis. 

![MFA är aktiverat användare](media/protect-personal-data-identity-access-controls/enable-mfa.png)

Om du för närvarande inte har licenser som du behöver gå igenom processen att avgöra den lämpligaste distributionstypen för ditt scenario. Du kan börja genom att titta i artikeln [välja Azure Multi-Factor Autehntication-lösning för dig](../multi-factor-authentication/multi-factor-authentication-get-started.md). Om du bestämmer dig för att du behöver skapa en Multi-Factor Authentication-server. Du kan starta genom att följa dessa steg:

1. Välj **Active Directory** i Azure-portalen (logga in som administratör).

2. Välj **MFA-Server**

3. Ange ett timeout-värde. 

    ![](media/protect-personal-data-identity-access-controls/mfa-server-settings.png)

4. Klicka på **spara**

I det här fönstret har också möjlighet att hämta MFA-Server. Du kan få ytterligare information om hur du planerar distributionen genom att granska artikeln och storlek [komma igång med Azure Multi-Factor Authentication-server](../multi-factor-authentication/multi-factor-authentication-get-started-server.md)

Mer information om hur du hanterar din leverantör av Multifaktorautent finns [komma igång med en Azure leverantör av Multifaktorautent.](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-auth-provider)

#### <a name="how-do-i-turn-on-two-step-verification-for-users"></a>Hur aktiverar tvåstegsverifiering för användarna?

Du kan tillämpa tvåstegsverifiering för alla inloggningar eller du kan skapa principer för villkorlig åtkomst för att kräva tvåstegsverifiering endast när särskilda villkor gäller.

Om du aktiverar Azure MFA genom att ändra användartillstånd är den traditionella metoden för att kräva tvåstegsverifiering. Alla användare som har samma krav på att utföra tvåstegsverifiering varje gång de loggar in. Aktivera en användare åsidosätter alla principer för villkorlig åtkomst som kan påverka den användaren.

Om du aktiverar Azure MFA med en princip för villkorlig åtkomst är ett mer flexibelt sätt för att kräva tvåstegsverifiering. Du kan skapa principer för villkorlig åtkomst som gäller för grupper som enskilda användare. Hög risk grupper kan få fler begränsningar än låg risk grupper eller tvåstegsverifiering kan krävs endast för hög risk molnappar och hoppades över för de låg risk. Villkorlig åtkomst är dock en betald funktion i Azure Active Directory.

Om du vill aktivera MFA genom att ändra användarens tillstånd, gör du följande:

1. Logga in på Azure portal som administratör.
2. Gå till **Azure Active Directory \> användare och grupper \> alla användare**.
3. Välj **Multifaktorautentisering**.
4. Sök efter den användare som du vill aktivera för Azure MFA. Du kan behöva ändra vyn överst på sidan.
5. Markera rutan bredvid användarens namn.
6. Välj på höger under snabba steg **aktivera**.

   ![](media/protect-personal-data-identity-access-controls/mfa-bulk.png)

7. Bekräfta dina val i popup-fönstret som öppnas.  Användare för vilka MFA är aktiverat uppmanas att registrera sig nästa gång de loggar in.

Om du vill aktivera Azure MFA med en princip för villkorlig åtkomst, gör du följande:

1. Logga in på Azure portal som administratör.

2. Gå till **Azure Active Directory \> villkorlig åtkomst**.

3. Välj **ny princip**.

4. Under **tilldelningar**väljer **användare och grupper**. Använd den **inkludera** och **undanta** flikar för att ange vilka användare och grupper som ska hanteras av principen.

5. Under **tilldelningar**väljer **Molnappar.** Välja att **omfattar alla molnappar**.
6.  Under **åtkomstkontroller**väljer **bevilja**. Välj **kräver Multi-Factor authentication**.
7.  Aktivera **aktiverar principen** till **på** och välj sedan **spara**.

För information om hur du konfigurerar Azure MFA-inställningar för att ställa in bedrägerivarningar, en engångsförbikoppling, använda anpassade röstmeddelanden, konfigurera cachelagring, ange tillförlitliga IP-adresser, skapa applösenord, aktiverar du komma ihåg Multifaktorautentisering för enheter som användare litar på och välj verifieringsmetoderna, se [konfigurera inställningarna för Azure Multi-Factor Authentication.](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next)

## <a name="next-steps"></a>Nästa steg

- [Skydda privilegierad åtkomst i Azure AD](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access)

- [Vanliga frågor om Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-faq)

- [Rollbaserad åtkomstkontroll felsökning](https://docs.microsoft.com/azure/active-directory/role-based-access-control-troubleshooting)

- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)
