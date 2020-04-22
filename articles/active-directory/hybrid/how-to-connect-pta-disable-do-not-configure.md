---
title: Inaktivera PTA när du använder Azure AD Connect "Konfigurera inte" | Microsoft-dokument
description: I den här artikeln beskrivs hur du inaktiverar PTA med Azure AD Connect "konfigurera inte"-funktionen.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa1046dc64fed3edb6c9d04f76a96f488769ff42
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726805"
---
# <a name="disable-pta-when-using-azure-ad-connect-do-not-configure"></a>Inaktivera PTA när du använder Azure AD Connect "Konfigurera inte"

Om du använder direktautentisering med Azure AD Connect och du har den inställd på "Konfigurera inte", kan du inaktivera den. Att inaktivera PTA kan göras med hjälp av följande cmdlets. 

## <a name="prerequisites"></a>Krav
Följande förutsättningar krävs:
- Alla Windows-datorer som har PTA-agenten installerad. 
- Agenten måste vara på version 1.5.1742.0 eller senare. 
- Ett globalt Azure-administratörskonto för att köra PowerShell-cmdlets för att inaktivera PTA.

>[!NOTE]
> Om din agent är äldre kanske den inte har de cmdlets som krävs för att slutföra den här åtgärden. Du kan hämta en ny agent från Azure Portal en installera den på valfri Windows-dator och ange administratörsautentiseringsuppgifter. (Installera agenten påverkar inte PTA-status i molnet)

> [!IMPORTANT]
> Om du använder Azure Government-molnet måste du skicka in parametern ENVIRONMENTNAME med följande värde. 
>
>| Namn på miljö | Molnet |
>| - | - |
>| AzureUSGovernment | US Gov|


## <a name="to-disable-pta"></a>Så här inaktiverar du PTA
I en PowerShell-session använder du följande för att inaktivera PTA:
1. PS C:\Program\Microsoft Azure AD Connect autentiseringsagent>`Import-Module .\Modules\PassthroughAuthPSModule`
2. `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth` eller `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth -EnvironmentName <identifier>`
3. `Disable-PassthroughAuthentication  -Feature PassthroughAuth` eller `Disable-PassthroughAuthentication -Feature PassthroughAuth -EnvironmentName <identifier>`

## <a name="if-you-dont-have-access-to-an-agent"></a>Om du inte har tillgång till en agent

Om du inte har en agentdator kan du använda följande kommando för att installera en agent.

1. Ladda ner den senaste Auth Agent från portal.azure.com.
2. Installera funktionen: `.\AADConnectAuthAgentSetup.exe` eller`.\AADConnectAuthAgentSetup.exe ENVIRONMENTNAME=<identifier>`


## <a name="next-steps"></a>Nästa steg

- [Användarinloggning med Azure Active Directory-direktautentisering](how-to-connect-pta.md)