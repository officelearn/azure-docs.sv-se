---
title: "Tilldela administratörsroller i Azure Active Directory | Microsoft Docs"
description: "En administratörsroll kan lägga till användare, tilldela administrativa roller, återställa användarlösenord, hantera användarlicenser eller hantera domäner. En användare som är tilldelad en administratörsroll har samma behörigheter för alla molntjänster som din organisation prenumererar på."
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 7fc27e8e-b55f-4194-9b8f-2e95705fb731
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro;
ms.openlocfilehash: 918e1c535ea8779a8aff3d94c6f1ef2cb2a17fd5
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2018
---
# <a name="assigning-administrator-roles-in-azure-active-directory"></a>Tilldela administratörsroller i Azure Active Directory

Du kan ange separata administratörer att hantera olika funktioner med Azure Active Directory (AD Azure). Administratörer kan har åtkomst till olika funktioner i Azure-portalen och, beroende på deras roll, skapa eller redigera användare, tilldela administrativa roller till andra, återställa användarlösenord, hantera användarlicenser och hantera domäner, bland annat. En användare som är tilldelad en administratörsroll har samma behörigheter för alla molntjänster som din organisation prenumererar på, oavsett om du tilldelar rollen i Office 365-portalen eller i Azure-portalen eller med hjälp av Azure AD-modulen för Windows PowerShell.

## <a name="details-about-the-global-administrator-role"></a>Information om rollen global administratör
Den globala administratören har åtkomst till alla administrativa funktioner. Den person som registrerar sig för en Azure-prenumeration har tilldelats rollen global administratör för katalogen som standard. Endast globala administratörer kan tilldela andra administratörsroller.

## <a name="assign-or-remove-administrator-roles"></a>Tilldela eller ta bort administratörsroller
Information om hur du tilldelar administrativa roller till en användare i Azure Active Directory finns [Tilldela administratörsroller i Azure Active Directory användare](active-directory-users-assign-role-azure-portal.md).

## <a name="available-roles"></a>Tillgängliga roller
Följande administratörsroller är tillgängliga:

* **Faktureringsadministratör**: gör inköp, hanterar prenumerationer, hanterar supportärenden och övervakar tjänstens hälsa.

* **Kompatibilitet administratören**: användare med den här rollen ha administrativ behörighet i i Office 365 säkerhet & Efterlevnadscentret och administrationscentret för Exchange. Mer information i ”[om administratörsroller i Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)”.

* **Villkorlig åtkomst administratör**: användare med den här rollen har möjlighet att hantera inställningar för villkorlig åtkomst i Azure Active Directory.
  > [!NOTE]
  > Användaren måste också vara Global administratör om du vill distribuera principen för villkorlig åtkomst i Exchange ActiveSync i Azure.
  
* **CRM-tjänstadministratör**: användare med den här rollen har globala behörigheter i Microsoft CRM Online när tjänsten finns, samt möjlighet att hantera supportärenden och övervakar tjänstens hälsa. Mer information på [om Office 365-administratörsroller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Enhetsadministratörer**: användare med den här rollen blir för lokala datoradministratörer på alla Windows 10-enheter som är anslutna till Azure Active Directory. De har inte behörighet att hantera enheter objekt i Azure Active Directory.

* **Directory läsare**: Detta är en äldre roll som ska tilldelas till program som inte stöder den [medgivande Framework](active-directory-integrating-applications.md). Det ska inte tilldelas alla användare.

* **Synkronisering Katalogkonton**: Använd inte. Den här rollen är tilldelas automatiskt till Azure AD Connect-tjänsten och inte avsedd eller stöd för annan användning.

* **Directory skrivare**: Detta är en äldre roll som ska tilldelas till program som inte stöder den [medgivande Framework](active-directory-integrating-applications.md). Det ska inte tilldelas alla användare.

* **Exchange-tjänstadministratör**: användarna med den här rollen har globala behörigheter i Microsoft Exchange Online när tjänsten finns. Mer information på [om Office 365-administratörsroller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Global administratör / företagsadministratör**: användare med den här rollen har åtkomst till alla administrativa funktioner i Azure Active Directory, samt tjänster som federera till Azure Active Directory som Exchange Online, SharePoint Online och Skype för företag – Online. En global administratör blir den person som registrerar sig för Azure Active Directory-klient. Endast globala administratörer kan tilldela andra administratörsroller. Det kan finnas mer än en global administratör i företaget. Globala administratörer kan återställa lösenordet för alla användare och alla andra administratörer.

  > [!NOTE]
  > Den här rollen identifieras som ”företagets administratör” i Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell. Det är ”Global administratör” i den [Azure-portalen](https://portal.azure.com).
  >
  >

* **Gästen bjuder in**: användare i den här rollen kan hantera Azure Active Directory B2B gästen användaren inbjudningar när inställningen ”medlemmar kan bjuda in” user är inställd på Nej. Mer information om B2B-samarbete när [om i Azure AD B2B-samarbete preview](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Det innehåller inte några andra behörigheter.

* **Administratören för information Protection**: användare med den här rollen kan komma åt Azure Information Protection i Azure-portalen. De kan konfigurera etiketter för Azure Information Protection-principen, hantera skydd mallar och aktivera skydd.

* **Intune-tjänstadministratören**: användarna med den här rollen har globala behörigheter i Microsoft Intune Online när tjänsten finns. Dessutom innehåller den här rollen kan hantera användare och enheter för att associera principen, samt skapa och hantera grupper.

* **Postlåda administratören**: den här rollen används endast som en del av Exchange Online e-postsupport för RIM Blackberry-enheter. Om din organisation inte använder Exchange Online-epost på RIM Blackberry-enheter, Använd inte den här rollen.

* **Samarbeta stöd för nivå 1**: Använd inte. Den här rollen är inaktuell och kommer att tas bort från Azure AD i framtiden. Den här rollen är avsedd för användning av ett litet antal Microsoft-partner återförsäljning och är inte avsedd för allmänt bruk.

* **Samarbeta stöd för nivå 2**: Använd inte. Den här rollen är inaktuell och kommer att tas bort från Azure AD i framtiden. Den här rollen är avsedd för användning av ett litet antal Microsoft-partner återförsäljning och är inte avsedd för allmänt bruk.

* **Lösenord för administratör / supportavdelning administratören**: användare med den här rollen kan ändra lösenord, hanterar tjänstbegäranden och övervakar tjänstens hälsa. Supportavdelningen administratörer kan endast ändra lösenord för användare och andra supportavdelningen administratörer. 

  > [!NOTE]
  > Den här rollen identifieras som ”supportavdelning administratör” i Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell. Det är ”Lösenordsadministratör” i den [Azure-portalen](https://portal.azure.com/).
  >
  >
  
* **Power BI tjänstadministratör**: användare med den här rollen har globala behörigheter i Microsoft Power BI när tjänsten är tillgänglig, samt möjlighet att hantera supportärenden och övervakar tjänstens hälsa. Mer information på [om Office 365-administratörsroller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d?ui=en-US&rs=en-001&ad=US).

* **Privilegierade rollen Administratör**: användare med den här rollen kan hantera rolltilldelningar i Azure Active Directory, såväl i Azure AD Privileged Identity Management. Den här rollen kan dessutom hantering av alla aspekter av Privileged Identity Management.

* **Rapporterar Reader**: användare med den här rollen kan visa rapport data och rapporter instrumentpanel i administrationscentret för Office 365 och införande kontexten hanteringspaket i PowerBI. Dessutom rollen ger åtkomst till inloggning rapporter och aktivitet i Azure AD och rapportering av data som returneras av Microsoft Graph API. En användare som tilldelats rollen Läsare rapporter kan komma åt endast relevanta användning och införande mått. De har inte någon administratörsbehörigheter för att konfigurera inställningar eller produkten specifika administrationscentret som Exchange åtkomst. 

* **Säkerhetsadministratör**: användare med den här rollen har alla läsbehörighet reader säkerhetsrollen plus möjligheten att hantera konfigurationen för säkerhetsrelaterade services: Azure Active Directory-identitetsskydd, Azure Information Protection, Privileged Identity Management och Office 365-säkerhet och efterlevnad Center. Mer information om Office 365 behörigheter finns på [behörigheter i Office 365 säkerhets- och Efterlevnadscentret](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

* **Säkerhet Reader**: användare med den här rollen har globala skrivskyddad åtkomst, inklusive alla uppgifter i Azure Active Directory, identitetsskydd, Privileged Identity Management, samt möjlighet att läsa Azure Active Directory-inloggning rapporter och granskningsloggar. Rollen ger även skrivskyddad behörighet i Office 365-säkerhet och efterlevnad Center. Mer information om Office 365 behörigheter finns på [behörigheter i Office 365 säkerhets- och Efterlevnadscentret](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

* **Stöd för tjänstadministratör**: användare med den här rollen kan öppna supportärenden med Microsoft för Azure och Office 365-tjänster och vyer som tjänsten instrumentpanelen och meddelandet center i Azure-portalen och administrationsportalen för Office 365. Mer information på [om Office 365-administratörsroller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **SharePoint-tjänstadministratör**: användare med den här rollen har globala behörigheter i Microsoft SharePoint Online när tjänsten finns, samt möjlighet att hantera supportärenden och övervakar tjänstens hälsa. Mer information på [om Office 365-administratörsroller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Skype för företag / Lync tjänstadministratör**: användare med den här rollen har globala behörigheter i Microsoft Skype för företag, om tjänsten finns, samt hantera Skype-specifika användarattribut i Azure Active Directory. Dessutom ger den här rollen kan hantera supportärenden och övervakar tjänstens hälsa. Mer information på [om Office 365-administratörsroller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

  > [!NOTE]
  > Den här rollen identifieras som ”Lync tjänstadministratör” i Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell. Det är ”Skype för företag tjänstadministratör” i den [Azure-portalen](https://portal.azure.com/).
  >
  >

* **Användaren kontoadministratör**: användare med den här rollen kan skapa och hantera alla aspekter av användare och grupper. Dessutom den här rollen omfattar möjligheten att hantera supportärenden och övervakar tjänsten hälsa. Vissa begränsningar gäller. Den här rollen tillåter exempelvis inte att ta bort en global administratör. Kontoadministratörer användare kan ändra lösenord för användare, supportavdelning administratörer och andra användarkonto administratörer.

## <a name="administrator-permissions"></a>Administratörsbehörighet

### <a name="billing-administrator"></a>Faktureringsadministratör

| Kan göra | Det går inte att göra |
| --- | --- |
|<p>Visa information om företagets och användare</p><p>Hantera supportärenden för Office</p><p>Utföra fakturerings- och köp åtgärder för Office-produkter</p> |<p>Återställa användarlösenord</p><p>Skapa och hantera användarvyer</p><p>Skapa, redigera, ta bort användare och grupper och hantera användarlicenser</p><p>Hantera domäner</p><p>Hantera information om företaget</p><p>Delegera administrativa roller till andra</p><p>Använda katalogsynkronisering</p><p>Visa granskningsloggar</p>|

### <a name="conditional-access-administrator"></a>Villkorlig åtkomst administratör

| Kan göra | Det går inte att göra |
| --- | --- |
|<p>Visa information om företagets och användare</p><p>Hantera inställningar för villkorlig åtkomst</p> |<p>Återställa användarlösenord</p><p>Skapa och hantera användarvyer</p><p>Skapa, redigera, ta bort användare och grupper och hantera användarlicenser</p><p>Hantera domäner</p><p>Hantera information om företaget</p><p>Delegera administrativa roller till andra</p><p>Använda katalogsynkronisering</p><p>Visa granskningsloggar</p>|

### <a name="global-administrator"></a>Global administratör
| Kan göra | Det går inte att göra |
| --- | --- |
|<p>Visa information om företagets och användare</p><p>Hantera supportärenden för Office</p><p>Utföra fakturerings- och köp åtgärder för Office-produkter</p><p>Återställa användarlösenord</p><p>Återställa andra administratörslösenord</p> <p>Skapa och hantera användarvyer</p><p>Skapa, redigera, ta bort användare och grupper och hantera användarlicenser</p><p>Hantera domäner</p><p>Hantera information om företaget</p><p>Delegera administrativa roller till andra</p><p>Använda katalogsynkronisering</p><p>Aktivera eller inaktivera multifaktorautentisering</p><p>Visa granskningsloggar</p> |Gäller inte |

### <a name="password-administrator--helpdesk-administrator"></a>För lösenordsadministratör / supportavdelning administratör
| Kan göra | Det går inte att göra |
| --- | --- |
| <p>Visa information om företagets och användare</p><p>Hantera supportärenden för Office</p><p>Ändra lösenord för användare och andra supportavdelningen administratörer</p>|<p>Utföra fakturerings- och köp åtgärder för Office-produkter</p><p>Skapa och hantera användarvyer</p><p>Skapa, redigera, ta bort användare och grupper och hantera användarlicenser</p><p>Hantera domäner</p><p>Hantera information om företaget</p><p>Delegera administrativa roller till andra</p><p>Använda katalogsynkronisering</p><p>Visa rapporter</p>|

### <a name="information-protection-administrator"></a>Administratören för information Protection 
i | Kan göra
-------- | ---------
Azure Information Protection | * Konfigurera principer för global, begränsade och etiketter<br>* Hantera, konfigurera, uppdatera mallar för skydd<br>* Skydd aktivera för användning 
Privileged Identity Management | * Läsbehörighet<br>* Kan inte hantera medlemskap i Azure AD-roller eller inställningar.
 
### <a name="reports-reader"></a>Rapporter läsare 
Kan göra | Det går inte att göra
------ | ----------
Visa Azure AD-inloggning rapporter och granskningsloggar<br>Visa information om företagets och användare<br>Instrumentpanelen för användning av Office 365 | Skapa och hantera användarvyer<br>Skapa, redigera, ta bort användare och grupper och hantera användarlicenser<br>Delegera administrativa roller till andra<br>Hantera information om företaget

### <a name="security-reader"></a>Säkerhetsläsare
| i | Kan göra |
| --- | --- |
| Identity Protection Center  |Läsa alla säkerhetsrapporter och inställningsinformation om för säkerhet<ul><li>Skräppost<li>Kryptering<li>Skydd mot dataförlust<li>Skadlig kod<li>Avancerat skydd<li>Mot nätfiske<li>Mailflow regler |
| Privileged Identity Management |<p>Har skrivskyddad åtkomst till all information som angetts i Azure AD PIM: principer och rapporter för Azure AD-rolltilldelningar säkerhet granskar och i framtiden skrivskyddad åtkomst till principdata och rapporter för scenarier utöver Azure AD-rolltilldelning.<p>**Det går inte att** registrera dig för Azure AD PIM eller göra några ändringar. PIMS portalen eller via PowerShell kan någon i den här rollen aktivera ytterligare roller (till exempel Global administratör eller Privilegierade rollen Administratör), om användaren är en kandidat för dem. |
| <p>Tjänstens hälsa för Övervakare för Office 365</p><p>Office 365-säkerhet och efterlevnad Center</p> |<ul><li>Läsa och hantera aviseringar<li>Läs säkerhetsprinciper<li>Läsa hotinformation, Cloud App Discovery och karantän i Sök- och undersök<li>Läsa alla rapporter |

### <a name="security-administrator"></a>Säkerhetsadministratör
| i | Kan göra |
| --- | --- |
| Identity Protection Center  |<ul><li>Alla behörigheter för rollen säkerhet läsare.<li>Dessutom kan utföra alla åtgärder för IPC förutom återställa lösenord. |
| Privileged Identity Management |<ul><li>Alla behörigheter för rollen säkerhet läsare.<li>**Det går inte att** Hantera medlemskap i Azure AD-roller eller inställningar. |
| <p>Tjänstens hälsa för Övervakare för Office 365</p><p>Office 365-säkerhet och efterlevnad Center |<ul><li>Alla behörigheter för rollen säkerhet läsare.<li>Kan konfigurera alla inställningar i funktionen Advanced Threat Protection (skydd mot skadlig kod och virus, skadliga URL-config, URL-spårning, etc.). |

### <a name="service-administrator"></a>Tjänstadministratör
| Kan göra | Det går inte att göra |
| --- | --- |
| <p>Visa information om företagets och användare</p><p>Hantera supportärenden för Office</p> |<p>Återställa användarlösenord</p><p>Utföra fakturerings- och köp åtgärder för Office-produkter</p><p>Skapa och hantera användarvyer</p><p>Skapa, redigera, ta bort användare och grupper och hantera användarlicenser</p><p>Hantera domäner</p><p>Hantera information om företaget</p><p>Delegera administrativa roller till andra</p><p>Använda katalogsynkronisering</p><p>Visa granskningsloggar</p> |

### <a name="user-account-administrator"></a>Kontoadministratör för användaren
| Kan göra | Det går inte att göra |
| --- | --- |
| <p>Visa information om företagets och användare</p><p>Hantera supportärenden för Office</p><p>Ändra lösenord för användare, supportavdelning administratörer och andra användarkonto administratörer</p><p>Skapa och hantera användarvyer</p><p>Skapa, redigera, ta bort användare och grupper och hantera användarlicenser med begränsningar. Han eller hon kan inte ta bort en global administratör eller skapa andra administratörer.</p> |<p>Utföra fakturerings- och köp åtgärder för Office-produkter</p><p>Hantera domäner</p><p>Hantera information om företaget</p><p>Delegera administrativa roller till andra</p><p>Använda katalogsynkronisering</p><p>Aktivera eller inaktivera multifaktorautentisering</p><p>Visa granskningsloggar</p> |

### <a name="to-add-a-colleague-as-a-global-administrator"></a>Att lägga till en kollega som global administratör

1. Logga in på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com) med ett konto som är en global administratör för klientkatalogen.

   ![Öppna administrationscentret för azure AD](./media/active-directory-assign-admin-roles-azure-portal/active-directory-admin-center.png)

2. Välj **användare och grupper &gt; alla användare**

3. Sök efter den användare som du vill beteckna som en global administratör och öppna bladet för den användaren.

4. På bladet användare väljer **Directory rollen**.
 
5. På bladet directory roll väljer du den **Global administratör** roll, och spara.

## <a name="deprecated-roles"></a>Föråldrad roller

Följande roller ska inte användas. De är föråldrad och kommer att tas bort från Azure AD i framtiden.

* Ad hoc-licensadministratör
* E-postverifierad användarskapare
* Anslut till en enhet
* Enhetshanterare
* Användare av enheter
* Arbetsplatsenhetskoppling

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du ändrar administratörer för en Azure-prenumeration finns i [Lägga till eller ändra Azure-administratörsroller](../billing-add-change-azure-subscription-administrator.md)
* Mer information om hur resursåtkomsten hanteras i Microsoft Azure finns i [Förstå resursåtkomst i Azure](active-directory-understanding-resource-access.md)
* Mer information om hur Azure Active Directory relaterar till din Azure-prenumeration finns [hur Azure-prenumerationer är associerade med Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Hantera användare](active-directory-create-users.md)
* [Hantera lösenord](active-directory-manage-passwords.md)
* [Hantera grupper](active-directory-manage-groups.md)
