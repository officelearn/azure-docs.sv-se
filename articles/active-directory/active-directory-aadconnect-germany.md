---
title: Azure AD Connect i Microsoft Cloud Tyskland
description: Azure AD Connect integrerar dina lokala kataloger med Azure Active Directory. På så sätt kan du tillhandahålla en gemensam identitet för Office 365-, Azure- och SaaS-program som är integrerade med Azure AD.
keywords: introduktion till Azure AD Connect, översikt över Azure AD Connect, vad är Azure AD Connect, installera Active Directory, Tyskland, Schwarzwald
services: active-directory
documentationcenter: ''
author: billmath
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/08/2016
ms.author: billmath

---
# Azure AD Connect i Microsoft Cloud Tyskland –offentlig förhandsversion
## Introduktion
Azure AD Connect tillhandahåller synkronisering mellan din lokala Active Directory och Azure Active Directory.
För närvarande måste många scenarier i [Microsoft Cloud Tyskland](https://www.microsoft.com/de-de/cloud/deutschland/default.aspx) utföras av operatören. När du använder Microsoft Cloud Tyskland måste du vara medveten om följande:

* Följande URL: er måste vara öppna på en proxyserver för att synkroniseringen ska lyckas:
  
  * *.microsoftonline.de
  * *.windows.net
  * * Listor över återkallade certifikat
* När du loggar in på din Azure AD-katalog måste du använda ett konto i domänen onmicrosoft.de.
* Följande funktioner är inte tillgängliga:
  * Azure AD Connect Health
  * Automatiska uppdateringar
  * Tillbakaskrivning av lösenord

## Ladda ned
Du kan hämta Azure AD Connect från Azure AD Connect-bladet på portalen.  Följ anvisningarna nedan för att hitta Azure AD Connect-bladet.

### Azure AD Connect-bladet
När du har loggat in på Azure-portalen gör du följande:

1. Gå till Bläddra
2. Välj Azure Active Directory
3. Välj sedan Azure AD Connect

Du bör se följande:

![Azure AD Connect-bladet](media\\active-directory-aadconnect-germany\\germany1.png)

I följande tabell beskrivs de funktioner som visas på bladet.

| Rubrik | Beskrivning |
| --- | --- |
| SYNKRONISERINGSSTATUS |Visar om synkronisering är aktiverat eller inaktiverat. |
| SENASTE SYNKRONISERING |Senast tidpunkten då en lyckad synkronisering slutfördes. |
| FEDERERADE DOMÄNER |Visar antalet federerade domäner som är konfigurerade för tillfället. |

## Installation
Du kan använda dokumentationen [här](active-directory-aadconnect.md#install-azure-ad-connect) för att installera Azure AD Connect.

## Avancerade funktioner och ytterligare information
För ytterligare information och riktlinjer för anpassade inställningar eller avancerade konfigurationer kan du börja med [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).  Den här sidan innehåller information och länkar till ytterligare vägledning.

<!--HONumber=Sep16_HO3-->


