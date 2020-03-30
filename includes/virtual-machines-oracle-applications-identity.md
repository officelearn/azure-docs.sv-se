---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: c1cca99a04e78c40cdd1061e52e49f45a73c73eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68361562"
---
### <a name="identity-tier"></a>Identitetsnivå 

Med Microsoft-Oracle-partnerskapet kan du konfigurera en enhetlig identitet i Azure, OCI och ditt Oracle-program. För JD Edwards EnterpriseOne eller PeopleSoft-programpaketet behövs en instans av Oracle HTTP Server (OHS) för att konfigurera enkel inloggning mellan Azure AD och Oracle IDCS.

OHS fungerar som en omvänd proxy till programnivån, vilket innebär att alla begäranden till slutprogrammen går igenom den. Oracle Access Manager WebGate är en OHS webbserver plugin som fångar varje begäran går till slutet ansökan. Om en resurs som används är skyddad (kräver en autentiserat session) initierar WebGate OIDC-autentiseringsflödet med Identity Cloud Service via användarens webbläsare. Mer information om de flöden som stöds av OpenID Connect WebGate finns i [dokumentationen till Oracle Access Manager](https://docs.oracle.com/en/middleware/idm/access-manager/12.2.1.3/aiaag/integrating-webgate-oidc-server.html).

Med den här inställningen kan en användare som redan är inloggad på Azure AD navigera till JD Edwards EnterpriseOne- eller PeopleSoft-programmet utan att logga in igen, via Oracle Identity Cloud Service. Kunder som distribuerar den här lösningen får fördelarna med enkel inloggning, inklusive en enda uppsättning autentiseringsuppgifter, en förbättrad inloggningsupplevelse, förbättrad säkerhet och minskad supportkostnad.

Mer information om hur du konfigurerar enkel inloggning för JD Edwards EnterpriseOne eller PeopleSoft med Azure AD finns i det associerade [faktablad från Oracle](https://cloud.oracle.com/iaas/whitepapers/deploy_peoplesoft_jdedwards_across_oci_azure.pdf).