---
title: "Bästa praxis för villkorlig åtkomst i Azure Active Directory | Microsoft Docs"
description: "Lär dig mer om saker du bör känna till och vad det är bör du undvika detta när du konfigurerar principer för villkorlig åtkomst."
services: active-directory
keywords: "villkorlig åtkomst till appar, villkorlig åtkomst med Azure AD, säker åtkomst till företagets resurser, principer för villkorlig åtkomst"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/16/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 74b97ac263dcc45f7a8dd7461cbdb23d9fd5e6fd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Metodtips för villkorlig åtkomst i Azure Active Directory

Det här avsnittet ger information om saker du bör känna till och vad det är bör du undvika detta när du konfigurerar principer för villkorlig åtkomst. Innan du läser det här avsnittet bör du bekanta dig med begrepp och termer som beskrivs i [villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-azure-portal.md)

## <a name="what-you-should-know"></a>Vad du bör känna till

### <a name="whats-required-to-make-a-policy-work"></a>Vad har krävs för att skapa en princip för att fungera?

När du skapar en ny princip, finns det inga användare, grupper, appar eller åtkomstkontroller som valts.

![Molnappar](./media/active-directory-conditional-access-best-practices/02.png)


Om du vill att din princip för att fungera, måste du konfigurera följande:


|Vad           | Hur                                  | Varför|
|:--            | :--                                  | :-- |
|**Molnappar** |Du måste välja en eller flera appar.  | Målet med en princip för villkorlig åtkomst är så att du kan finjustera hur behöriga användare kan komma åt dina appar.|
| **Användare och grupper** | Du måste välja minst en användare eller grupp som har behörighet att få åtkomst till molnappar som du har valt. | En villkorlig åtkomstprincip som har ingen användare och grupper som har tilldelats, utlöses aldrig. |
| **Åtkomstkontroll** | Du måste välja minst en åtkomstkontroll. | Princip för processorn behöver veta vad du gör om dina villkor är uppfyllda.|


Förutom de grundläggande kraven bör i många fall kan du också konfigurera ett villkor. När en princip fungerar också utan ett konfigurerat villkor, är villkor drivande faktor för att finjustera åtkomst till dina appar.


![Molnappar](./media/active-directory-conditional-access-best-practices/04.png)



### <a name="how-are-assignments-evaluated"></a>Hur utvärderas tilldelningar?

Alla tilldelningar som är logiskt **and**. Om du har mer än en tilldelning som konfigurerats för att utlösa en princip, måste alla tilldelningar vara uppfyllda.  

Om du behöver konfigurera en plats-villkor som gäller för alla anslutningar från utanför företagets nätverk åstadkommer detta genom att:

- Inklusive **alla platser**
- Förutom **alla betrodda IP-adresser**

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>Vad händer om du har principer i den klassiska Azure-portalen och Azure-portalen konfigurerad?  
Båda principerna tillämpas av Azure Active Directory och användaren får åtkomst till endast när alla krav är uppfyllda.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>Vad händer om du har principer i Intune Silverlight-portalen och Azure Portal?
Båda principerna tillämpas av Azure Active Directory och användaren får åtkomst till endast när alla krav är uppfyllda.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>Vad händer om jag har flera principer för samma användare konfigurerade?  
För varje inloggning, Azure Active Directory utvärderar alla principer och garanterar att alla krav är uppfyllda innan du beviljar åtkomst till användaren.


### <a name="does-conditional-access-work-with-exchange-activesync"></a>Fungerar villkorlig åtkomst med Exchange ActiveSync

Ja, du kan använda Exchange ActiveSync i en princip för villkorlig åtkomst.


## <a name="what-you-should-avoid-doing"></a>Vad du bör undvika att göra

Villkorlig åtkomst framework ger dig en utmärkt konfigurationsflexibilitet. Stor flexibilitet innebär dock också att du noggrant läser varje princip för konfiguration innan du släpper den för att undvika oönskade resultat. I den här kontexten bör du vara särskilt uppmärksam på tilldelningar som påverkar fullständiga **alla användare / grupper / molnappar**.

I din miljö bör du undvika följande konfigurationer:


**För alla användare alla molnappar:**

- **Blockera åtkomst** -den här konfigurationen blockerar hela organisationen, som definitivt inte är en bra idé.

- **Kräver en kompatibel enhet** – för användare som inte har registrerat sina enheter än den här principen blockerar all åtkomst, inklusive åtkomst till Intune-portalen. Om du är administratör saknar en registrerad enhet blockerar du från att få tillbaka till Azure portal för att ändra principen i den här principen.

- **Kräva domänanslutning** – den här principen blockera åtkomst har också möjlighet att blockera åtkomst för alla användare i organisationen om du inte har en domänansluten enhet ännu.


**För alla användare, alla molnappar, alla enhetsplattformar:**

- **Blockera åtkomst** -den här konfigurationen blockerar hela organisationen, som definitivt inte är en bra idé.



## <a name="policy-migration"></a>Migrering av princip

Om du har principer i den klassiska Azure-portalen konfigurerats bör du migrera dem till Azure-portalen eftersom:


- En användare som är i en Azure klassiska portal princip och en Azure-portalen princip måste uppfylla kraven i båda principerna 

- Om du inte migrera dina befintliga principer kan du inte implementera principer som du vill bevilja åtkomst


### <a name="migration-from-the-azure-classic-portal"></a>Migrering från den klassiska Azure-portalen

I det här scenariot: 

- I din [klassiska Azure-portalen](https://manage.windowsazure.com), du har konfigurerat:

    - sharepoint online

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-best-practices/14.png)

    - En princip för enhetsbaserad villkorlig åtkomst

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-best-practices/15.png)

- Du vill konfigurera en villkorlig åtkomstprincip för hantering av mobilprogram i Azure-portalen 
 

#### <a name="configuration"></a>Konfiguration 

- Granska dina principer för enhetsbaserad villkorlig åtkomst

- Migrera dem till Azure-portalen 

- Lägg till principer för villkorlig åtkomst för hantering av mobila program


### <a name="migrating-from-intune"></a>Migrera från Intune 

I det här scenariot:

- I [Intune](https://portal.azure.com/#blade/Microsoft_Intune/SummaryBlade ), du har en villkorlig åtkomstprincip för hantering av mobila program för antingen Exchange Online eller SharePoint Online konfigurerad

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-best-practices/15.png)

- Du vill migrera till att använda villkorlig åtkomst för hantering för mobila program i Azure-portalen


#### <a name="configuration"></a>Konfiguration 
 
- Granska dina principer för enhetsbaserad villkorlig åtkomst

- Migrera dem till Azure-portalen 

- Granska villkorlig åtkomst principerna för mobil programhantering konfigurerats för Exchange Online eller SharePoint Online i Intune

- Lägg till kontroll för **kräver godkända applikationer** förutom enhetsbaserad åtkomstkontroll 
 

### <a name="migrating-from-the-azure-classic-portal-and-intune"></a>Migrera från den klassiska Azure-portalen och Intune

I det här scenariot:

- Du har konfigurerat följande:

    - **Klassiska Azure-portalen:** enhetsbaserad villkorlig 

    - **Intune:** principer för villkorlig åtkomst för hantering av mobila program 
    
- Du vill migrera båda principer med principer för villkorlig åtkomst för hantering av mobila program i Azure-portalen


#### <a name="configuration"></a>Konfiguration

- Granska dina principer för enhetsbaserad villkorlig åtkomst

- Migrera dem till Azure-portalen 

- Granska mobila villkorlig åtkomst programhanteringsprincip konfigurerats för Exchange Online eller SharePoint Online i Intune

- Lägg till kontroll för **kräver godkända applikationer** utöver den enhetsbaserad 




## <a name="next-steps"></a>Nästa steg

Om du vill veta hur du konfigurerar en princip för villkorlig åtkomst finns [Kom igång med villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).
