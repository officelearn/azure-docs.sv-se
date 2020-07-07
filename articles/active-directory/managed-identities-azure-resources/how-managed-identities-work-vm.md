---
title: Hur hanterade identiteter för Azure-resurser fungerar med Azure Virtual Machines
description: Beskrivning av hanterade identiteter för Azure-resurser fungerar med Azure Virtual Machines.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.custom: mvc
ms.date: 06/11/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: b61fd2f9bc36743754a43b05629a798f0305d4e5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85609217"
---
# <a name="how-managed-identities-for-azure-resources-work-with-azure-virtual-machines"></a>Hur hanterade identiteter för Azure-resurser fungerar med virtuella Azure-datorer

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i din kod.

I den här artikeln får du lära dig hur hanterade identiteter fungerar med Azure Virtual Machines (VM).


## <a name="how-it-works"></a>Så här fungerar det

Internt är hanterade identiteter tjänstens huvud namn av en särskild typ, som endast kan användas med Azure-resurser. När den hanterade identiteten tas bort tas motsvarande tjänst objekt bort automatiskt.
När en användardefinierad eller systemtilldelad identitet skapas, utfärdar MSRP (Managed Identity Resource Provider) ett certifikat internt till identiteten. 

Din kod kan använda en hanterad identitet för att begära åtkomsttoken för tjänster som stöder Azure AD-autentisering. Azure tar hand om de autentiseringsuppgifter som används av tjänstinstansen. 

Följande diagram visar hur hanterade tjänstidentiteter fungerar med virtuella datorer i Azure (VM):

![Hanterade tjänstidentiteter och virtuella datorer i Azure](media/how-managed-identities-work-vm/data-flow.png)

|  Egenskap    | Systemtilldelad hanterad identitet | Användartilldelad hanterad identitet |
|------|----------------------------------|--------------------------------|
| Skapa |  Skapas som en del av en Azure-resurs (till exempel en virtuell Azure-dator eller Azure App Service). | Skapas som fristående Azure-resurs. |
| Livscykel | Delad livs cykel med den Azure-resurs som den hanterade identiteten skapas med. <br/> När den överordnade resursen tas bort, tas även den hanterade identiteten bort. | Oberoende livs cykel. <br/> Måste tas bort explicit. |
| Dela mellan Azure-resurser | Kan inte delas. <br/> Den kan bara kopplas till en enda Azure-resurs. | Kan delas. <br/> Samma användare-tilldelade hanterade identitet kan associeras med fler än en Azure-resurs. |
| Vanliga användarsituationer | Arbets belastningar som finns i en enda Azure-resurs. <br/> Arbets belastningar för vilka du behöver oberoende identiteter. <br/> Till exempel ett program som körs på en enskild virtuell dator | Arbets belastningar som körs på flera resurser och som kan dela en enda identitet. <br/> Arbets belastningar som behöver förautentisering till en säker resurs som en del av ett etablerings flöde. <br/> Arbets belastningar där resurser återvinns ofta, men behörigheter bör vara konsekventa. <br/> Till exempel en arbets belastning där flera virtuella datorer behöver åtkomst till samma resurs |

## <a name="system-assigned-managed-identity"></a>Systemtilldelad hanterad identitet

1. Azure Resource Manager tar emot en begäran om att aktivera den systemtilldelade hanterade identiteten på en virtuell dator.

2. Azure Resource Manager skapar ett huvudnamn för tjänsten i Azure AD som representerar den virtuella datorns identitet. Tjänstens huvudnamn skapas i den Azure AD-klientorganisation som är betrodd av prenumerationen.

3. Azure Resource Manager konfigurerar identiteten på den virtuella datorn genom att uppdatera Azure Instance Metadata Service Identity-slutpunkten med klient-ID och certifikat för tjänstens huvud namn.

4. När den virtuella datorn har fått en identitet använder du informationen om tjänstens huvudnamn för att ge den virtuella datorn åtkomst till Azure-resurser. Använd rollbaserad åtkomstkontroll (RBAC) i Azure AD när du anropar Azure Resource Manager för att tilldela lämplig roll till tjänstens huvudnamn för den virtuella datorn. Ge din kod åtkomst till den specifika hemligheten eller nyckeln i Key Vault när du anropar Key Vault.

5. Din kod som körs på den virtuella datorn kan begära en token från Azure instance metadata service-slutpunkt, som endast kan nås från den virtuella datorn:`http://169.254.169.254/metadata/identity/oauth2/token`
    - Resursparametern anger vilken tjänst som token ska skickas till. Använd `resource=https://management.azure.com/` för att autentisera mot Azure Resource Manager.
    - API-versionsparametern anger IMDS-versionen, använd api-version=2018-02-01 eller högre.

6. Ett anrop görs till Azure AD för att begära en åtkomsttoken (se steg 5) med klient-ID:t och certifikatet som konfigurerades i steg 3. Azure AD returnerar en åtkomsttoken för JSON Web Token (JWT).

7. Koden skickar åtkomsttoken vid ett anrop till en tjänst som stöder Azure AD-autentisering.

## <a name="user-assigned-managed-identity"></a>Användartilldelad hanterad identitet

1. Azure Resource Manager tar emot en begäran om att skapa en användartilldelad hanterad identitet.

2. Azure Resource Manager skapar ett huvudnamn för tjänsten i Azure AD som representerar den användartilldelade hanterade identiteten. Tjänstens huvudnamn skapas i den Azure AD-klientorganisation som är betrodd av prenumerationen.

3. Azure Resource Manager tar emot en begäran om att konfigurera den tilldelade hanterade identiteten på en virtuell dator och uppdaterar slut punkten för Azure Instance Metadata Service-identiteten med klient-ID och certifikat för den användardefinierade hanterade identitets tjänstens huvud namn.

4. När den användartilldelade hanterade identiteten har skapats använder du informationen om tjänstens huvudnamn för att ge identiteten åtkomst till Azure-resurser. Använd rollbaserad åtkomstkontroll (RBAC) i Azure AD när du anropar Azure Resource Manager för att tilldela lämplig roll till tjänstens huvudnamn för den användartilldelade identiteten. Ge din kod åtkomst till den specifika hemligheten eller nyckeln i Key Vault när du anropar Key Vault.

   > [!Note]
   > Du kan även utföra det här steget före steg 3.

5. Din kod som körs på den virtuella datorn kan begära en token från Azure-Instance Metadata Service identitetens slut punkt, endast tillgänglig från den virtuella datorn:`http://169.254.169.254/metadata/identity/oauth2/token`
    - Resursparametern anger vilken tjänst som token ska skickas till. Använd `resource=https://management.azure.com/` för att autentisera mot Azure Resource Manager.
    - Parametern för klient-ID anger den identitet som token har begärts för. Det här värdet krävs för att lösa tvetydigheter om mer än en användartilldelad identitet finns på samma virtuella dator.
    - Parametern för API-version anger Azure Instance Metadata Service-versionen. Använd `api-version=2018-02-01` eller senare.

6. Ett anrop görs till Azure AD för att begära en åtkomsttoken (se steg 5) med klient-ID:t och certifikatet som konfigurerades i steg 3. Azure AD returnerar en åtkomsttoken för JSON Web Token (JWT).
7. Koden skickar åtkomsttoken vid ett anrop till en tjänst som stöder Azure AD-autentisering.


## <a name="next-steps"></a>Nästa steg

Kom igång med funktionen Hanterade identiteter för Azure-resurser med följande snabbstarter:

* [Använda en systemtilldelad hanterad identitet för en virtuell Windows-dator för åtkomst till Resource Manager](tutorial-windows-vm-access-arm.md)
* [Använda en systemtilldelad hanterad identitet för en virtuell Linux-dator för åtkomst till Resource Manager](tutorial-linux-vm-access-arm.md)
