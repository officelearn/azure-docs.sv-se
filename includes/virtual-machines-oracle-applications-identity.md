---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: c1cca99a04e78c40cdd1061e52e49f45a73c73eb
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361562"
---
### <a name="identity-tier"></a>Identitets nivå 

Med Microsoft-Oracle-partnerskapet kan du konfigurera en enhetlig identitet i Azure, OCI och Oracle-programmet. För JD Edwards EnterpriseOne-eller fjärrinstallationsserver-programsviten krävs en instans av Oracle HTTP-servern (OHS) för att konfigurera enkel inloggning mellan Azure AD och Oracle IDCS.

OHS fungerar som en omvänd proxy till program nivån, vilket innebär att alla begär anden till slut programmen går igenom den. Oracle Access Manager webgrind är ett OHS webb server-plugin-program som fångar varje begäran till slut programmet. Om en resurs som nås är skyddad (kräver en autentiserad session) initierar webgrind OIDC-autentiseringsschemat med identitets moln tjänsten via användarens webbläsare. Mer information om de flöden som stöds av OpenID Connect-webgrind finns i [dokumentationen för Oracle Access Manager](https://docs.oracle.com/en/middleware/idm/access-manager/12.2.1.3/aiaag/integrating-webgate-oidc-server.html).

Med den här installationen kan en användare som redan är inloggad i Azure AD navigera till JD Edwards EnterpriseOne eller ett-program utan att logga in igen via Oracle Identity Cloud service. Kunder som distribuerar den här lösningen får fördelarna med enkel inloggning, inklusive en enda uppsättning autentiseringsuppgifter, en förbättrad inloggnings upplevelse, förbättrad säkerhet och minskad support kostnad.

Om du vill veta mer om hur du konfigurerar enkel inloggning för JD Edwards EnterpriseOne eller den andra sidan med Azure AD, se tillhör ande [Oracle-whitepaper](https://cloud.oracle.com/iaas/whitepapers/deploy_peoplesoft_jdedwards_across_oci_azure.pdf).