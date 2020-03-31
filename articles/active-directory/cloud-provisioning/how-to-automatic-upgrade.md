---
title: 'Azure AD Connect molnetableringsagent: Automatisk uppgradering | Microsoft-dokument'
description: I den här artikeln beskrivs den inbyggda automatiska uppgraderingsfunktionen i Azure AD Connect-molnetableringsagenten.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f09b2fc685881aa8a7bd87b6a855c657af9ef43d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190321"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD Connect molnetableringsagent: Automatisk uppgradering

Det är enkelt att se till att installationen av din Azure Active Directory (Azure Active Directory) Connect-molnetableringsagent alltid är uppdaterad med den automatiska uppgraderingsfunktionen.

Agenten installeras här: "Programfiler\Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe"

Om du vill verifiera din version högerklickar du på den körbara filen och väljer egenskaper och sedan information.

![Agent-filversion](media/how-to-automatic-upgrade/agent1.png)

Agentuppdateringaren installeras här: "Programfiler\Azure AD Connect Provisioning Agent Updater\AzureADConnectAgentUpdater.exe"

Om du vill verifiera din version högerklickar du på den körbara filen och väljer egenskaper och sedan information.

![Agent updater version](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstall-the-agent"></a>Avinstallera agenten
Om du vill ta bort agenten går du till **Avinstallera eller ändrar ett program** och avinstallerar följande:

- **Uppdatering av Microsoft Azure AD Connect-agent**
- **Etableringsagent för Microsoft Azure AD Connect**
- **Etableringsagentpaket för Microsoft Azure AD Connect**

![Borttagning av agent](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect-molnetablering?](what-is-cloud-provisioning.md)

